FROM python:3.9-slim

WORKDIR /app

# Install system dependencies first
RUN apt-get update && \
    apt-get install -y --no-install-recommends gcc python3-dev && \
    rm -rf /var/lib/apt/lists/*

# Copy requirements first for better caching
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy all other files
COPY . .

# Set environment variables
ENV PYTHONUNBUFFERED=1

# Expose the port
EXPOSE 8000

# Run the application (removed the environment variables from CMD)
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]