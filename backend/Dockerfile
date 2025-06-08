# Stage 1
FROM node:21 AS backend-builder

# Set the working directory
WORKDIR /app

# Copy package.json and package-lock.json first
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy the rest of the application code
COPY . .

# Run tests (optional)
#RUN npm run test

# Stage 2
FROM node:21-slim

# Set the working directory
WORKDIR /app

# Copy the application code from backend-builder stage
COPY --from=backend-builder /app ./

# Copy environment file
COPY .env.docker .env

# Expose the backend API port
EXPOSE 5000

# Start the backend app
CMD ["npm", "start"]
