# Stage de build: instala dependências e compila o código
FROM node:18-alpine AS builder

# Instala ferramentas de build, Python e bash (necessários para node-gyp, re2 etc.)
RUN apk add --no-cache python3 make g++ build-base bash

WORKDIR /app

# Copia definição de dependências (inclui package-lock.json se existir)
COPY package.json package-lock.json* ./

# Instala todas as dependências (incluindo devDependencies)
RUN npm ci --silent

# Copia o restante do código-fonte
COPY . .

# Executa o build (por exemplo, gera dist/ para TypeScript)
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
