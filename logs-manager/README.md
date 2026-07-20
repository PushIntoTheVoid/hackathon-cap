# Grafana Loki

Esta herramienta es para el monitoreo de logs en tiempo real.

## Despliega cada servicio en un servidor independiente

Más detalles en:

- [1-loki/INSTALL.md](./1-loki/INSTALL.md)
- [2-promtail/INSTALL.md](./2-promtail/INSTALL.md)
- [3-grafana/INSTALL.md](./3-grafana/INSTALL.md)

## Despliega todo en un solo servidor

Más detalles en:

- [4-loki-promtail-grafana/INSTALL.md](./4-loki-promtail-grafana/INSTALL.md)

## Para el registro de logs integrado al proyecto NestJS Base Backend

Los ficheros de logs de un determinado sistema se van creando sobre la carpeta definida en la variable de entorno `LOG_PATH`

**Ejemplo:** Si se tiene: `LOG_PATH=/tmp/logs/`, al desplegar la aplicación automáticamente se crearán los ficheros de logs con la siguiente estructura:

```txt
/tmp/logs
  ├─ nestjs-base-backend
        ├─ audit_application.log
        ├─ audit_authentication.log
        ├─ audit_casbin.log
        ├─ audit_https-exception.log
        ├─ audit_mensajeria.log
        ├─ audit_request.log
        ├─ audit_response.log
        ├─ error.log
        ├─ info.log
        └─ warn.log
```

Cada fichero de log tendrá masomenos el siguiente contenido:

```json
{"level":100,"time":1697590561392,"context":"application","pid":7001,"msg":"Servicio desplegado","app":"nestjs-backend-base","version":"1.0.0","entorno":"development","urlLocal":"http://localhost:3000","urlRed":"http://192.168.0.1:3000","fechaDespliegue":"17/10/2023 10:56:01:390"}
```

Y para agregar contenido a estos ficheros: [INSTRUCCIONES_USO_LOGGER.md](./INSTRUCCIONES_USO_LOGGER.md)
