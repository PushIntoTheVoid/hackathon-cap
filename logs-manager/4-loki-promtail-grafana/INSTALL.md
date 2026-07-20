# Instrucciones de despliegue

Para desplegar los servicios de Loki, Promtail y Grafana dentro del mismo servidor.

## Despliegue de grafana en modo desarrollo

Primero asegúrese de que los ficheros de logs existan (Ej: `/tmp/logs/**/*.log`).

**Nota.-** Asegúrese de que el grupo al que pertenece el usuario de sistema tenga permisos de escritura sobre la carpeta logs.

```bash
sudo chmod -R 775 /tmp/logs
```

Luego crear el archivo `.env` y reemplazar los valores de ser necesario

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
