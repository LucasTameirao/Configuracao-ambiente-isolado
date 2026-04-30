FROM python:3.12-slim

# Evita gerar arquivos .pyc
ENV PYTHONDONTWRITEBYTECODE=1

# Evita buffer no terminal
ENV PYTHONUNBUFFERED=1

WORKDIR /workspace

# (Opcional) instala dependências se existir requirements.txt
COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt || true
