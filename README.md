# Guia de Instalação — OnHive

Este guia descreve como instalar e executar o **OnHive** usando Docker e Docker Compose no seu ambiente.

---

## Pré-requisitos

Antes de começar, certifique-se de ter instalado:

- **Docker** — [Como instalar o Docker](https://docs.docker.com/engine/install/)
- **Docker Compose** — [Como instalar o Docker Compose](https://docs.docker.com/compose/install/)

Para conferir se estão instalados:

```bash
docker --version
docker compose version
```

---

## Estrutura do projeto

A aplicação é composta por três serviços:

| Serviço           | Descrição              | Porta padrão |
|-------------------|------------------------|--------------|
| **mongodb**       | Banco de dados         | 27017        |
| **onhive-backend**| API do backend         | 4000         |
| **onhive-frontend**| Aplicação frontend    | 4200         |

---

## Passo a passo da instalação

### 1. Clonar ou acessar o repositório

```bash
cd /caminho/para/OnHive-Instalation
```

### 2. Configurar variáveis de ambiente

Crie um arquivo `.env` na raiz do projeto a partir do exemplo:

```bash
cp .env_example .env
```

Edite o arquivo `.env` e preencha os valores obrigatórios:

| Variável           | Descrição                          | Exemplo        |
|--------------------|------------------------------------|----------------|
| `MONGODB_HOST`     | Host do banco (geralmente localhost)| `localhost`    |
| `MONGODB_PORT`     | Porta do MongoDB                   | `27017`        |
| `MONGODB_USER`     | Usuário do MongoDB                 | `seu_usuario`  |
| `MONGODB_PASSWORD` | Senha do MongoDB                   | `sua_senha`    |
| `API_PORT`         | Porta da API do backend           | `4000`         |
| `APP_PORT`         | Porta do frontend                 | `4200`         |

**Variáveis opcionais** (já possuem valor padrão no Docker Compose):

- `MongoDbSettings__DataBase` — Nome do banco (padrão: `onhive`)
- `VITE_API_URL` — URL da API usada pelo frontend (padrão: `http://localhost:4000`)
- `VITE_ENV` — Ambiente (padrão: `production`)

**Exemplo de `.env` preenchido:**

```env
MONGODB_HOST=localhost
MONGODB_PORT=27017
MONGODB_USER=admin
MONGODB_PASSWORD=senha_segura
API_PORT=4000
APP_PORT=4200
```

> **Importante:** O arquivo `.env` contém dados sensíveis e não deve ser commitado no Git (já está no `.gitignore`).

### 3. Subir os serviços com Docker Compose

Na pasta do projeto, execute:

```bash
docker compose -f docker-compose.yml up -d
```

- `-d` inicia os containers em segundo plano.

Para ver os logs em tempo real:

```bash
docker compose -f docker-compose.yml logs -f
```

### 4. Verificar se os serviços estão rodando

```bash
docker compose -f docker-compose.yml ps
```

Você deve ver os três containers ativos: `onhive-mongodb`, `onhive-backend` e `onhive-frontend`.

### 5. Acessar a aplicação

- **Frontend:** [http://localhost:4200](http://localhost:4200) (ou a porta definida em `APP_PORT`)
- **Backend (API):** [http://localhost:4000](http://localhost:4000) (ou a porta definida em `API_PORT`)

---

## Comandos úteis

| Ação              | Comando |
|-------------------|--------|
| Parar os serviços | `docker compose -f docker-compose.yml down` |
| Parar e remover volumes | `docker compose -f docker-compose.yml down -v` |
| Ver logs          | `docker compose -f docker-compose.yml logs -f` |
| Reiniciar um serviço | `docker compose -f docker-compose.yml restart <nome_do_servico>` |

---

## Solução de problemas

- **Porta em uso:** Se alguma porta (27017, 4000 ou 4200) já estiver em uso, altere o valor correspondente no `.env` e suba os containers novamente.
- **Erro de conexão com o MongoDB:** Confira usuário, senha e porta no `.env` e se o container `onhive-mongodb` está rodando.
- **Frontend não carrega a API:** Verifique se `VITE_API_URL` no `.env` aponta para a URL correta da API (ex.: `http://localhost:4000`).

---

## Observações

- Os dados do MongoDB são persistidos no volume `mongodb_data`. Ao usar `docker compose down -v`, esse volume é removido e os dados são perdidos.
- As imagens utilizadas são: `mongo:latest`, `onhive/onhive-backend-api:latest` e `onhive/onhive-frontend-app:latest`. É necessário ter acesso a essas imagens (Docker Hub ou registro interno).
