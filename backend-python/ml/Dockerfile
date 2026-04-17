# Use Python 3.10 (stable with TF CPU)
FROM python:3.10-slim

# Avoid interactive prompts
ENV DEBIAN_FRONTEND=noninteractive

WORKDIR /app

# Copy requirements first (helps caching)
COPY requirements.txt /app/requirements.txt

# Install system deps needed by pillow, tensorflow, etc.
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    libglib2.0-0 \
    libsm6 \
    libxrender1 \
    libxext6 \
 && rm -rf /var/lib/apt/lists/*

# Install Python deps
RUN pip install --upgrade pip setuptools wheel
RUN pip install --no-cache-dir -r /app/requirements.txt

# Copy the app
COPY . /app

# Default port (can be overridden with PORT env)
ENV PORT=8000

# Expose (for docs; Render will map the port)
EXPOSE 8000

# Start with gunicorn; allow PORT env to override default
CMD ["sh", "-c", "gunicorn -b 0.0.0.0:${PORT:-8000} predict:app --workers 1 --threads 4 --timeout 120"]
