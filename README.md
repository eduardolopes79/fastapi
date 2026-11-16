# FastAPI

API REST em FastAPI para gerenciamento de tarefas, com cadastro de usuários.

> 🚧 **Projeto em fase inicial.** Por enquanto só existe o CRUD de usuários. O restante está no [Roadmap](#roadmap) e a API ainda vai mudar bastante.

## O que já funciona

- Criar, listar, atualizar e remover usuários.
- Validação dos dados com Pydantic, incluindo formato de e-mail.
- A senha não é devolvida nas respostas.
- Documentação interativa gerada automaticamente (Swagger UI e ReDoc).

## Tecnologias

Python 3.14+, FastAPI, Pydantic, Poetry, pytest, Ruff e Taskipy. Veja no roadmap o que ainda vai entrar (SQLAlchemy, Alembic, PostgreSQL, Docker e outros).

## Como rodar

Requisitos: Python 3.14+ e [Poetry](https://python-poetry.org/).

```bash
poetry install
poetry run task run
```

A API sobe em <http://127.0.0.1:8000>. A documentação fica em `/docs` (Swagger) e `/redoc`.

## Endpoints

| Método   | Rota               | Descrição           | Sucesso | Erros    |
| -------- | ------------------ | ------------------- | ------- | -------- |
| `GET`    | `/`                | Boas-vindas         | 200     | —        |
| `POST`   | `/users/`          | Cria usuário        | 201     | 422      |
| `GET`    | `/users/`          | Lista usuários      | 200     | —        |
| `PUT`    | `/users/{user_id}` | Atualiza usuário    | 200     | 404, 422 |
| `DELETE` | `/users/{user_id}` | Remove usuário      | 200     | 404      |

Exemplo:

```bash
curl -X POST http://127.0.0.1:8000/users/ \
  -H "Content-Type: application/json" \
  -d '{"username": "alice", "email": "alice@example.com", "password": "secret"}'
```

## Testes e qualidade de código

```bash
poetry run task test     # lint + pytest com cobertura
poetry run task lint     # ruff check
poetry run task format   # ruff check --fix + ruff format
```

## Limitações atuais

- Os usuários ficam em uma lista em memória e somem quando o servidor reinicia.
- A senha é guardada sem hash.
- Não há autenticação: qualquer pessoa pode editar ou apagar qualquer usuário.
- O ID é a posição na lista, então apagar um usuário muda o ID dos seguintes.
- `username` e `email` podem se repetir.

## Roadmap

**Dados**
- [ ] Banco de dados com SQLAlchemy
- [ ] Migrações com Alembic
- [ ] Impedir `username` e `email` duplicados

**Autenticação**
- [ ] Hash de senha
- [ ] Login com JWT
- [ ] Cada usuário só altera e apaga a própria conta
- [ ] Autenticação mais robusta (por exemplo, renovação de token)

**Tarefas**
- [ ] CRUD de tarefas ligado ao usuário logado
- [ ] Filtros e paginação na listagem

**Arquitetura**
- [ ] Reorganizar o projeto em módulos e routers
- [ ] Tornar a aplicação assíncrona (FastAPI e SQLAlchemy)

**Infraestrutura**
- [ ] Docker e PostgreSQL
- [ ] Testes automatizados na integração contínua (GitHub Actions)
- [ ] Deploy no Fly.io

