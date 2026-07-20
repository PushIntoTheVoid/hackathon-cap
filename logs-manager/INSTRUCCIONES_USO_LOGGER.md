# Instrucciones de uso de logger

Este componente (Logger) nos permite registrar eventos o capturar errores del sistema, ya se encuentra integrado en el core del proyecto NestJS Backend Base.

## Modo de uso

**Ejemplo 1** Para registrar un error controlado manualmente

```ts
import { LoggerService } from "../src/core/logger";

const logger = LoggerService.getInstance();

function tarea(datos) {
  try {
    // código inseguro
  } catch (error) {
    logger.error(error);
  }
}
```

Ejemplos de implementación:

```ts
// Para capturar errores manualmente
logger.error(error);
logger.error(error, mensaje);
logger.error(error, mensaje, metadata);
logger.error(error, mensaje, metadata, modulo);
logger.error(error, {
  mensaje,
  metadata,
  modulo,
});

// Para registrar mensajes de advertencia
logger.warn(mensaje);
logger.warn(mensaje, metadata);
logger.warn(mensaje, metadata, modulo);
logger.warn({
  mensaje,
  metadata,
  modulo,
});
```

**Ejemplo 2** Para lanzar una excepción controlada de un error desconocido

```ts
import { BaseException } from "../src/core/logger";

function tarea(datos) {
  try {
    // código inseguro
  } catch (error) {
    throw new BaseException(error);
  }
}
```

Ejemplos de implementación:

```ts
// Para lanzar excepciones personalizadas
throw new BaseException(error);
throw new BaseException(error, {
  mensaje,
  metadata,
  modulo,
  httpStatus,
  causa,
  accion,
});
```

## Casos de uso

- Para registrar errores. Ej.: errores en tiempo de ejecución. Registro manual (errores controlados) y registro automático (errores no controlados)
- Para registrar eventos. Ej.: cuando un servicio ha sido iniciado o detenido, cuando un componente ha sido activado. Registro manual

## Caso 1. Para capturar errores en tiempo de ejecución

Los errores en tiempo de ejecución son problemas que se producen durante la ejecución de un programa y pueden interrumpir su funcionamiento normal.

Estos errores son capturados como excepciones y requieren un proceso de depuración para identificar y corregir la causa subyacente.

## 1. Errores Controlados

Nest proporciona un conjunto de [excepciones estándar](https://docs.nestjs.com/exception-filters#built-in-http-exceptions) que se heredan de la base `HttpException`. Estos se exponen desde el paquete `@nestjs/common`, las excepciones HTTP más comunes:

- `BadRequestException`
- `UnauthorizedException`
- `NotFoundException`
- `ForbiddenException`
- `ConflictException`
- `PreconditionFailedException`
- `...`

**Ejemplo:** Para lanzar una excepción propia de NestJS (de tipo `HttpException`)

```ts
import { UnauthorizedException } from "@nestjs/common";

function validar(headers) {
  if (!headers.authorization) {
    throw new UnauthorizedException();
  }
}
```

Ejemplos de implementación:

```ts
// Para lanzar excepciones básicas
throw new UnauthorizedException();
throw new UnauthorizedException(mensaje);
throw new UnauthorizedException(mensaje, { cause });
```

## 2. Errores No Controlados

Algunos errores que pueden presentarse de manera imprevista son los siguientes:

**Ejemplo:** Cuando una consulta SQL no se encuentra bien formulada:

```ts
async function recuperar() {
  // código inserguro no controlado
  return await this.dataSource
    .getRepository(Usuario)
    .createQueryBuilder("usuario")
    .leftJoinAndSelect("usuario.relacioninexistente", "relacioninexistente")
    .getMany();
}
```

**Nota.-** Estos tipos de errores siempre serán de tipo `500 Internal Server Exception`

## 3. Logs para servicios externos

Son errores producidos por servicios externos como SEGIP, Ciudadanía o Mensajería.

**Ejemplo:** Envío de un correo electrónico a través del servicio de mensajería.

```ts
function sendEmail(data) {
  try {
    // código inseguro - llamada a un servicio externo
    const response = this.httpService
      .post("/correo", data)
      .pipe(map((res) => res.data));
    const result = await firstValueFrom(response);
    return result;
  } catch (error) {
    const mensaje = "Ocurrió un error al enviar el mensaje por E-MAIL";
    throw new ExternalServiceException("MENSAJERÍA:CORREO", error, mensaje);
  }
}
```

Ejemplos de implementación:

```ts
// Para registrar detalles del error
throw new ExternalServiceException(servicio, error);
throw new ExternalServiceException(servicio, error, mensaje);
throw new ExternalServiceException(servicio, error, mensaje, metadata);
```

## 4. Logs de auditoría

Estos registros se crean en ficheros independientes y son utilizados para registrar eventos del sistema.

**Ejemplo 1** Registro de accesos

```ts
import { LoggerService } from "../src/core/logger";

const logger = LoggerService.getInstance();

function login(user) {
  logger.audit("authentication", {
    mensaje: "Ingresó al sistema",
    metadata: { usuario: user.id, tipo: "básico" },
  });
}
```

**Ejemplo 2** En el ejemplo anterior de envío de E-MAIL podemos agregar logs de auditoría para determinar todas las peticiones realizadas y aquellas que presentaron algún error.

```ts
function sendEmail(data) {
  try {
    // código inseguro - llamada a un servicio externo
    const response = this.httpService
      .post("/correo", data)
      .pipe(map((res) => res.data));
    const result = await firstValueFrom(response);
    logger.auditInfo("mensajeria", "E-MAIL enviado correctamente", {
      success: 1,
    });
    return result;
  } catch (error) {
    logger.auditError("mensajeria", "Falló al enviar el E-MAIL", {
      success: 0,
      status: error.response?.status,
      data: error.response?.data,
    });
    const mensaje = "Ocurrió un error al enviar el mensaje por E-MAIL";
    throw new ExternalServiceException("MENSAJERÍA:CORREO", error, mensaje);
  }
}
```

Ejemplos de implementación:

```ts
// Para registrar información específica del evento
logger.audit("application", mensaje);
logger.audit("application", mensaje, metadata);
logger.audit("application", {
  mensaje,
  metadata,
  formato, // Para darle formato cuando se imprime en la consola
});
```

Además se incluyen algunas variantes de implementación `auditError`, `auditWarning`, `auditSuccess` y `auditInfo` que se utiliza para diferenciar los mensajes cuando se imprimen por la consola, no tiene ningún efecto en los ficheros de logs.

```ts
logger.auditError("application", mensaje);
logger.auditWarning("application", mensaje);
logger.auditSuccess("application", mensaje);
logger.auditInfo("application", mensaje);
```

**Nota.-** Para crear logs de auditoría personalizados, se deben seguir los siguientes pasos:

1. Definir un contexto (cadena de texto sin espacios en blanco) y declararlo en el archivo `.env` adicionándolo a los demás contextos sobre la variable `LOG_AUDIT`.

```bash
# Ejemplo:
LOG_AUDIT='application request response ... mi-contexto'
```

2. Empezar a registrar los logs llamando a la función `audit`.

```ts
// Ejemplo
logger.auditInfo("mi-contexto", "Mensaje principal", { info: "adicional" });
```
