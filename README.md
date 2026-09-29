# Previsão da Qualidade da Água via Sensoriamento Remoto

Projeto acadêmico em equipe que combina imagens de satélite e aprendizado de máquina para apoiar a análise da qualidade da água em reservatórios. O sistema reúne uma API em Django REST Framework, processamento de imagens Sentinel-2 e armazenamento de dados e modelos em PostgreSQL.

> Este repositório público é uma cópia para portfólio do projeto da equipe. Arquivos de credenciais e o histórico original não fazem parte desta cópia.

## O que o sistema faz

- Gerencia usuários, reservatórios, parâmetros e modelos de aprendizado de máquina por meio de uma API REST.
- Recebe solicitações de análise para uma área e um intervalo de datas.
- Consulta imagens Sentinel-2 SR Harmonized no Google Earth Engine e prepara imagens para processamento.
- Calcula atributos espectrais e índices como NDVI, NDCI, FAI e MNDWI para identificar pixels de água e gerar previsões.
- Processa imagens em blocos, com suporte a execução paralela.
- Disponibiliza documentação interativa da API com Swagger e ReDoc.

## Tecnologias

| Área | Tecnologias |
| --- | --- |
| API e aplicação | Python 3.10, Django 5.1, Django REST Framework |
| Banco de dados | PostgreSQL |
| Sensoriamento remoto | Google Earth Engine, Sentinel-2 |
| Processamento e machine learning | NumPy, Pandas, Rasterio, scikit-learn, joblib |
| Execução local | Docker e Docker Compose |

## Como executar localmente

### Pré-requisitos

- Docker Engine e Docker Compose.
- Uma conta de serviço do Google Cloud com acesso ao Google Earth Engine, para executar o fluxo de imagens de satélite.

### Configurar o ambiente

1. Entre na pasta da aplicação:

   ```bash
   cd Codigo
   ```

2. Crie o arquivo `.env` nesta pasta. O Compose espera estas variáveis:

   ```dotenv
   DEBUG=True
   SECRET_KEY=<gere-uma-chave-segura-para-desenvolvimento>
   DB_NAME=water-quality-db
   DB_USER=postgres
   DB_PASSWORD=<defina-uma-senha-local>
   DB_HOST=db
   DB_PORT=5432
   GOOGLE_APPLICATION_CREDENTIALS=/app/credentials/<arquivo-da-conta-de-servico>.json
   ```

   Coloque o JSON da conta de serviço em `Codigo/processing/credentials/`, que é montada no contêiner como `/app/credentials`. Configure o caminho correspondente em `GOOGLE_APPLICATION_CREDENTIALS`.

   **Não use credenciais reais no README, no código ou em commits.** O `.env` e os arquivos de conta de serviço são locais e não devem ser publicados. Use uma chave secreta e senhas próprias; `DEBUG=True` é apenas para desenvolvimento.

### Iniciar

Com o `.env` e as credenciais configurados, ainda em `Codigo/`, execute:

```bash
docker compose up --build
```

O contêiner da aplicação aplica as migrações, inicia o agendador de tarefas e sobe o servidor Django na porta `8000`. O PostgreSQL fica disponível na porta `5433` do computador.

- API: <http://localhost:8000/api/>
- Swagger: <http://localhost:8000/api/docs/>
- ReDoc: <http://localhost:8000/api/redoc/>
- Esquema OpenAPI: <http://localhost:8000/schema/>
- Administração Django: <http://localhost:8000/admin/>

Para criar um usuário administrador:

```bash
docker compose exec web python manage.py createsuperuser
```

Para parar os contêineres, use `Ctrl+C` e depois:

```bash
docker compose down
```

## API

As rotas da aplicação ficam sob `/api/`. A documentação Swagger lista os métodos, parâmetros e formatos de resposta disponíveis.

| Recurso | Rota |
| --- | --- |
| Usuários | `/api/users/` |
| Reservatórios | `/api/reservoirs/` |
| Parâmetros | `/api/parameters/` |
| Parâmetros associados a modelos | `/api/analysis-parameters/` |
| Modelos de machine learning | `/api/machine-learning-models/` |
| Solicitações de análise | `/api/analysis-request/` |
| Obter token JWT | `/api/login/` |
| Atualizar token JWT | `/api/login/refresh/` |

As rotas protegidas exigem autenticação JWT. Envie o token de acesso no cabeçalho `Authorization: Bearer <token>`.

## Organização do repositório

```text
.
├── Artefatos/       # Espaço para artefatos acadêmicos do projeto
├── Codigo/
│   ├── api/         # Modelos, serializers, rotas e comandos Django
│   ├── processing/  # Coleta e processamento de imagens e previsões
│   ├── benchmark/   # Scripts, modelos e resultados de benchmark
│   └── water_quality_project/  # Configuração do projeto Django
├── Documentacao/    # Documentação complementar
├── CITATION.cff     # Metadados de citação
└── LICENSE
```

## Equipe

- Andrei Gonçalves Rohlfs Massaini
- Beatriz Fulgêncio C. Menezes
- Fabio Franco de Azevedo
- Lucas Abreu Lopes
- Luis Henrique Dusanek Guedes

**Professores responsáveis:** Henrique Cota de Freitas e João Paulo Coelho Furtado.

## Licença e citação

Consulte [`LICENSE`](LICENSE) para os termos de uso e [`CITATION.cff`](CITATION.cff) para os metadados de citação. Ao reutilizar o projeto, preserve os créditos da equipe.
