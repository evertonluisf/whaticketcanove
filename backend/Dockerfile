# Etapa de build
FROM node:18-alpine AS builder

# Instala dependências nativas necessárias para compilar alguns pacotes Node
RUN apk add --no-cache python3 make g++ build-base bash

WORKDIR /app

# Copia apenas arquivos de dependências
COPY package.json package-lock.json* ./

# Instala TODAS as dependências (incluindo devDependencies como typescript)
RUN npm install --silent

# Copia o restante da aplicação
COPY . .

# Compila o TypeScript
RUN npm run build

# Etapa final: imagem leve para produção
FROM node:18-alpine

# ⚠️ Instala Chromium e dependências do Puppeteer
RUN apk add --no-cache \
  chromium \
  nss \
  freetype \
  harfbuzz \
  ca-certificates \
  ttf-freefont \
  bash

# Variáveis de ambiente exigidas pelo Puppeteer
ENV PUPPETEER_EXECUTABLE_PATH=/usr/bin/chromium-browser
ENV PUPPETEER_SKIP_CHROMIUM_DOWNLOAD=true
ENV NODE_OPTIONS="--no-sandbox"

WORKDIR /app

# Copia apenas arquivos necessários do build
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/package.json ./

# Expõe a porta correta para o Easypanel
EXPOSE 3100

# Healthcheck (opcional)
HEALTHCHECK --interval=30s --timeout=3s \
  CMD wget --quiet --spider http://localhost:3100/health || exit 1

# Inicializa o servidor
CMD ["node", "dist/server.js"]
