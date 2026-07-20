# Grafana

Aplicación para visualizar logs

## Instrucciones de despliegue

Primero asegúrese de que el servicio de loki se enuentre activo.

Luego crear el archivo `.env` y reemplazar los valores de ser necesario.

```bash
cp .env.sample .env
```

Ahora si, podemos desplegar el servicio:

```bash
docker compose up -d
```

Dashboard de Grafana [http://localhost:7000](http://localhost:7000)

- user: `admin`
- pass: `admin`
