# Aplicación Contenerizada - CRUD Node.js y MongoDB

## Requisitos Previos

- Docker Desktop y Docker Compose instalados.
- Git.

## Estructura del Proyecto

- `src/app.js`: Código del servidor Express y conexión a Mongoose.
- `Dockerfile`: Instrucciones de construcción de la imagen Node.js.
- `docker-compose.yml`: Orquestación de contenedores (App + MongoDB).
- `.env.example`: Plantilla de variables de entorno.

## Preparación de Variables

Copiar `.env.example` a `.env`:

```bash
cp .env.example .env
```
