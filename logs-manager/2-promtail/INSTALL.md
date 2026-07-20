# Promtail

Servicio para enviar datos desde ficheros de logs a Loki.

## Instrucciones de despliegue

Primero asegúrese de que los ficheros de logs existan (Ej: `/tmp/logs/**/*.log`).

Luego crear el archivo `.env` y reemplazar los valores de ser necesario.

```bash
cp .env.sample .env
```

Ahora si, podemos desplegar el servicio:

```bash
docker compose up -d
```
