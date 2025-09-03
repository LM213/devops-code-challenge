# Use Node 16 as base image
FROM node:16

# Set working directory
WORKDIR /app

# Copy package files and install dependencies
COPY package*.json ./
RUN npm ci

# Copy the rest of the backend code
COPY . .

# Expose backend port
EXPOSE 8080

# Start backend
CMD ["npm", "start"]

