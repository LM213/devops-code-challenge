# Use Node 16 to build frontend
FROM node:16 as build

WORKDIR /app
COPY package.json package-lock.json ./
RUN npm install
COPY . .
RUN npm run build

# Use Nginx to serve static frontend
FROM nginx:alpine
COPY --from=build /app/build /usr/share/nginx/html

# Expose frontend port
EXPOSE 80

# Start Nginx
CMD ["nginx", "-g", "daemon off;"]

