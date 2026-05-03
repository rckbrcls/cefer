# db_project

> **Status:** Archived
> This academic project is kept for coursework reference, historical context, and portfolio review.

Projeto acadêmico - Sistema de gerenciamento de reservas e atividades para instalações esportivas.

**Disciplina**: SCC0641 – Laboratório de Bases de Dados

## Summary

- Archived academic full-stack project for managing reservations, activities, users, staff, resources, reports, and sports-facility workflows.
- Solves the SCC0641 database-lab assignment by combining a PostgreSQL relational model, Flask backend, Next.js frontend, Docker Compose orchestration, and synthetic data generation.
- Main stack: PostgreSQL 17, Flask, psycopg2, Flask-Cors, Next.js 16, React 19, TypeScript, Tailwind CSS, TanStack Query/Table, Zustand, Recharts, and Docker Compose.
- Current status: archived coursework reference; useful for portfolio review and database-first project explanation, not an actively maintained product.
- Technical value: demonstrates SQL schema/scripts, generated sample data, role-based UI surfaces, and containerized full-stack local setup.

## Overview

Aplicação full-stack para gerenciamento de reservas e atividades em instalações esportivas, desenvolvida com:

- **Backend**: Flask (Python) - API REST
- **Frontend**: Next.js 16 (React 19 + TypeScript)
- **Banco de Dados**: PostgreSQL 17
- **Orquestração**: Docker Compose

Este projeto foi desenvolvido como parte do Projeto Final (PF) da disciplina SCC0641 – Laboratório de Bases de Dados.

**Autores**:

- Breno Rodrigues - 11734142
- Erick Barcelos - 11345562
- Gabriel Henrique dos Santos - 13783972
- Lourençco Roselino - 11796805
- Nelson Luiz - 9793502

## Tech Stack

- PostgreSQL 17 for the relational model, SQL functions, views, and generated data.
- Flask with psycopg2 and Flask-Cors for the REST API.
- Next.js 16, React 19, TypeScript, Tailwind CSS, TanStack Query/Table, Zustand, and Recharts for the frontend.
- Docker Compose for the full local stack.
- Shell scripts under `server/scripts/` for database population and downgrade flows.

## Getting Started

### Requirements

- Docker e Docker Compose instalados
- Node.js 20+ e pnpm (para desenvolvimento local do frontend)
- Python 3.12+ (para desenvolvimento local do backend)

### Running Locally

Este método sobe toda a aplicação (PostgreSQL, Flask e Next.js) em containers Docker.

**1. Configurar variáveis de ambiente**

Crie um arquivo `.env` na raiz do projeto com as seguintes variáveis:

```env
# PostgreSQL
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres
POSTGRES_DB=public
POSTGRES_PORT=5432

# Database Connection (Flask)
DB_HOST=postgres
DB_PORT=5432
DB_NAME=public
DB_USER=postgres
DB_PASSWORD=postgres
DB_SCHEMA=              # Opcional: schema específico do PostgreSQL

# Flask
FLASK_SECRET_KEY=your-secret-key-here
FLASK_DEBUG=true
FLASK_RUN_PORT=5050     # Porta interna do Flask dentro do container
FLASK_RUN_HOST=0.0.0.0
FLASK_PORT=5050         # Porta de mapeamento Docker (host:container)

# CORS (opcional - necessário apenas para desenvolvimento local)
CORS_ORIGINS=http://localhost:3000  # Origens permitidas separadas por vírgula

# Next.js
NEXT_PUBLIC_API_URL=http://localhost:5050
NODE_ENV=development
NEXTJS_PORT=3000

# População automática do banco (opcional)
POPULATE_DB=true
```

**2. Subir os serviços**

```bash
docker compose up -d
```

Isso irá:

- Iniciar o PostgreSQL 17
- Construir e iniciar a aplicação Flask
- Construir e iniciar a aplicação Next.js
- Popular o banco de dados automaticamente (se `POPULATE_DB=true`)

**3. Acessar as aplicações**

- **Frontend**: <http://localhost:3000>
- **Backend API**: <http://localhost:5050>
- **PostgreSQL**: localhost:5432

**4. Ver logs**

```bash
# Todos os serviços
docker compose logs -f

# Apenas um serviço
docker compose logs -f flask_app
docker compose logs -f nextjs_app
docker compose logs -f postgres
```

**5. Parar os serviços**

```bash
docker compose down
```

**Backend (Flask)**

1. **Instalar dependências Python**

```bash
cd server
python3 -m venv venv
source venv/bin/activate  # No Windows: venv\Scripts\activate
pip install -r requirements.txt
```

2. **Configurar variáveis de ambiente**

Certifique-se de que o arquivo `.env` está configurado corretamente (veja Método 1).

**Importante**: Para desenvolvimento local, configure `CORS_ORIGINS` no `.env` com a URL do frontend (ex: `CORS_ORIGINS=http://localhost:3000`). No Docker, isso não é necessário pois o CORS é configurado automaticamente.

