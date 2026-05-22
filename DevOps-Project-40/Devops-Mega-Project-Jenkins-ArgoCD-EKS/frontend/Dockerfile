# ------------------- Stage 1: Build Stage ------------------------------
# Use Node.js 21 image to build the frontend application
FROM node:21 AS frontend-builder

# Set the working directory inside the container
WORKDIR /app

# Copy package.json and package-lock.json first
# This helps Docker cache the npm install step if dependencies do not change
COPY package*.json ./

# Install frontend dependencies
RUN npm install

# Copy the rest of the frontend source code into the container
COPY . .

# Copy Docker environment file to Vite environment file
# Vite will read variables from .env.local during the build
COPY .env.docker .env.local

# Build the frontend application for production
# This creates the final static files inside the dist/ directory
RUN npm run build


# ------------------- Stage 2: Production Stage ------------------------------
# Use lightweight Nginx image to serve the built frontend files
FROM nginx:alpine

# Copy the production build output from the builder stage
# Nginx serves files from /usr/share/nginx/html by default
COPY --from=frontend-builder /app/dist /usr/share/nginx/html

# Expose port 80 because Nginx listens on port 80 by default
EXPOSE 80

# Start Nginx in the foreground so the container keeps running
CMD ["nginx", "-g", "daemon off;"]
