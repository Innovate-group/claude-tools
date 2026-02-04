---
name: andreani-api
description: >
  Integracion con las APIs de Andreani para gestion de envios en Argentina.
  Cubre creacion de ordenes de envio (B2C/B2B), consulta de estados,
  obtencion de etiquetas (PDF/ZPL), y solicitud de preparacion en warehouse.
  Usar cuando el usuario pida implementar logica de envios con Andreani,
  crear ordenes, consultar tracking, generar etiquetas, o integrar warehouse
  de Andreani. Triggers: andreani, orden de envio, etiqueta andreani,
  shipping andreani, envio andreani, warehouse andreani, preenvio, bultos,
  crear envio, tracking andreani, cotizar envio andreani, logistica andreani
---

# Andreani API Integration

Documentacion para integrar las APIs de Andreani (envios Argentina). Version API: v2 (ordenes/etiquetas), v1 (warehouse).

## Entornos

| Entorno | Base URL |
|---------|----------|
| QA | `https://apisqa.andreani.com` |
| PROD | `https://apis.andreani.com` |

Autenticacion: Bearer Token en header (vigencia 24 horas).

## Endpoints

| Endpoint | Metodo | Path | Uso |
|----------|--------|------|-----|
| Crear orden | POST | `/v2/ordenes-de-envio` | Crear envio B2C o B2B |
| Consultar estado | GET | `/v2/ordenes-de-envio/{id}` | Ver estado de orden |
| Obtener etiqueta | GET | `/v2/ordenes-de-envio/{id}/etiquetas` | PDF o ZPL |
| Solicitar preparacion | PUT | `/v1/almacenes/{almacen}/pedidos` | Warehouse |
| Consultar preparacion | GET | `/v1/almacenes/{almacen}/pedidos/{txId}` | Estado warehouse |

## Flujo tipico

1. **Crear orden** (POST) → recibir `agrupadorDeBultos` (B2C) o `numeroDeEnvio` (B2B)
2. **Consultar estado** (GET) → polling hasta estado `Creada`
3. **Obtener etiquetas** (GET) → usar `etiquetasPorAgrupador` de la respuesta
4. Imprimir/guardar etiqueta (PDF default, ZPL para Zebra)

## B2C vs B2B

- **B2C**: Hasta 50kg, 1 bulto. Usar `agrupadorDeBultos` como identificador.
- **B2B**: Mayor a 50kg o multiples bultos. Requiere `remito` (formato: 12 digitos + R). Usar `numeroDeEnvio` como identificador.

## Campos obligatorios minimos (crear orden)

```
contrato, origen (postal o sucursal), destino (postal o sucursal),
remitente.nombreCompleto, destinatario[].nombreCompleto,
bultos[].kilos, bultos[].volumenCm
```

Para B2B agregar: `remito.numeroRemito`, `remito.complementarios`

## Validaciones clave

- CP origen: 4 digitos; CP destino: max 12 chars
- Max 300 bultos por orden
- Remito B2B: 12 digitos + R, deben ser unicos
- Token caduca en 24h → implementar refresh automatico
- Ordenes vigentes por 30 dias

## Manejo de errores

- **400**: Validacion fallida. Response incluye `errors[].field` y `errors[].message`.
- **401**: Token invalido o expirado.
- **404**: Sucursal o envio no encontrado.

Implementar reintentos con backoff exponencial para errores transitorios.

## Documentacion en linea

Si necesitas consultar o verificar documentacion actualizada de la API, podes navegar el sitio oficial de developers usando el MCP de Chrome:

- **URL**: `https://developers.andreani.com/document`
- Usar el MCP de Chrome para acceder al sitio, buscar endpoints, ver ejemplos y validar schemas.
- Util cuando la referencia local no cubra un caso especifico o se necesite confirmar cambios recientes en la API.

## Referencia detallada

- **Ordenes y etiquetas**: Read [references/ordenes-y-etiquetas.md](references/ordenes-y-etiquetas.md) para schemas completos de request/response de POST crear orden, GET estado, GET etiqueta.
- **Warehouse**: Read [references/warehouse.md](references/warehouse.md) para schema completo de PUT solicitud de preparacion.
