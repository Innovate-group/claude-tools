# API de Warehouse - Solicitud de Preparacion (v1)

## PUT Crear Pedido de Preparacion

- **QA:** `https://apisqa.andreani.com/v1/almacenes/{numeroAlmacen}/pedidos`
- **PROD:** `https://apis.andreani.com/v1/almacenes/{numeroAlmacen}/pedidos`
- **Auth:** Bearer Token

### Request Body

```json
{
  "almacen": "string",                    // OBLIGATORIO - Almacen SCE (ej: "wmwhse6")
  "ContratoDistribucion": "string",       // OBLIGATORIO - max 30
  "ContratoWarehousePedido": "string",    // OBLIGATORIO - max 128
  "pedido": {
    "almacencliente": "string",           // OBLIGATORIO - max 30
    "CambioLoteDirigido": "string",       // OPCIONAL - max 128
    "COT": "string",                      // OPCIONAL - max 128
    "datosadicionales": null,             // OPCIONAL
    "destinatario": {
      "contacto": "string",               // OPCIONAL - max 30
      "direccion": {
        "calle": "string",                // OPCIONAL - max 45
        "codigopostal": "string",         // OPCIONAL - CP 4 digitos, max 18
        "departamento": "string",         // OPCIONAL - max 45
        "localidad": "string",            // OPCIONAL - max 45
        "numero": "string",               // OPCIONAL - max 45
        "pais": "string",                 // OPCIONAL - default AR, max 10
        "piso": "string",                 // OPCIONAL - max 45
        "provincia": "string",            // OPCIONAL - ISO, max 25
        "referenciadedomicilio": "string"  // OPCIONAL - max 45
      },
      "email": "string",                  // OPCIONAL - max 55
      "iddestinatario": "string",         // OBLIGATORIO - ID o "0", max 15
      "idinternocliente": "string",       // OPCIONAL - max 15
      "nombrecompleto": "string",         // OPCIONAL - max 45
      "numerodedocumento": "string",      // OPCIONAL - max 45
      "telefono": "string",               // OPCIONAL - max 18
      "tipodedocumento": "string"         // OPCIONAL - DNI/CUIT/CUIL
    },
    "Distribuidor": {
      "datosadicionales": "string",       // OPCIONAL
      "etiqueta": "string",               // OPCIONAL - URL etiqueta ZPL
      "nombre": "string",                 // OPCIONAL - Distribuidor preasignado
      "numerodeenvio": "string",          // OPCIONAL
      "remito": "string"                  // OPCIONAL - URL remito
    },
    "facturalegal": "string",             // OPCIONAL - max 128
    "fechadeexpedicionsolicitada": "string", // OPCIONAL - datetime
    "fechadefacturacion": "string",       // OPCIONAL - datetime
    "fechaentrega": "string",             // OPCIONAL - datetime
    "fechapedido": "string",              // OBLIGATORIO - datetime
    "franjahoraria": "string",            // OPCIONAL - max 128
    "idandreani": "string",              // OPCIONAL - max 55
    "linea": [                            // OBLIGATORIO - Array de articulos
      {
        "admitepickingparcial": "string", // OPCIONAL - max 128
        "articulo": {
          "cantidad": 0,                  // OBLIGATORIO - decimal(22)
          "codigo": "string",             // OBLIGATORIO - SKU, max 50
          "datosadicionales": null,       // OPCIONAL
          "lineaexterna": "string",       // OPCIONAL - max 20
          "lote": {
            "fechadevencimiento": "string", // OPCIONAL - datetime
            "lotedefabricante": "string", // OPCIONAL - max 50
            "lotesecundario": "string",   // OPCIONAL - max 50
            "otrosdatos": ""              // OPCIONAL
          },
          "numeropedido": "string",       // OPCIONAL - max 55
          "propietario": "string",        // OBLIGATORIO - ID propietario WMS, max 12
          "unidadmedida": "string"        // OBLIGATORIO - UM, max 10
        },
        "numerodelinea": 1,               // OPCIONAL - max 5
        "tipoacondicionamientoescundario": "string" // OPCIONAL - max 255
      }
    ],
    "marketplace": "string",              // OPCIONAL - max 128
    "notas": "string",                    // OPCIONAL - max 128
    "numeroordenexterna": "string",       // OBLIGATORIO - ID ref cliente, max 55
    "ordencompra": "string",              // OPCIONAL - max 50
    "preciovalorFC": "string",            // OPCIONAL - max 128
    "propietario": "string",              // OBLIGATORIO - ID propietario WMS, max 55
    "remito": "string",                   // OPCIONAL - max 55
    "sociocomercial": "string",           // OPCIONAL - max 15
    "tieneGestionCobranza": "string",     // OPCIONAL - max 128
    "tipo": "string",                     // OPCIONAL - max 128
    "valordeclarado": "string"            // OPCIONAL - max 128
  },
  "planta": "string"                      // OBLIGATORIO - Instancia/Almacen
}
```

### Response (HTTP 200)
```json
{
  "idTransaccion": 0
}
```

---

## GET Consultar Estado de Preparacion

- **Endpoint:** `/v1/almacenes/{numeroAlmacen}/pedidos/{idTransaccion}`
- **Auth:** Bearer Token
- **Estados posibles:** `PENDIENTE`, `ACEPTADO`, `RECHAZADO`
