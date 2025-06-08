# ------------------- Stage 1: Build Stage ------------------------------
FROM node:21 AS frontend-builder

# Set the working directory to /app
WORKDIR /app

# Copy package.json and package-lock.json first to leverage Docker's cache
COPY package*.json ./

# Clean npm cache
RUN npm cache clean --force

# Remove existing node_modules and package-lock.json if needed
RUN rm -rf node_modules
RUN rm -f package-lock.json

# Install dependencies (using legacy peer deps to resolve conflicts)
RUN npm install --legacy-peer-deps

# Copy the rest of the application code
COPY . .

# ------------------- Stage 2: Final Stage ------------------------------
FROM node:21-slim

# Set the working directory to /app
WORKDIR /app

# Copy built assets and dependencies from frontend-builder stage
COPY --from=frontend-builder /app ./

# Copy .env.docker to .env.local
COPY .env.docker .env.local

# Expose port for the Node.js application
EXPOSE 5173

# Run the app in development mode
CMD ["npm", "run", "dev", "--", "--host"]
