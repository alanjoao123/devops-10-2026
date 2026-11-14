# Lista de Tarefas — DevOps PUCPR 2026/2

API REST para gerenciamento de tarefas, desenvolvida com **FastAPI** na Extensão de DevOps da PUCPR. O projeto conta com testes automatizados, lint, pipeline de CI/CD no GitHub Actions, imagem Docker publicada no Docker Hub e implantação automática no Render.

- **Imagem Docker:** [alansilva12/tarefas](https://hub.docker.com/r/alansilva12/tarefas)
- **Aplicação no Render:** https://tarefas-api-gwwg.onrender.com
- **Documentação (Swagger):** https://tarefas-api-gwwg.onrender.com/docs

## Rotas

| Método | Rota | Descrição |
|--------|------|-----------|
| GET | `/` | Rota raiz |
| GET | `/autor` | Nome do autor |
| GET | `/tarefas` | Lista as tarefas |
| POST | `/tarefas?titulo=...&descricao=...` | Cria uma tarefa |
| GET | `/tarefas/{id_tarefa}` | Exibe uma tarefa |
| PUT | `/tarefas/{id_tarefa}` | Conclui uma tarefa |
| DELETE | `/tarefas/{id_tarefa}` | Remove uma tarefa |

## Execução local

Pré-requisitos: Python 3.14 e Docker (para o PostgreSQL).

```bash
git clone https://github.com/alanjoao123/devops-10-2026.git
cd devops-10-2026

docker run -d --name banco-tarefas -p 5432:5432 \
  -e POSTGRES_USER=tarefas -e POSTGRES_PASSWORD=tarefas -e POSTGRES_DB=tarefas \
  postgres:16

python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt

export DATABASE_USERNAME=tarefas
export DATABASE_PASSWORD=tarefas
export DATABASE_ADDRESS=localhost
export DATABASE=tarefas

fastapi dev app/api.py
```

Acesse http://localhost:8000/docs.

### Testes e lint

```bash
pylint app
DATABASE_USERNAME=mock DATABASE_PASSWORD=mock DATABASE_ADDRESS=mock DATABASE=mock python -m pytest -v
```

## Pipeline CI/CD

Arquivo `.github/workflows/python-app.yml`, executado em todo push e pull request para a `main`:

1. **build** (CI): instala as dependências, roda o Pylint e os testes com Pytest.
2. **entrega** (CD): constrói a imagem Docker e publica no Docker Hub (`alansilva12/tarefas:2.0`).
3. **deploy**: aciona o Deploy Hook do Render, que baixa a nova imagem e reinicia o serviço.

## Tecnologias utilizadas

- Python 3.14, FastAPI, SQLModel / SQLAlchemy
- PostgreSQL (produção) e SQLite (testes)
- Pytest, Pylint
- Docker e Docker Hub
- GitHub Actions
- Render