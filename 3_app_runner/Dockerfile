# 1. Build do frontend
FROM node:20 AS build-frontend
WORKDIR /app/frontend
COPY cafe-frontend/ .
RUN npm install
RUN npm run build

# 2. Backend + servidor final
FROM python:3.10-slim

# Instalar dependências
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copiar backend
COPY paris_cafe/ ./paris_cafe/

# Copiar frontend dentro do backend estático
COPY --from=build-frontend /app/frontend/dist ./static/

# Variáveis necessárias
ENV PYTHONUNBUFFERED=1
ENV PORT=8000

# Coletar estáticos e migrar BD (SQLite mesmo)
RUN python paris_cafe/manage.py collectstatic --noinput
RUN python paris_cafe/manage.py migrate

# Comando final
CMD gunicorn paris_cafe.wsgi:application --bind 0.0.0.0:$PORT