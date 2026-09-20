# HANDOUT — AULA 07

## Caça às Vulnerabilidades

*Revisão de segurança de uma API .NET — Arquitetura de Aplicações Web*

## 🎯 MISSÃO

Vocês são a dupla de revisores de segurança da empresa. Os 4 trechos abaixo são da MESMA API, prestes a ir para produção. Para CADA card:

- Descrevam a falha com as próprias palavras (não precisa do nome técnico ainda)
- Estimem o dano possível se isso chegar à produção
- Proponham a correção

*⏱️ Tempo: 30 minutos  |  👥 Formato: em duplas  |  Todo o código é fictício e roda apenas no laboratório.*

> **Nomes:** ____________________   **Turma:** ____________________   **Data:** ___ / ___ / ______

## VULNERABILIDADE 01 — A busca de clientes

> `GET /api/clientes/buscar?nome=...`

Endpoint de busca usado pela tela de atendimento. O parâmetro nome vem direto da caixa de busca do site.

```text
 1  [HttpGet("buscar")]
 2  public IActionResult Buscar(string nome)
 3  {
 4      var sql = "SELECT * FROM Clientes WHERE Nome = '"
 5                + nome + "'";
 6      var clientes = _db.Clientes.FromSqlRaw(sql).ToList();
 7      return Ok(clientes);
 8  }
```

**Sua análise:**

1. Qual é a falha? O erro está em como a consulta Sql é montada,abre espaço para o sql injection.
A consulta Sql abre espaço pra crimes ciberneticos, pois com a query: var sql = "SELECT * FROM Clientes WHERE Nome = '"
 + nome + "'";. A concnação abre espaço pra substituição de nome or 1=1; todos registros da tabela retornados.
2. Qual o dano possível em produção?
Exposição de dados sensiveis,alteração ou exclusão de dados,acesso não autorizado a dados privados.
3. Como corrigir?

public IActionResult Buscar(string nome)
{
    var clientes = _db.Clientes
                      .Where(c => c.Nome == nome)
                      .ToList();
    return Ok(clientes);
}

## VULNERABILIDADE 02 — A consulta de faturas

> `GET /api/faturas/{id}`

Endpoint usado pelo app para exibir a fatura do cartão. O usuário está autenticado quando chama esta rota.

```text
 1  [HttpGet("{id}")]
 2  public IActionResult GetFatura(int id)
 3  {
 4      var fatura = _db.Faturas.Find(id);
 5      if (fatura == null) return NotFound();
 6      return Ok(fatura);
 7  }
```

**Sua análise:**

1. Qual é a falha?
O endpoint expõe diretamente uma entidade Fatura pelo seu id sem qualquer tipo de controle de acesso/autorização.

2. Qual o dano possível em produção?
exposição de dados sem controle de acesso ou seja se o cliente  chama Get/faturas/{id} poderá obter dados
de varias faturas.
3. Como corrigir?
if (fatura == null || fatura.UsuarioId != usuarioLogadoId)
    return NotFound();

## VULNERABILIDADE 03 — A configuração do servidor

> `Program.cs (roda igual em dev e em produção)`

Trecho de inicialização da API, idêntico em todos os ambientes. Este arquivo está versionado no Git da empresa.

```text
 1  public const string Conn =
 2      "Server=prod-db;Database=Banco;User=sa;" +
 3      "Password=Newton@2026!";
 4
 5  var app = WebApplication.CreateBuilder(args).Build();
 6  app.UseDeveloperExceptionPage();
 7  app.Run();
```

**Sua análise:**

1. Qual é a falha?
Exposição de usuario e senha dentro do código fonte.
2. Qual o dano possível em produção?
 exposição de senha e usuário,acesso não autorizado

3. Como corrigir?
var builder = WebApplication.CreateBuilder(args);
var connectionString = builder.Configuration.GetConnectionString("DefaultConnection");

## VULNERABILIDADE 04 — A atualização de perfil

> `PUT /api/usuarios/{id}`

Endpoint que o app chama quando o usuário edita o próprio perfil. O corpo da requisição é o JSON enviado pelo cliente.

```text
 1  public class UsuarioUpdate
 2  {
 3      public string Nome  { get; set; }
 4      public string Email { get; set; }
 5      public string Role  { get; set; }   // "user" | "admin"
 6  }
 7
 8  [HttpPut("{id}")]
 9  public IActionResult Atualizar(int id, UsuarioUpdate dto)
10  {
11      _repo.AtualizarTudo(id, dto);
12      return NoContent();
13  }
```

**Sua análise:**

1. Qual é a falha?
Atualização sem validação,falta de validação de entrada,permite que qualquer cliente envie admin
2. Qual o dano possível em produção?
sobreescrita de dados validos,atualização parcial incorreta,falhas silenciosas.
3. Como corrigir?
public class UsuarioUpdate
{
    [Required]
    public string Nome { get; set; }

    [Required, EmailAddress]
    public string Email { get; set; }

    [Required]
    [RegularExpression("^(user|admin)$")]
    public string Role { get; set; }
}

## DESAFIO

1. Qual das 4 falhas um scanner automático de código teria MAIS dificuldade de encontrar? Por quê?

Vulnerabilidade 02-consuta de faturas,Falta de contexto de regra de negócio: Um scanner de código consegue analisar facilmente padrões sintáticos e fluxo de dados como em SQL Injection, onde ele percebe a entrada de texto não tratada chegando à consulta.