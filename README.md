# E-commerce — Projeto 3

API de pedidos com InversifyJS, PostgreSQL e Docker Compose.

## Como rodar

Requisito: Docker Desktop instalado e rodando.

```bash
cp .env.example .env
docker compose up --build
```

A API sobe em `http://localhost:3000` quando aparecer `Server rodando` no log.

O Compose sobe dois containers:

- `banco`: PostgreSQL 16, sem porta publicada. Os dados ficam no volume `dados-banco`.
- `api`: a aplicação. Ela só sobe quando o banco passa no `healthcheck`, e ao iniciar cria as tabelas e cadastra os produtos.

Para parar: `docker compose down`. Os pedidos continuam salvos no volume.

## Modos de execução

O modo é definido pela variável `APP_ENV` no arquivo `.env`.

| | `APP_ENV=dev` | `APP_ENV=test` |
|---|---|---|
| Pedidos | gravados no PostgreSQL | guardados em memória |
| E-mail | enviado pelo Ethereal (precisa de internet) | impresso no log da `api` |

Para trocar de modo, altere o `.env` e rode `docker compose up --build` de novo.

## Como testar

Criar um pedido (produto `1` = livro físico, R$ 100 + R$ 10 de frete; produto `2` = e-book, R$ 50):

```bash
curl -X POST http://localhost:3000/orders \
-H "Content-Type: application/json" \
-d '{
  "customer": "cliente@ethereal.email",
  "items": [{ "productId": 1, "quantity": 1 }],
  "paymentMethod": "pix"
}'
```

Buscar o pedido pelo `id` devolvido:

```bash
curl -i http://localhost:3000/orders/1
```

Um `id` que não existe devolve `404`:

```bash
curl -i http://localhost:3000/orders/999
```