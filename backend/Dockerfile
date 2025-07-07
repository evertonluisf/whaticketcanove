# Stage de build: instala dependências e compila o código
FROM node:18-alpine AS builder

# Instala ferramentas necessárias para compilar dependências nativas
RUN apk add --no-cache python3 make g++ build-base bash

WORKDIR /app

# Copia apenas os arquivos de dependências
COPY package.json ./

# ⚠️ Usando npm install no lugar de npm ci
# Ideal se você NÃO estiver versionando package-lock.json
RUN npm install --production --silent

# Copia o restante do projeto (src, config, etc.)
COPY . .

# Compila o código (caso use TypeScript, Babel etc.)
RUN npm run build

# Stage final: container enxuto para produção
FROM node:18-alpine

# (opcional) bash para debug ou uso de scripts
RUN apk add --no-cache bash

WORKDIR /app

# Copia apenas os arquivos necessários para produção
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/package.json ./

# Define porta que o app usará (usada no EasyPanel)
EXPOSE 3100

# Healthcheck (opcional, mas recomendado)
HEALTHCHECK --interval=30s --timeout=3s \
  CMD wget --quiet --spider http://localhost:3100/health || exit 1

# Inicializa a aplicação
CMD ["node", "dist/server.js"]
