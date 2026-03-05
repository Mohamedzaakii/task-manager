FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
COPY nest-cli.json tsconfig.json ./
RUN npm ci 
COPY src/ ./src/
COPY test/ ./test/
RUN npm run build 


FROM node:20-alpine AS production
WORKDIR /app
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001
COPY package*.json ./
RUN npm ci --only=production && \
    npm cache clean --force
COPY --from=builder --chown=nodejs:nodejs /app/dist ./dist
USER nodejs
EXPOSE 3001
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD curl -f http://localhost:3001/health || exit 1
CMD ["node", "dist/main"]

