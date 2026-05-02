FROM node:18-alpine AS builder
WORKDIR /app
COPY app.js .

FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app/app.js .
EXPOSE 3000
CMD ["node", "app.js"]