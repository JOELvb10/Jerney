# ---- Build stage ----
FROM node:20-alpine3.23 AS build

WORKDIR /app

# Update Alpine packages
RUN apk update && apk upgrade --no-cache

COPY package*.json ./

# Install production dependencies only
RUN npm ci --omit=dev && npm cache clean --force

# ---- Production stage ----
FROM node:20-alpine3.23 AS production

# Update Alpine packages to fix OS vulnerabilities
RUN apk update && apk upgrade --no-cache

# Install runtime dependencies
RUN apk add --no-cache dumb-init

# Create non-root user
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

WORKDIR /app

# Copy dependencies
COPY --from=build /app/node_modules ./node_modules

# Copy source
COPY src ./src

# Set ownership
RUN chown -R appuser:appgroup /app

# Run as non-root
USER appuser

EXPOSE 5000

ENTRYPOINT ["dumb-init", "--"]
CMD ["node", "src/index.js"]
