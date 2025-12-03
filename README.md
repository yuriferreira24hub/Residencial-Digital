# Residencial Digital

Sistema completo de seguros residenciais com integração à API da Allianz, desenvolvido com Node.js/Express (backend) e Next.js (frontend).

## 📋 Pré-requisitos

Antes de começar, certifique-se de ter instalado em sua máquina:

- [Git](https://git-scm.com/downloads)
- [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- [Node.js](https://nodejs.org/) (versão 18 ou superior)
- [npm](https://www.npmjs.com/) ou [yarn](https://yarnpkg.com/)

## 🚀 Instalação e Configuração

### 1. Clone o Repositório

```bash
git clone https://github.com/yuriferreira24hub/Residencial-Digital.git
cd Residencial-Digital
```

### 2. Subindo o Projeto com Docker

⚠️ **Nota**: As variáveis de ambiente já estão configuradas no `docker-compose.yml`. Você não precisa criar arquivos `.env` manualmente para rodar com Docker.

#### 2.1. Inicie os containers

```bash
docker-compose up -d
```

Este comando irá:
- Criar e iniciar o banco de dados PostgreSQL na porta 5432
- Criar e iniciar o PgAdmin na porta 8080
- Criar e iniciar o backend (Node.js/Express) na porta 3000
- Executar as migrations automaticamente
- Criar automaticamente um usuário admin
- Criar e iniciar o frontend (Next.js) na porta 3001

#### 2.2. Verifique se os containers estão rodando

```bash
docker ps
```

Você deve ver 4 containers ativos:
- `residencial-db` - PostgreSQL
- `residencial-pgadmin` - PgAdmin (interface web do banco)
- `residencial-backend` - API Backend
- `residencial-frontend` - Interface Frontend

#### 2.3. Aguarde a inicialização

O processo completo leva cerca de 30-60 segundos. Você pode acompanhar os logs:

```bash
# Ver logs do backend
docker logs -f residencial-backend

# Ver logs do frontend
docker logs -f residencial-frontend
```

## 🌐 Acessando a Aplicação

Após seguir todos os passos:

- **Frontend**: http://localhost:3001
- **Backend API**: http://localhost:3000/v1
- **PgAdmin** (Gerenciador de Banco): http://localhost:8080

### Credenciais de Acesso

#### Aplicação (Frontend/Backend)
- **Email**: admin@example.com
- **Senha**: admin123

#### PgAdmin (Gerenciador de Banco)
- **Email**: admin@admin.com
- **Senha**: admin

Para conectar ao banco no PgAdmin:
- **Host**: postgres (ou host.docker.internal se estiver fora do Docker)
- **Port**: 5432
- **Database**: segurodb
- **Username**: postgres
- **Password**: postgres

## 📁 Estrutura do Projeto

```
Residencial-Digital/
├── docker-compose.yml                 # Configuração Docker
├── README.md                          # Este arquivo
│
├── residencial-digital-backend/       # Backend Node.js/Express
│   ├── src/
│   │   ├── controllers/              # Controladores das rotas
│   │   ├── services/                 # Lógica de negócio
│   │   ├── repositories/             # Acesso ao banco de dados
│   │   ├── routes/                   # Definição de rotas
│   │   ├── middlewares/              # Middlewares (auth, validação)
│   │   ├── dtos/                     # Validação com Zod
│   │   └── utils/                    # Utilitários
│   ├── prisma/
│   │   └── schema.prisma             # Schema do banco de dados
│   ├── package.json
│   └── .env                          # Variáveis de ambiente
│
└── residencial-digital-frontend/      # Frontend Next.js
    ├── app/
    │   ├── login/                    # Página de login
    │   ├── home/                     # Dashboard
    │   ├── properties/               # Gestão de propriedades
    │   ├── quotes/                   # Cotações
    │   └── components/               # Componentes reutilizáveis
    ├── public/                       # Arquivos estáticos
    ├── package.json
    └── .env.local                    # Variáveis de ambiente
```

## 🛠️ Comandos Úteis

### Docker

```bash
# Iniciar os containers
docker-compose up -d

# Parar os containers
docker-compose down

# Ver logs em tempo real
docker logs -f residencial-backend
docker logs -f residencial-frontend

# Ver últimas 50 linhas dos logs
docker logs --tail 50 residencial-backend

# Reiniciar um container específico
docker restart residencial-backend

# Reconstruir e reiniciar containers
docker-compose up -d --build

# Reconstruir sem cache (resolve problemas de dependências)
docker-compose build --no-cache
docker-compose up -d

# Acessar o terminal de um container
docker exec -it residencial-backend sh

# Listar todos os containers (incluindo parados)
docker ps -a

# Remover containers, volumes e redes
docker-compose down -v
```

### Backend

```bash
# Entrar na pasta do backend
cd residencial-digital-backend

# Rodar migrations dentro do container
docker exec -it residencial-backend npx prisma migrate dev

# Gerar Prisma Client
docker exec -it residencial-backend npx prisma generate

# Ver banco de dados (Prisma Studio)
docker exec -it residencial-backend npx prisma studio

# Criar usuário admin manualmente
curl -X POST http://localhost:3000/v1/users \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Administrador",
    "email": "admin@example.com",
    "password": "admin123",
    "role": "admin"
  }'

# Rodar em desenvolvimento (FORA do Docker - requer configuração manual)
npm install
npm run dev
```

### Frontend

```bash
# Entrar na pasta do frontend
cd residencial-digital-frontend

# Ver logs em tempo real
docker logs -f residencial-frontend

# Rodar em desenvolvimento (FORA do Docker - requer configuração manual)
npm install
npm run dev

# Build para produção
npm run build

# Rodar em produção
npm start
```

### Banco de Dados

```bash
# Acessar PostgreSQL via linha de comando
docker exec -it residencial-db psql -U postgres -d segurodb

# Fazer backup do banco
docker exec -t residencial-db pg_dump -U postgres segurodb > backup.sql

# Restaurar backup
docker exec -i residencial-db psql -U postgres segurodb < backup.sql

# Limpar dados do banco (cuidado!)
docker exec -it residencial-db psql -U postgres -d segurodb -c "TRUNCATE TABLE \"User\", \"Property\", \"Quote\", \"Policy\" CASCADE;"
```

## 🔧 Troubleshooting

### Problema: Container não inicia

```bash
# Verifique os logs
docker logs residencial-backend
docker logs residencial-frontend

# Reconstrua as imagens
docker-compose up -d --build
```

### Problema: Erro de conexão com o banco

1. Verifique se o container do PostgreSQL está rodando:
```bash
docker ps | grep postgres
```

2. Verifique a variável `DATABASE_URL` no `.env`

3. Tente reiniciar os containers:
```bash
docker-compose restart
```

### Problema: Porta já em uso

Se as portas 3000, 3001, 5432 ou 8080 já estiverem em uso, edite o `docker-compose.yml`:

```yaml
# Exemplo: mudar porta do backend de 3000 para 3002
ports:
  - "3002:3000"
```

### Problema: Erro com bcrypt (invalid ELF header)

Esse erro ocorre quando o `node_modules` foi instalado no Windows e está sendo usado no Docker (Linux). Solução:

```bash
# Reconstruir as imagens sem cache
docker-compose build --no-cache
docker-compose up -d
```

O arquivo `.dockerignore` já está configurado para evitar esse problema.

### Problema: Erro 401 (Unauthorized) no frontend

Isso é **normal** quando você não está autenticado. O `AuthGuard` verifica automaticamente e redireciona para a página de login. Certifique-se de:

1. O backend está rodando (`docker logs residencial-backend`)
2. As variáveis de ambiente estão corretas no `docker-compose.yml`
3. A variável `FRONTEND_URL` está definida como `http://localhost:3001`

## 📝 Endpoints Principais da API

### Autenticação
- `POST /v1/auth/login` - Login
- `GET /v1/auth/check` - Verificar autenticação
- `POST /v1/auth/logout` - Logout

### Usuários
- `POST /v1/users` - Criar usuário
- `GET /v1/users` - Listar usuários
- `GET /v1/users/:id` - Buscar usuário
- `PUT /v1/users/:id` - Atualizar usuário
- `DELETE /v1/users/:id` - Deletar usuário

### Propriedades
- `POST /v1/properties` - Criar propriedade
- `GET /v1/properties` - Listar propriedades
- `GET /v1/properties/:id` - Buscar propriedade
- `PUT /v1/properties/:id` - Atualizar propriedade
- `DELETE /v1/properties/:id` - Deletar propriedade

### Cotações
- `POST /v1/quotes` - Criar cotação
- `GET /v1/quotes` - Listar cotações
- `GET /v1/quotes/:id` - Buscar cotação
- `PUT /v1/quotes/:id/approve` - Aprovar cotação
- `PUT /v1/quotes/:id/reject` - Rejeitar cotação

### Apólices
- `POST /v1/policies` - Criar apólice
- `GET /v1/policies` - Listar apólices
- `GET /v1/policies/:id` - Buscar apólice

## 🔒 Segurança

### Autenticação

O sistema utiliza autenticação baseada em **cookies HttpOnly**, que oferece maior segurança:

- ✅ Cookies não acessíveis via JavaScript (proteção contra XSS)
- ✅ Cookies com flag `SameSite` (proteção contra CSRF)
- ✅ Cookies com flag `Secure` em produção (apenas HTTPS)
- ✅ JWT armazenado apenas no servidor via cookies

### Variáveis de Ambiente Sensíveis

⚠️ **IMPORTANTE**: Antes de colocar em produção, altere as seguintes variáveis no `docker-compose.yml`:

```yaml
JWT_SECRET: "gere_uma_chave_segura_aleatoria_aqui"
POSTGRES_PASSWORD: "senha_forte_do_banco"
PGADMIN_DEFAULT_PASSWORD: "senha_forte_pgadmin"
```

Para gerar uma chave JWT segura:
```bash
# Node.js
node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"

# OpenSSL
openssl rand -hex 32
```

## 👨‍💻 Autor

**Yuri Demétrio Ferreira**

- GitHub: [@yuriferreira24hub](https://github.com/yuriferreira24hub)