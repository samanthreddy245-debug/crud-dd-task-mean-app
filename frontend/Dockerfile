# Stage 1: Build Angular app
FROM node:18-alpine AS build

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .

# Production build
RUN npm run build -- --configuration production

# Stage 2: Serve with Nginx
FROM nginx:alpine

# Copy built app to nginx html folder
COPY --from=build /app/dist/angular-15-crud /usr/share/nginx/html

# Default Nginx serves /usr/share/nginx/html on port 80
EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
