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

### 2. Configuração do Backend

#### 2.1. Navegue até a pasta do backend

```bash
cd residencial-digital-backend
```

#### 2.2. Instale as dependências

```bash
npm install
```

#### 2.3. Configure as variáveis de ambiente


Crie um arquivo `.env` na raiz da pasta `residencial-digital-backend`:

```bash
# Crie o arquivo .env
touch .env  # Linux/Mac
# ou
New-Item .env  # Windows PowerShell
```

Adicione as seguintes variáveis ao arquivo `.env`:

```env
# Database
DATABASE_URL="postgresql://postgres:postgres@postgres:5432/segurodb"

# JWT Secret (troque por uma chave segura)
JWT_SECRET="sua_chave_secreta_muito_segura_aqui"

# Node Environment
NODE_ENV="development"

# API Allianz (opcional - para integração com seguros)
ALLIANZ_API_KEY="sua_api_key_allianz"
ALLIANZ_API_URL="https://api.allianz.com"
```

⚠️ **Importante**: Nunca compartilhe suas chaves secretas! O arquivo `.env` já está no `.gitignore`.

### 3. Configuração do Frontend

#### 3.1. Navegue até a pasta do frontend

```bash
cd ../residencial-digital-frontend
```

#### 3.2. Instale as dependências

```bash
npm install
```

#### 3.3. Configure as variáveis de ambiente

Crie um arquivo `.env.local` na raiz da pasta `residencial-digital-frontend`:

```bash
# Crie o arquivo .env.local
touch .env.local  # Linux/Mac
# ou
New-Item .env.local  # Windows PowerShell
```

Adicione as seguintes variáveis ao arquivo `.env.local`:

```env
# URL da API Backend
NEXT_PUBLIC_API_URL=http://localhost:3000/v1

# Ambiente
NODE_ENV=development
```

### 4. Subindo o Projeto com Docker

#### 4.1. Volte para a raiz do projeto

```bash
cd ..
```

#### 4.2. Inicie os containers

```bash
docker-compose up -d
```

Este comando irá:
- Criar e iniciar o banco de dados PostgreSQL
- Criar e iniciar o backend (Node.js/Express) na porta 3000
- Criar e iniciar o frontend (Next.js) na porta 3001

#### 4.3. Verifique se os containers estão rodando

```bash
docker ps
```

Você deve ver 3 containers ativos:
- `residencial-postgres`
- `residencial-backend`
- `residencial-frontend`

### 5. Configuração do Banco de Dados

#### 5.1. Execute as migrations

```bash
cd residencial-digital-backend
docker exec -it residencial-backend npx prisma migrate deploy
```

#### 5.2. Crie um usuário administrador

```bash
docker exec -it residencial-backend npm run create-admin
```

Ou use o endpoint da API para criar manualmente:

```bash
POST http://localhost:3000/v1/users
Content-Type: application/json

{
  "name": "admin",
  "email": "admin@example.com",
  "password": "admin123",
  "role": "admin"
}
```

## 🌐 Acessando a Aplicação

Após seguir todos os passos:

- **Frontend**: http://localhost:3001
- **Backend API**: http://localhost:3000/v1

### Credenciais de Acesso (após criar o admin)

- **Email**: admin@test.com
- **Senha**: admin123

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

# Ver logs do backend
docker logs -f residencial-backend

# Ver logs do frontend
docker logs -f residencial-frontend

# Reiniciar um container específico
docker restart residencial-backend

# Acessar o terminal de um container
docker exec -it residencial-backend sh
```

### Backend

```bash
# Entrar na pasta do backend
cd residencial-digital-backend

# Executar migrations
npx prisma migrate dev

# Gerar Prisma Client
npx prisma generate

# Ver banco de dados (Prisma Studio)
npx prisma studio

# Rodar em desenvolvimento (fora do Docker)
npm run dev
```

### Frontend

```bash
# Entrar na pasta do frontend
cd residencial-digital-frontend

# Rodar em desenvolvimento (fora do Docker)
npm run dev

# Build para produção
npm run build

# Rodar em produção
npm start
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

Se as portas 3000 ou 3001 já estiverem em uso, edite o `docker-compose.yml`:

```yaml
# Exemplo: mudar porta do backend de 3000 para 3002
ports:
  - "3002:3000"
```

### Problema: Dependências desatualizadas

```bash
# Backend
cd residencial-digital-backend
npm install

# Frontend
cd residencial-digital-frontend
npm install
```

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

## 🤝 Contribuindo

1. Faça um Fork do projeto
2. Crie uma branch para sua feature (`git checkout -b feature/MinhaFeature`)
3. Commit suas mudanças (`git commit -m 'Adiciona MinhaFeature'`)
4. Push para a branch (`git push origin feature/MinhaFeature`)
5. Abra um Pull Request

## 📄 Licença

Este projeto está sob a licença MIT.

## 👨‍💻 Autor

**Yuri Demétrio Ferreira**
- GitHub: [@yuriferreira24hub](https://github.com/yuriferreira24hub)

