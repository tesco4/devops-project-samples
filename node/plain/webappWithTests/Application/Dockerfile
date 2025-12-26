FROM node:18-alpine
WORKDIR /app
COPY . .
RUN npm ci || npm install
EXPOSE 8092
ENV PORT=8092
CMD ["node","server.js"]
