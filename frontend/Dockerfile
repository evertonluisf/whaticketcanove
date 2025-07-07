# Etapa de build
FROM node:14-alpine as build
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Etapa final: servir com o `serve`
FROM node:14-alpine
WORKDIR /app
RUN npm install -g serve
COPY --from=build /app/build /app/build
EXPOSE 3100
CMD ["serve", "-s", "build", "-l", "3000"]
