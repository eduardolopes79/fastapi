# fastapi_zero

API REST em **FastAPI** com CRUD de usuários, criada como projeto de estudo e base para algo maior.

> 🚧 **Projeto em fase inicial.** O que está listado em [Funcionalidades atuais](#funcionalidades-atuais) já funciona; o restante está no [Roadmap](#roadmap) e ainda será construído. A API pode mudar bastante entre versões.

## Sumário

- [Estado atual](#estado-atual)
- [Funcionalidades atuais](#funcionalidades-atuais)
- [Tecnologias](#tecnologias)
- [Como rodar](#como-rodar)
- [Endpoints](#endpoints)
- [Testes e qualidade de código](#testes-e-qualidade-de-código)
- [Estrutura do projeto](#estrutura-do-projeto)
- [Limitações conhecidas](#limitações-conhecidas)
- [Roadmap](#roadmap)
- [Contribuindo](#contribuindo)

## Estado atual

| Área                       | Status          |
| -------------------------- | --------------- |
| CRUD de usuários           | ✅ Funcionando  |
| Validação de dados         | ✅ Funcionando  |
| Testes automatizados       | ✅ Básico       |
| Lint e formatação          | ✅ Configurado  |
| Banco de dados             | 🔜 Planejado    |
| Autenticação               | 🔜 Planejado    |
| Docker / CI / Deploy       | 🔜 Planejado    |

## Funcionalidades atuais

- Rota de boas-vindas (`GET /`).
- Criar, listar, atualizar e remover usuários.
- Validação de entrada com Pydantic (inclusive formato de e-mail).
- A senha **nunca é devolvida** nas respostas: o schema público (`UserPublic`) expõe só `id`, `username` e `email`.
- Documentação interativa gerada automaticamente (Swagger UI e ReDoc).

## Tecnologias

- [Python](https://www.python.org/) 3.14+
- [FastAPI](https://fastapi.tiangolo.com/) (`fastapi[standard]`)
- [Pydantic](https://docs.pydantic.dev/)
- [Poetry](https://python-poetry.org/) para dependências
- [pytest](https://docs.pytest.org/) + pytest-cov para testes
- [Ruff](https://docs.astral.sh/ruff/) para lint e formatação
- [Taskipy](https://github.com/taskipy/taskipy) para atalhos de comandos

## Como rodar

### Pré-requisitos

- Python 3.14 ou superior
- Poetry

### Instalação

```bash
git clone <url-do-repositorio>
cd fastapi_zero
poetry install
```

### Executando o servidor

```bash
poetry run task run
```

Isso executa `fastapi dev fastapi_zero/app.py`. A API fica em <http://127.0.0.1:8000>, com recarga automática ao salvar arquivos.

Documentação interativa:

- Swagger UI: <http://127.0.0.1:8000/docs>
- ReDoc: <http://127.0.0.1:8000/redoc>

## Endpoints

| Método   | Rota               | Descrição                | Sucesso | Erros |
| -------- | ------------------ | ------------------------ | ------- | ----- |
| `GET`    | `/`                | Mensagem de boas-vindas  | 200     | —     |
| `POST`   | `/users/`          | Cria um usuário          | 201     | 422   |
| `GET`    | `/users/`          | Lista os usuários        | 200     | —     |
| `PUT`    | `/users/{user_id}` | Atualiza um usuário      | 200     | 404, 422 |
| `DELETE` | `/users/{user_id}` | Remove um usuário        | 200     | 404   |

### Exemplo

```bash
curl -X POST http://127.0.0.1:8000/users/ \
  -H "Content-Type: application/json" \
  -d '{"username": "alice", "email": "alice@example.com", "password": "secret"}'
```

Resposta (`201 Created`):

```json
{
  "username": "alice",
  "email": "alice@example.com",
  "id": 1
}
```

## Testes e qualidade de código

Os comandos abaixo são atalhos definidos no `pyproject.toml` via Taskipy:

| Comando                    | O que faz                                              |
| -------------------------- | ------------------------------------------------------ |
| `poetry run task lint`     | Verifica o código com `ruff check`                     |
| `poetry run task format`   | Corrige problemas (`ruff check --fix`) e formata o código |
| `poetry run task test`     | Roda o lint, depois o pytest com cobertura             |

Ao final dos testes, o relatório de cobertura em HTML é gerado em `htmlcov/`.

## Estrutura do projeto

```
fastapi_zero/
├── fastapi_zero/
│   ├── app.py        # Aplicação e rotas
│   └── schema.py     # Schemas Pydantic (entrada, saída e modelo interno)
├── tests/
│   ├── conftest.py   # Fixtures (cliente de teste)
│   └── test_app.py   # Testes das rotas
├── pyproject.toml    # Dependências e configuração (Poetry, Ruff, pytest, Taskipy)
└── poetry.lock
```

## Limitações conhecidas

Por estar no começo, algumas coisas são propositalmente simples e **não servem para produção**:

- **Sem persistência:** os usuários ficam numa lista em memória e somem quando o servidor reinicia.
- **Senha em texto puro:** ela não é devolvida pela API, mas é guardada sem hash na memória.
- **Sem autenticação:** qualquer pessoa pode listar, editar e apagar qualquer usuário.
- **IDs frágeis:** o ID é calculado pela posição na lista, então remover um usuário desloca os IDs dos seguintes.
- **Sem unicidade:** é possível cadastrar `username` e `email` repetidos.
- **Testes dependentes de ordem:** os testes compartilham o mesmo estado em memória e precisam rodar na sequência atual.

Todos esses pontos estão cobertos no roadmap abaixo.

## Roadmap

As etapas abaixo são a direção do projeto, mais ou menos em ordem de prioridade. Podem mudar conforme o desenvolvimento avança.

### 1. Base de dados

- [ ] Integrar um banco de dados relacional com **SQLAlchemy**
- [ ] Migrações com **Alembic**
- [ ] Configuração por variáveis de ambiente (`.env` com `pydantic-settings`)
- [ ] Garantir unicidade de `username` e `email` (retornando `409 Conflict`)
- [ ] IDs gerados pelo banco, em vez da posição na lista

### 2. Segurança e autenticação

- [ ] Hash de senha (por exemplo, Argon2 ou bcrypt)
- [ ] Login com **JWT** (`/token`)
- [ ] Proteger rotas: cada usuário só altera e remove a própria conta
- [ ] Refresh de token

### 3. API

- [ ] Paginação e filtros na listagem de usuários
- [ ] Busca de usuário por ID (`GET /users/{user_id}`)
- [ ] Rotas assíncronas (`async def`) junto com o banco
- [ ] Versionamento da API (`/api/v1`)
- [ ] Novos recursos além de usuários (a definir)

### 4. Testes e qualidade

- [ ] Banco isolado por teste (cada teste começa do zero)
- [ ] Factories para dados de teste
- [ ] Testes de erro (404, 409, 422) e de autenticação
- [ ] Meta de cobertura mínima

### 5. Infraestrutura

- [ ] `Dockerfile` e `docker-compose` (API + banco)
- [ ] Integração contínua com GitHub Actions (lint + testes)
- [ ] Deploy em nuvem
- [ ] Logs estruturados e tratamento de erros padronizado

## Contribuindo

O projeto ainda está tomando forma, então sugestões e ideias são bem-vindas. Abra uma *issue* para conversar antes de enviar um *pull request*.

## Autor

**Eduardo**

<!-- Adicione aqui a licença do projeto, caso decida usar uma (ex.: MIT). -->
