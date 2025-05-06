# Use an official Python runtime as a parent image
FROM python:3.9-slim as builder

# Set environment variables for docker-compose version
ENV DOCKER_COMPOSE_VERSION=1.29.2

# Set the working directory in the container
WORKDIR /app

# Install system dependencies
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    curl \
    jq \
    && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Download and install docker-compose V1
RUN curl -L "https://github.com/docker/compose/releases/download/${DOCKER_COMPOSE_VERSION}/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose && \
    chmod +x /usr/local/bin/docker-compose

# Copy requirements first to leverage Docker cache
COPY autoscaler/requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Final stage
FROM python:3.9-slim
WORKDIR /app

# Copy from builder
COPY --from=builder /usr/local/bin/docker-compose /usr/local/bin/docker-compose
COPY --from=builder /usr/local/lib/python3.9/site-packages /usr/local/lib/python3.9/site-packages

# Copy application files
COPY autoscaler/autoscaler.py .
COPY docker-compose.yml .

# Specify the command to run on container start
CMD ["python", "-u", "autoscaler.py"]