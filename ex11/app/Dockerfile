FROM python:3.12-slim

RUN apt-get update && apt-get install -y curl \
    && rm -rf /var/lib/apt/lists/*

COPY index.html /index.html

CMD ["python3", "-m", "http.server", "80", "--directory", "/"]