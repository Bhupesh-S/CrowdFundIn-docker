FROM node:20-alpine AS deps
WORKDIR /app
COPY package*.json ./
RUN npm install

FROM deps AS build
COPY tsconfig.json ./
COPY config ./config
COPY src ./src
RUN npm run build

FROM node:20-alpine AS runtime
WORKDIR /app
RUN apk add --no-cache netcat-openbsd
RUN addgroup -S app && adduser -S app -G app
ENV NODE_ENV=local
ENV PORT=3000
COPY --from=build /app/node_modules ./node_modules
COPY --from=build /app/lib ./lib
COPY --from=build /app/config ./config
COPY package.json ./
COPY entrypoint.sh /app/entrypoint.sh
RUN chown -R app:app /app && chmod +x /app/entrypoint.sh
USER app
EXPOSE 3000
ENTRYPOINT ["/app/entrypoint.sh"]
CMD ["node", "lib/index.js"]
