
Resolução do Erro npm ci --silent no Dockerfile
O log indica que o comando npm ci está falhando porque o projeto não possui um arquivo lock válido ou o package-lock.json está desatualizado em relação ao package.json. Além disso, o Docker está reclamando do uso de ENV para segredos, mas isso não impede o build.

1. Geração e versionamento de package-lock.json
No seu diretório local whaticket/backend, execute:

bash
npm install
Isso criará (ou atualizará) o package-lock.json conforme o seu package.json.

Confirme que lockfileVersion no package-lock.json é ≥ 1 (geralmente gerado pelo npm 8+).

Faça commit e push:

bash
git add backend/package-lock.json
git commit -m "chore: adicionar/atualizar package-lock.json"
git push
Mantenha no Dockerfile o comando:

text
RUN npm ci --silent
Por que?
npm ci exige um lockfile consistente para instalação determinística das dependências.

2. Alternativa: usar npm install em vez de npm ci
Se preferir não versionar o package-lock.json, adapte o Dockerfile para usar npm install:

text
# Stage de build
FROM node:18-alpine AS builder
RUN apk add --no-cache python3 make g++ build-base bash

WORKDIR /app
COPY package.json ./

# Usa npm install em vez de ci
RUN npm install --production --silent

COPY . .

RUN npm run build

# Stage final: container enxuto para produção
FROM node:18-alpine

# Instala apenas bash (opcional)
RUN apk add --no-cache bash

WORKDIR /app

# Copia artefatos do build
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/package.json ./

# Variáveis de ambiente no formato KEY=VALUE
ENV NODE_ENV=production \
    PORT=3100 \
    BACKEND_URL=https://api-whaticket.efettivagestao.com.br \
    FRONTEND_URL=https://whaticket.efettivagestao.com.br \
    DB_HOST=automacao_whaticket-banco \
    DB_PORT=5432 \
    DB_DIALECT=postgres \
    DB_NAME=whaticket-db \
    DB_USER=whaticket_user \
    DB_PASS=whaticket_Senha125 \
    JWT_SECRET=jej98msb89ervrj8bjsr9dfjd \
    JWT_REFRESH_SECRET=kijv89wb4jg89jrgjw589gmiv9j \
    LOG_LEVEL=info \
    MAX_FILE_SIZE=20000000

# Expondo porta para o Easypanel
EXPOSE 3100

# Healthcheck opcional
HEALTHCHECK --interval=30s --timeout=3s \
  CMD wget --quiet --spider http://localhost:3100/health || exit 1

# Comando de inicialização (formato JSON recomendado)
CMD ["node", "dist/server.js"]
