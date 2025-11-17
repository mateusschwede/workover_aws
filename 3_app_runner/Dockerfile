# =============== FRONTEND BUILD ===============
FROM node:20 AS build-frontend
WORKDIR /app/frontend

# Copia o frontend
COPY cafe-frontend/ .

# Instala e gera build
RUN npm install
RUN npm run build

# =============== BACKEND ===============
FROM python:3.10-slim

# Evita buffer do Python
ENV PYTHONUNBUFFERED=1

# Porta padrão do App Runner
ENV PORT=8000

WORKDIR /app

# Copia requirements do backend
COPY paris_cafe/requirements.txt .

# Instala dependências Python
RUN pip install --no-cache-dir -r requirements.txt

# Copia o projeto Django
COPY paris_cafe/ ./paris_cafe/

# Copia build do frontend para a pasta de estáticos do Django
COPY --from=build-frontend /app/frontend/dist ./static/

# Gera arquivos estáticos e migrações
RUN python paris_cafe/manage.py collectstatic --noinput
RUN python paris_cafe/manage.py migrate

# Comando final do servidor
CMD gunicorn paris_cafe.wsgi:application --bind 0.0.0.0:$PORT