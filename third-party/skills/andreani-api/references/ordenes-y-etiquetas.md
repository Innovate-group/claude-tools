# API de Ordenes de Envio y Etiquetas (v2)

## Table of Contents
- [POST Crear Orden de Envio](#post-crear-orden-de-envio)
- [GET Consultar Estado](#get-consultar-estado)
- [GET Obtener Etiqueta](#get-obtener-etiqueta)

---

## POST Crear Orden de Envio

- **QA:** `https://apisqa.andreani.com/v2/ordenes-de-envio`
- **PROD:** `https://apis.andreani.com/v2/ordenes-de-envio`
- **Auth:** Bearer Token (vigencia 24h)

### Estados de la Orden
- **Pendiente**: API no se comunico con TMS
- **Solicitado**: API se comunico con TMS pero sin respuesta
- **Creado**: TMS acepto el preenvio, listo para admision fisica
- **Rechazado**: TMS rechazo el preenvio

### Request Body

```json
{
  "contrato": "string",              // OBLIGATORIO - max 30 chars
  "tipoDeServicio": "string",        // OPCIONAL
  "sucursalClienteID": 0,            // OPCIONAL
  "idPedido": "string",              // OPCIONAL (B2B) - Referencia cliente
  "centroDeCostos": "string",        // OPCIONAL - max 40 chars
  "productoAEntregar": "string",     // OPCIONAL (Logistica Inversa)
  "productoARetirar": "string",      // OPCIONAL (Logistica Inversa)
  "tipoProducto": "string",          // OPCIONAL - Linea Blanca, Electrodomestico, etc.
  "categoriaFacturacion": "string",  // OPCIONAL
  "pagoDestino": 2,                  // OPCIONAL - Porte=1, Debido=2
  "valorACobrar": 0.0,               // OPCIONAL - decimal(12,2)
  "codigoVerificadorDeEntrega": "string", // OPCIONAL
  "pagoPendienteEnMostrador": true,  // OPCIONAL - pago efectivo en sucursal
  "origen": {},                      // OBLIGATORIO
  "destino": {},                     // OBLIGATORIO
  "remitente": {},                   // OBLIGATORIO
  "destinatario": [],                // OBLIGATORIO
  "remito": {},                      // Opcional (B2B)
  "fechaDeEntrega": {},              // OPCIONAL
  "bultos": []                       // OBLIGATORIO
}
```

### origen (una de dos opciones)

**Opcion A: origen.postal**
```json
"origen": {
  "postal": {
    "codigoPostal": "string",        // OBLIGATORIO - 4 digitos
    "calle": "string",               // OBLIGATORIO - max 40
    "numero": "string",              // OBLIGATORIO - max 40
    "piso": "string",                // OPCIONAL
    "departamento": "string",        // OPCIONAL
    "localidad": "string",           // OBLIGATORIO - max 40
    "region": "string",              // OPCIONAL - ISO 3166-2:AR
    "pais": "string",                // OPCIONAL - default Argentina
    "casillaDeCorreo": "string",     // OPCIONAL - email
    "componentesDeDireccion": [
      { "meta": "entreCalle", "contenido": "Aguirre y Velazco" }
    ]
  }
}
```

**Opcion B: origen.sucursal**
```json
"origen": {
  "sucursal": {
    "id": "string",                  // OBLIGATORIO
    "nomenclatura": "string",        // OPCIONAL
    "descripcion": "string"          // OPCIONAL
  }
}
```

### destino (una de dos opciones)

**Opcion A: destino.postal**
```json
"destino": {
  "postal": {
    "codigoPostal": "string",        // OBLIGATORIO - max 12 chars
    "calle": "string",               // OBLIGATORIO - max 40
    "numero": "string",              // OBLIGATORIO - max 40
    "piso": "string",                // OPCIONAL
    "departamento": "string",        // OPCIONAL
    "localidad": "string",           // OBLIGATORIO - max 40
    "region": "string",              // OPCIONAL - ISO 3166-2:AR
    "pais": "string",                // OPCIONAL - default Argentina
    "casillaDeCorreo": "string",     // OPCIONAL - email
    "componentesDeDireccion": [
      { "meta": "piso", "contenido": "2" }
    ]
  }
}
```

**Opcion B: destino.sucursal**
```json
"destino": {
  "sucursal": {
    "id": "string"                   // OBLIGATORIO
  }
}
```

### remitente (OBLIGATORIO)
```json
"remitente": {
  "nombreCompleto": "string",        // OBLIGATORIO - max 40
  "email": "string",                 // OPCIONAL
  "documentoTipo": "string",         // OPCIONAL - DNI/CUIT/CUIL
  "documentoNumero": "string",       // OPCIONAL - max 20
  "telefonos": [
    {
      "tipo": 0,                     // Trabajo=0, Celular=1, Casa=2, Otros=3
      "numero": "string"             // max 15
    }
  ]
}
```

### destinatario (OBLIGATORIO - Array)
```json
"destinatario": [
  {
    "nombreCompleto": "string",      // OBLIGATORIO - max 40
    "email": "string",               // OPCIONAL - max 40
    "documentoTipo": "string",       // OPCIONAL - DNI/CUIT/CUIL
    "documentoNumero": "string",     // OPCIONAL - max 20
    "telefonos": [
      {
        "tipo": 1,                   // Trabajo=1, Celular=2, Casa=3, Otros=4
        "numero": "string"           // max 15
      }
    ]
  }
]
```

> **ATENCION**: Los valores de `tipo` en telefonos difieren entre remitente (0-3) y destinatario (1-4).

### remito (OBLIGATORIO para B2B)
```json
"remito": {
  "numeroRemito": "string",          // Formato: 123456789012R (12 digitos + R, max 30)
  "complementarios": ["string"]      // Array de remitos complementarios
}
```

### fechaDeEntrega (OPCIONAL)
```json
"fechaDeEntrega": {
  "fecha": "string",                 // YYYY-MM-DDTHH:mm:ss.SSSZ (mayor a fecha actual)
  "horaDesde": "string",
  "horaHasta": "string"
}
```

### bultos (OBLIGATORIO)
```json
"bultos": [
  {
    "kilos": 0.0,                    // OBLIGATORIO - decimal(12,3)
    "largoCm": 0,                    // OPCIONAL
    "altoCm": 0,                     // OPCIONAL
    "anchoCm": 0,                    // OPCIONAL
    "volumenCm": 0.0,                // OBLIGATORIO - decimal(6,3)
    "valorDeclaradoSinImpuestos": 0.0, // OPCIONAL B2B - decimal(12,2)
    "valorDeclaradoConImpuestos": 0.0, // OPCIONAL - decimal(12,2)
    "referencias": [
      { "meta": "idCliente", "contenido": "string" },
      { "meta": "observaciones", "contenido": "string" }
    ],
    "descripcion": "string",         // OPCIONAL
    "numeroDeEnvio": "string",       // OPCIONAL - pre-numeracion
    "ean": "string"                  // OPCIONAL
  }
]
```

### Response B2C (HTTP 202)
```json
{
  "estado": "Pendiente",
  "tipo": "B2C",
  "sucursalDeDistribucion": { "nomenclatura": "TIG", "descripcion": "TIGRE (AV PRES J D PERON)", "id": "96" },
  "sucursalDeRendicion": { "nomenclatura": "REN", "descripcion": "AVELLANEDA - RENDICIONES", "id": "112" },
  "fechaCreacion": "2025-11-10T17:29:40-03:00",
  "numeroDePermisionaria": "RNPSP Nro 586",
  "descripcionServicio": "Encomienda",
  "bultos": [
    {
      "numeroDeBulto": "1",
      "numeroDeEnvio": "360000101651699",
      "totalizador": "1/1",
      "linking": [
        { "meta": "Etiqueta", "contenido": "https://apisqa.andreani.com/v2/ordenes-de-envio/API0000000428931/etiquetas?bulto=1" }
      ]
    }
  ],
  "agrupadorDeBultos": "API0000000428931",
  "etiquetasPorAgrupador": "https://apisqa.andreani.com/v2/ordenes-de-envio/API0000000428931/etiquetas"
}
```

### Response B2B (HTTP 202)
```json
{
  "estado": "Pendiente",
  "tipo": "DMS",
  "sucursalDeDistribucion": { "nomenclatura": "SAT", "descripcion": "SALTA (AU CIRC OESTE)", "id": "23" },
  "fechaCreacion": "2025-11-10T17:45:00-03:00",
  "zonaDeReparto": "1",
  "descripcionServicio": "ENTREGA / BULTO/PAQUETE / SLA 1 - ESTANDAR B2B / FORMULARIO B2B 1",
  "etiquetaRemito": "https://apis.andreani.com/v2/ordenes-de-envio/400400003255576/etiquetas?tipo=remito",
  "bultos": [
    {
      "numeroDeBulto": "400400003255576001",
      "numeroDeEnvio": "400400003255576",
      "totalizador": "1/1",
      "linking": [
        { "meta": "Etiqueta", "contenido": "https://apis.andreani.com/v2/ordenes-de-envio/400400003255576/etiquetas?bulto=400400003255576001" }
      ]
    }
  ],
  "etiquetasPorAgrupador": "https://apis.andreani.com/v2/ordenes-de-envio/400400003255576/etiquetas"
}
```

### Errores

**400 Bad Request:**
```json
{ "detail": "string", "errors": [{ "field": "string", "message": "string" }], "status": 0, "title": "string", "type": "string" }
```

Casos comunes: contrato incorrecto/obligatorio, CP incorrecto, localidad inexistente, destinatario vacio, bultos vacio, numero de envio duplicado, max 300 bultos, remito no unico, JSON invalido.

**401 Unauthorized**: Sin acceso al preenvio.
**404 Not Found**: Sucursal no encontrada o numero de envio inexistente.

---

## GET Consultar Estado

- **QA:** `https://apisqa.andreani.com/v2/ordenes-de-envio/{numeroAndreani}`
- **PROD:** `https://apis.andreani.com/v2/ordenes-de-envio/{numeroAndreani}`
- **Auth:** Bearer Token
- **Path param:** `numeroAndreani` = `agrupadorDeBultos` (B2C) o `numeroDeEnvio` (B2B)

### Response (HTTP 200)
```json
{
  "estado": "Creada",
  "tipo": "B2C",
  "sucursalDeDistribucion": { "nomenclatura": "SAT", "descripcion": "SALTA (CIRC. OESTE)", "id": "180" },
  "sucursalDeRendicion": { "nomenclatura": "REN", "descripcion": "AVELLANEDA - RENDICIONES", "id": "112" },
  "fechaCreacion": "2025-11-07T13:58:26-03:00",
  "descripcionServicio": "Encomienda",
  "bultos": [
    {
      "numeroDeBulto": "1",
      "numeroDeEnvio": "360002788173830",
      "totalizador": "1/1",
      "linking": [
        { "meta": "Etiqueta", "contenido": "https://apis.andreani.com/v2/ordenes-de-envio/API0000137265918/etiquetas?bulto=1" }
      ]
    }
  ],
  "agrupadorDeBultos": "API0000137265918",
  "etiquetasPorAgrupador": "https://apis.andreani.com/v2/ordenes-de-envio/API0000137265918/etiquetas"
}
```

---

## GET Obtener Etiqueta

- **QA:** `https://apisqa.andreani.com/v2/ordenes-de-envio/{numeroAndreani}/etiquetas`
- **PROD:** `https://apis.andreani.com/v2/ordenes-de-envio/{numeroAndreani}/etiquetas`
- **Auth:** Bearer Token
- **Path param:** B2C = `agrupadorDeBultos`, B2B = `numeroDeEnvio`

### Headers
- `Accept: application/pdf` (default)
- `Accept: application/zpl` (impresoras termicas Zebra)

### Query Parameters
| Param | Descripcion | Ejemplo |
|-------|-------------|---------|
| bulto | Numero de bulto especifico | `?bulto=1` |
| tipo | `remito` o `documentoDeCambio` | `?tipo=remito` |
| id | Identificador de etiqueta | - |
| lote | Lote (cada 50 etiquetas) | `?lote=1` |
| desde | Desde que etiqueta | `?desde=1` |
| hasta | Hasta que etiqueta (max 50) | `?hasta=50` |

### Ejemplos de URLs
- Bulto especifico: `/ordenes-de-envio/API0000137265918/etiquetas?bulto=1`
- Todas las etiquetas: `/ordenes-de-envio/API0000137265918/etiquetas`
- Remito B2B: `/ordenes-de-envio/400400003255576/etiquetas?tipo=remito`
- Doc de cambio: `/ordenes-de-envio/{id}/etiquetas?tipo=documentoDeCambio`
