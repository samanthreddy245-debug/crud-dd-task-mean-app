FROM node:18-alpine

# Create app directory
WORKDIR /app

# Install dependencies
COPY package*.json ./
RUN npm install --only=production

# Copy the rest of the source
COPY . .

# Environment
ENV PORT=8080
# Will be overridden by docker-compose
ENV MONGO_URL=mongodb://localhost:27017/dd_db

EXPOSE 8080

CMD ["node", "server.js"]