3. **Rodar o servidor Flask**

```bash
# Com o PostgreSQL rodando (via Docker Compose ou localmente)
flask run --host=0.0.0.0 --port=5050 --reload
```

**Frontend (Next.js)**

1. **Instalar dependências**

```bash
cd client
pnpm install
```

2. **Configurar variáveis de ambiente**

Certifique-se de que `NEXT_PUBLIC_API_URL` está configurado no `.env` apontando para a URL do backend Flask.

3. **Rodar o servidor de desenvolvimento**

```bash
pnpm dev
```

O frontend estará disponível em <http://localhost:3000>

## Usage

**Popular o banco de dados**

**Opção 1: Automática (Docker)**

Se `POPULATE_DB=true` no `.env`, o banco será populado automaticamente ao iniciar o container Flask.

**Opção 2: Manual**

```bash
# Dentro do container ou ambiente Python ativado
cd server
./scripts/populate_db.sh
```

Este script:

- Aplica as migrações de schema
- Popula todas as tabelas com dados sintéticos

**Reverter/limpar o banco**

```bash
cd server
./scripts/downgrade_db.sh
```

Isso executará os scripts de downgrade na ordem inversa, limpando todas as tabelas e o schema.

**Acessar o PostgreSQL via psql**

```bash
# Via Docker
docker exec -it postgres17 bash
su postgres
psql

# Ou diretamente
docker exec -it postgres17 psql -U postgres -d public
```

Consulte o arquivo `LOGINS.md` para informações sobre usuários de teste e senhas padrão.

**Resumo rápido:**

- **Senha padrão**: `senha123` (para todos os usuários internos)
- **Admin**: `admin@usp.br` / `senha123`
- **Funcionário**: `funcionario@usp.br` / `senha123`
- **Interno**: `interno@usp.br` / `senha123`

**Backend**

- `./scripts/populate_db.sh` - Popula o banco de dados
- `./scripts/downgrade_db.sh` - Reverte/limpa o banco de dados

**Frontend**

- `pnpm dev` - Inicia servidor de desenvolvimento
- `pnpm build` - Build de produção
- `pnpm start` - Inicia servidor de produção
- `pnpm lint` - Executa o linter

## Project Structure

```text
db_project/
├── client/          # Frontend Next.js
│   └── Dockerfile.dev  # Dockerfile para desenvolvimento do Next.js
├── server/          # Backend Flask
│   ├── app/         # Aplicação Flask (rotas, serviços)
│   ├── data_generators/  # Geradores de dados sintéticos
│   ├── docker/      # Configurações Docker do Flask
│   │   └── Dockerfile  # Dockerfile para o Flask
│   └── sql/         # Scripts SQL (migrações, views, funções)
├── docs/            # Documentação do projeto
└── docker-compose.yml
```

Additional documentation:

- [`relatorio.md`](relatorio.md) - Relatório técnico completo do projeto (Projeto Final - Bases de Dados)
- [`LOGINS.md`](LOGINS.md) - Informações sobre logins de teste
- `docs/` - Documentação técnica e entregas

## Architecture

**Estrutura do Backend**

- `app/routes/` - Rotas da API REST
- `app/services/` - Lógica de negócio
- `app/database.py` - Configuração do banco de dados
- `sql/upgrade_schema.sql` - Schema principal
- `sql/downgrades/` - Scripts de downgrade
- `sql/functions/` - Funções SQL
- `sql/views.sql` - Views do banco

**Estrutura do Frontend**

- `app/` - Rotas e páginas (App Router do Next.js)
- `components/` - Componentes React
- `hooks/` - Custom hooks
- `lib/` - Utilitários e configurações

## Current Status

This project is archived as a coursework and portfolio reference. The Docker Compose flow, SQL scripts, Flask API, and Next.js frontend remain documented for study and local reproduction.

## Known Limitations

**Erro de conexão com o banco**

- Verifique se o PostgreSQL está rodando: `docker compose ps`
- Verifique as variáveis de ambiente no `.env`
- Verifique os logs: `docker compose logs postgres`

**Erro ao popular o banco**

- Verifique se o PostgreSQL está saudável: `docker compose ps`
- Verifique os logs do Flask: `docker compose logs flask_app`
- Tente popular manualmente: `./scripts/populate_db.sh`

**Porta já em uso**

- Altere as portas no arquivo `.env` (ex: `FLASK_PORT=5051`, `FLASK_RUN_PORT=5051`, `NEXTJS_PORT=3001`)
- Ou pare o processo que está usando a porta

**Erro de CORS no desenvolvimento local**

- Certifique-se de que `CORS_ORIGINS` está configurado no `.env` com a URL do frontend (ex: `CORS_ORIGINS=http://localhost:3000`)
- Verifique se `NEXT_PUBLIC_API_URL` está apontando para a URL correta do backend
