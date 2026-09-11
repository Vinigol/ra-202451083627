# HANDOUT — AULA 02

## Dissecando o HTTP

*6 requisições sob o microscópio — Arquitetura de Aplicações Web*

## 🎯 MISSÃO

Vocês interceptaram 6 conversas entre um app e a API de uma biblioteca. Para CADA card:

- Descrevam o que o cliente pediu (verbo + recurso na URI)
- Expliquem o que o status code da resposta informa
- Respondam: repetindo a MESMA requisição 3 vezes seguidas, o estado do servidor muda?

Ao final, preencham juntos a TABELA-SÍNTESE dos verbos na última página.

*⏱️ Tempo: 30 minutos  |  👥 Formato: em duplas  |  Dica: o card 6 esconde uma pegadinha de quem é a culpa.*

> **Nomes:** ____________________   **Turma:** ____________________   **Data:** ___ / ___ / ______

## REQUISIÇÃO 01 — A prateleira inteira

```text
→ REQUISIÇÃO
GET /api/livros HTTP/1.1
Host: biblioteca.newton.br
Accept: application/json
```

```text
← RESPOSTA
HTTP/1.1 200 OK
Content-Type: application/json

[ { "id": 1, "titulo": "Clean Code", "autor": "Robert C. Martin" },
  { "id": 7, "titulo": "O Programador Pragmático", "autor": "Hunt & Thomas" } ]
```

**Sua análise:**

1. O que o cliente pediu (verbo + recurso)?
o cliente fez uma requisição Get ao servidor que deverá retornar uma pág web (biblioteca.newton.br)

2. O que o status code informa? Deu certo? Culpa de quem se não deu?
Sim, status code 200, é requisição concluida com sucesso.

3. Repetindo esta requisição 3 vezes seguidas, o estado do servidor muda? E a resposta?
O servidor vai retornar o mesmo Json e o status code será o 200,já que a requisição é a mesma.

## REQUISIÇÃO 02 — O livro fantasma

```text
→ REQUISIÇÃO
GET /api/livros/99 HTTP/1.1
Host: biblioteca.newton.br
Accept: application/json
```

```text
← RESPOSTA
HTTP/1.1 404 Not Found
Content-Type: application/problem+json

{ "title": "Not Found", "status": 404 }
```

**Sua análise:**

1. O que o cliente pediu (verbo + recurso)?
GET /api/livros/99- requisição cliente servidor no qual, o servidor retorna uma pagina web.

2. O que o status code informa? Deu certo? Culpa de quem se não deu?
o Status code está corretor pois , o servidor não encontrou o Id 9.

3. Repetindo esta requisição 3 vezes seguidas, o estado do servidor muda? E a resposta?
persistindo a mesma solicitação o status code permanece o mesmo,enquanto o recurso não existir no sistema.

## REQUISIÇÃO 03 — Livro novo na estante

```text
→ REQUISIÇÃO
POST /api/livros HTTP/1.1
Host: biblioteca.newton.br
Content-Type: application/json

{ "titulo": "Domain-Driven Design", "autor": "Eric Evans" }
```

```text
← RESPOSTA
HTTP/1.1 201 Created
Location: /api/livros/8
Content-Type: application/json

{ "id": 8, "titulo": "Domain-Driven Design", "autor": "Eric Evans" }
```

**Sua análise:**

1. O que o cliente pediu (verbo + recurso)?
POST /api/livros
o cliente está solicitando que o servidor faça um Post api livros, na url biblioteca.newton.br

2. O que o status code informa? Deu certo? Culpa de quem se não deu?
Status code 201, significa que a requisição foi atendida, ou seja foi postado o livro no sistema.

3. Enviando este POST 3 vezes seguidas, o que acontece na estante? Para que serve o header Location?
o servidor irá realizar 3 post, criando 3 id diferente, para o mesmo livro.

## REQUISIÇÃO 04 — Corrigindo a ficha completa

```text
→ REQUISIÇÃO
PUT /api/livros/7 HTTP/1.1
Host: biblioteca.newton.br
Content-Type: application/json

{ "id": 7, "titulo": "O Programador Pragmático", "autor": "D. Hunt; D. Thomas" }
```

```text
← RESPOSTA
HTTP/1.1 200 OK
Content-Type: application/json

{ "id": 7, "titulo": "O Programador Pragmático", "autor": "D. Hunt; D. Thomas" }
```

**Sua análise:**

1. O que o cliente pediu (verbo + recurso)?
PUT /api/livros/7: atualize o livro id 7
o cliente fez uma requisição que seja, atualizado os dados de um livro.

2. O que o status code informa? Deu certo? Culpa de quem se não deu?
Status cod 200, confirma que a atualização foi realizada com sucesso

3. Repetindo esta requisição 3 vezes seguidas, o estado do servidor muda? E a resposta?
vai retornar o mesmo status code,o servidor vai atualizar o mesmo id 7.
## REQUISIÇÃO 05 — Fora do catálogo

```text
→ REQUISIÇÃO
DELETE /api/livros/7 HTTP/1.1
Host: biblioteca.newton.br
```

```text
← RESPOSTA
HTTP/1.1 204 No Content
```

**Sua análise:**

1. O que o cliente pediu (verbo + recurso)?
DELETE /api/livros/7 
2. O que o status code informa? Deu certo? Culpa de quem se não deu?
Status 204 code indica que a requisição foi realizada com sucesso, porem não exite corpo de resposta.

3. Repetindo o DELETE, o estado do servidor muda? Que resposta você ESPERA na segunda vez?
repetindo 3 vezes a mesma requisição vai acontecer o seguinte, a primeira vai ser concluida com sucesso, após isso o servidor não vai encontrar nada, já que o recurso já foi deletado, então ele irá retornar o status code 404 not  found.

## REQUISIÇÃO 06 — O cadastro capenga

```text
→ REQUISIÇÃO
POST /api/livros HTTP/1.1
Host: biblioteca.newton.br
Content-Type: application/json

{ "autor": "Anônimo" }
```

```text
← RESPOSTA
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json

{ "title": "Bad Request", "status": 400,
  "errors": { "Titulo": [ "O campo Titulo é obrigatório" ] } }
```

**Sua análise:**

1. O que o cliente pediu (verbo + recurso)?
POST /api/livros
2. O que o status code informa? Deu certo? Culpa de quem se não deu?
O status code retornou 400, requisição invalida pois faltou um campo obrigatório.

3. Repetindo esta requisição 3 vezes seguidas, o estado do servidor muda? E a resposta?
Persiste o mesmo status code,requisição invalida.
## TABELA-SÍNTESE — Os verbos do HTTP

*Preencham com base nos 6 cards. “Seguro” = não altera nada no servidor. “Idempotente” = repetir N vezes deixa o servidor no mesmo estado que 1 vez.*

| **Verbo** | **Para que serve** | **Seguro?** | **Idempotente?** | **Status típicos** |
| --- | --- | --- | --- | --- |
| **`GET`** | buscar | sim | sim |200  |
| **`POST`** | criar |sim  | não | 201 |
| **`PUT`** |  atualizar|sim  |  sim|  200/204|
| **`PATCH`** | atualizar parcialemente | sim |  sim|  200/204|
| **`DELETE`** | remover recurso | sim | sim | 204 |

## DESAFIO

1. O verbo PATCH não apareceu em nenhum card. Qual a diferença entre PATCH e PUT? Um app de banco quer alterar SÓ o apelido do usuário, entre dezenas de campos do perfil — qual dos dois você usaria e por quê?

Patch atualiza os dados parcialmente
put atualiza os dados de forma geral.
