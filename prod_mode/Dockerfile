# Stage 1: Build
FROM node:24-alpine AS builder
RUN apk add --no-cache openssl
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Stage 2: Run
FROM node:24-alpine
RUN apk add --no-cache openssl
WORKDIR /app
# Add crypto support
RUN apk add --no-cache openssl
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY package*.json ./

EXPOSE 3000
CMD ["npm", "run", "start:prod", "--", "--host", "0.0.0.0"]