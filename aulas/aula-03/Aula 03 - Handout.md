# HANDOUT — AULA 03

## Consultoria de Design: a API da EscolaTech

*Identifique os anti-padrões e proponha o redesenho — Arquitetura de Aplicações Web*

## 🎯 MISSÃO

A EscolaTech contratou a consultoria de vocês para auditar a API do sistema escolar. Todos os endpoints abaixo FUNCIONAM e estão em produção — mas o time novo se recusa a mexer neles. Para CADA endpoint:

- Identifiquem o(s) problema(s) de design (pode haver mais de um!)
- Proponham o redesenho: método HTTP + rota + status codes corretos

*⏱️ Tempo: 25 minutos  |  👥 Formato: em duplas  |  Dica: se a rota conta o que faz em português, algo está errado.*

> **Nomes:** ___Vinicius Augusto_________________   **Turma:** ____________________   **Data:** __20_ / _08__ / __2026____

## ENDPOINT 01 — POST /api/getAlunos

**Documentação atual (extraída da wiki da EscolaTech):**

```text
POST /api/getAlunos
Retorna TODOS os alunos cadastrados (hoje: 12.482 registros).
Resposta: 200 OK + array JSON completo (~9 MB).
Obs. da wiki: "usar POST porque GET não estava funcionando".
```

1. Qual(is) problema(s) de design vocês identificam?
A requisição foi feita de forma incorreta
Não se deve utilizar verbo pra fazer requisições ,devemos utilizar metodos http
O metodo http de busca de dados é o Get.
Não foi usado a técnica de páginação, em que se dividi os dados em partes menores,chamadas de paginas,facilitando o processamento no servidor quanto a navegação do usúario.

2. Seu redesenho (método + rota + status codes):
GET/api/Alunos
Status code 200 ok

## ENDPOINT 02 — GET /deletarAluno?id=7

**Documentação atual (extraída da wiki da EscolaTech):**

```text
GET /deletarAluno?id=7
Remove o aluno do banco de dados.
Resposta: 200 OK + "OK" (mesmo se o aluno não existir).
Obs. da wiki: "dá pra deletar pelo navegador, bem prático".
```

1. Qual(is) problema(s) de design vocês identificam?
o Metodo Get é utilizado apenas pra leitura e consultas de dados
o Metodo correto pra remoção ou deletar é o Delete
Status code incorreto, nesse caso o status code que retornária seria 405 Method Not Allowed (indicando que o método usado não é permitido).

2. Seu redesenho (método + rota + status codes):
Delete/Usuarios/7
Status code 200

## ENDPOINT 03 — POST /api/alunos (criação)

**Documentação atual (extraída da wiki da EscolaTech):**

```text
POST /api/alunos
Body: { "nome": "...", "curso": "..." }
Cria o aluno e responde: 200 OK + body "OK".
O app precisa buscar a lista inteira de novo para descobrir o ID gerado.
```

1. Qual(is) problema(s) de design vocês identificam?
Nesse caso em especifico o endepoint está correto, o problema está na resposta que o servidor dá, ou seja status code incorreto

2. Seu redesenho (método + rota + status codes):
Status code:201(created)
location: api/alunos/123
Body:{"id":123,"nome:"...","curso":".."}

## ENDPOINT 04 — GET /escolas/1/turmas/3/alunos/25/matriculas/88/disciplinas/12

**Documentação atual (extraída da wiki da EscolaTech):**

```text
GET /escolas/1/turmas/3/alunos/25/matriculas/88/disciplinas/12
Retorna os dados da disciplina 12 da matrícula 88.
Para montar a URL o app precisa conhecer 5 IDs diferentes.
Resposta: 200 OK + JSON da disciplina.
```

1. Qual(is) problema(s) de design vocês identificam?
como o app precisa de apenas 5 ids pra montar a consulta, esse endepoint ficou grande, ou seja está fugindo das boas praticas do Rest.

2. Seu redesenho (método + rota + status codes):
Get/disciplinas/12
Isso reduz a complexidade e evita que o cliente precise conhecer IDs que não são relevantes para a consulta.

## ENDPOINT 05 — GET /api/alunos/7/matriculas (erro)

**Documentação atual (extraída da wiki da EscolaTech):**

```text
GET /api/alunos/7/matriculas
Se o aluno 7 não existe, responde:
200 OK + "<html><b>Erro: aluno nao existe!</b></html>"
O app mobile quebra tentando fazer parse do JSON.
```

1. Qual(is) problema(s) de design vocês identificam?
o servidor retorna status code 200,mesmo quando há erro.
A resposta que o servidor retorna é html,quebrando o app mobile que espera uma resposta json

2. Seu redesenho (método + rota + status codes)
Status code 404 not found "erro" "aluno não encontrado"
## ENDPOINT 06 — PUT /api/atualizarNotaParcial?aluno=7&disc=12&nota=8.5

**Documentação atual (extraída da wiki da EscolaTech):**

```text
PUT /api/atualizarNotaParcial?aluno=7&disc=12&nota=8.5
Atualiza SÓ a nota parcial da disciplina, sem body.
Todos os dados vão na query string.
Resposta: 200 OK + "OK".
```

1. Qual(is) problema(s) de design vocês identificam?
Nesse caso, o verbo http put atualiza um recurso completamente, enquanto se necessita atualiza apenas uma nota.

2. Seu redesenho (método + rota + status codes):
Patch/api/alunos/7/disciplinas/12/notasParcial
Status code:200 ok

## DESAFIO

1. A EscolaTech quer lançar mudanças na API sem quebrar o app mobile antigo, que não recebe atualização há 2 anos. Que decisão de design — que falta na API INTEIRA — resolve esse problema? Como ficariam as rotas?
Formato antigo do endepoint
GET /api/v1/alunos/7/matriculas

Formato antigo das rotas
GET /api/v1/alunos/7/matriculas
POST /api/v1/alunos
PUT /api/v1/alunos/7/notas

Atualização do Endepoint
Get/api/v2/alunos/7/matriculas

Novas rotas
GET /api/v2/alunos/7/matriculas
POST /api/v2/alunos
PATCH /api/v2/alunos/7/disciplinas/12/notaParcial
DELETE /api/v2/alunos/7

