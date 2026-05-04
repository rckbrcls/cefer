# Database Guide

PostgreSQL is the source of truth for CEFER Management. The backend is intentionally database-first: schema, views, indexes, functions, triggers, and route queries are stored as SQL assets under `server/sql/`.

## Main Assets

| Path | Purpose |
| --- | --- |
| `server/sql/upgrade_schema.sql` | Creates the relational schema |
| `server/sql/downgrade_schema.sql` | Drops schema objects |
| `server/sql/downgrades/` | Deletes data by table/domain |
| `server/sql/functions/` | PL/pgSQL functions and triggers |
| `server/sql/indexes.sql` | Explicit indexes |
| `server/sql/views.sql` | SQL views used by the API and reports |
| `server/sql/queries/` | Query files used by Flask routes |

## Domain Model Overview

The schema includes these main areas:

| Area | Tables |
| --- | --- |
| People and identity | `PESSOA`, `INTERNO_USP`, `FUNCIONARIO`, `FUNCIONARIO_ATRIBUICAO`, `FUNCIONARIO_RESTRICAO`, `EDUCADOR_FISICO` |
| Facilities and equipment | `INSTALACAO`, `EQUIPAMENTO`, `DOACAO`, `EMPRESTIMO_EQUIPAMENTO` |
| Reservations | `RESERVA`, `RESERVA_EQUIPAMENTO` |
| Activities | `ATIVIDADE`, `OCORRENCIA_SEMANAL`, `CONDUZ_ATIVIDADE`, `PARTICIPACAO_ATIVIDADE` |
| Events | `EVENTO`, `SUPERVISAO_EVENTO` |
| Extension groups | `GRUPO_EXTENSAO`, `ATIVIDADE_GRUPO_EXTENSAO` |
| External invitations | `CONVITE_EXTERNO` |
| Authentication and auditing | `USUARIO_SENHA`, `AUDITORIA_LOGIN`, `METRICA_ACESSO_DIARIA`, `SOLICITACAO_CADASTRO` |

The schema also defines the `DIA_SEMANA` enum for weekly activity occurrences.

## Important Relationships

- `INTERNO_USP`, `FUNCIONARIO`, and `EDUCADOR_FISICO` specialize `PESSOA`.
- `FUNCIONARIO_ATRIBUICAO` stores staff/admin assignment labels.
- `RESERVA` links installations to an internal responsible user.
- `RESERVA_EQUIPAMENTO` links equipment to an internal responsible user.
- `ATIVIDADE` is scheduled through `OCORRENCIA_SEMANAL`.
- `CONDUZ_ATIVIDADE` links educators to activities.
- `PARTICIPACAO_ATIVIDADE` links participants to activities and can reference an internal inviter.
- `CONVITE_EXTERNO` stores external invitation tokens and invite status.
- `USUARIO_SENHA` stores application login credentials and user type metadata.
- `SOLICITACAO_CADASTRO` stores registration requests and optional admin approval data.

## Views

`server/sql/views.sql` defines API/reporting views:

- `vw_reservas_completas`
- `vw_atividades_completas`
- `vw_equipamentos_disponiveis`
- `vw_instalacoes_ocupacao`
- `vw_reservas_equipamentos_completas`

The Flask views API under `/views` reads query files from `server/sql/queries/views/`.

## Functions And Triggers

Function files:

- `auth_functions.sql`: password hashing/verification, user roles, registration request approval/rejection, internal authentication, external token authentication, invite accept/reject, unblock user.
- `admin_functions.sql`: user, installation, equipment, activity, and administration helpers.
- `internal_functions.sql`: internal reservations, availability, activity listing, and group/equipment helpers.
- `staff_functions.sql`: staff activity and reporting helpers.
- `common_triggers.sql`: reservation time validation, educator formation validation, and user type synchronization triggers.
- `downgrade_functions.sql` and `downgrade_triggers.sql`: cleanup support.

The bootstrap order applies `auth_functions.sql` before other function files because auth helpers such as password hashing are prerequisites for generated credentials.

## Indexes

`server/sql/indexes.sql` adds indexes for:

- Reservation lookups by installation, responsible user, and date.
- Activity participation lookups by activity and participant.
- Equipment reservation lookups by equipment, responsible user, and date.
- Weekly occurrence lookups by activity, installation, and weekday.
- Event-to-reservation lookups.
- Equipment-to-installation lookups.
- Person email and internal USP number lookups.
- External invite token and email lookups.
- Registration request status lookups.

## Query Organization

Route queries are grouped by domain:

- `server/sql/queries/auth/`
- `server/sql/queries/admin/`
- `server/sql/queries/internal/`
- `server/sql/queries/staff/`
- `server/sql/queries/external/`
- `server/sql/queries/extension_group/`
- `server/sql/queries/reports/`
- `server/sql/queries/views/`
- `server/sql/queries/meta/`

Python route code loads these files through `server/app/services/database/executor.py`.

## Bootstrap And Population

Bootstrap and population code:

- `server/app/services/database/bootstrap.py`
- `server/data_generators/populate.py`
- `server/data_generators/data_generator.py`
- `server/docker/entrypoint.py`
- `server/scripts/populate_db.sh`

The bootstrap path applies the schema, functions, triggers, indexes, views, and sample data. The Docker entrypoint can run this automatically when `POPULATE_DB=true`.

## Downgrade And Cleanup

Cleanup support includes:

- `server/scripts/downgrade_db.sh`
- `server/data_generators/downgrade.py`
- `server/sql/downgrades/`
- `server/sql/downgrade_schema.sql`

Any schema changes should be reviewed together with the downgrade assets and seed generators.

## Change Precautions

- Keep table and column names aligned with SQL queries under `server/sql/queries/`.
- Update frontend hooks and API consumers when response shapes change.
- Update data generators when required columns, constraints, or relationships change.
- Review indexes when adding high-volume filtering or joining patterns.
- Review auth functions and triggers when changing role, user type, or invite behavior.
