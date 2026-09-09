# Dominican POS & Hospitality Print Kit (es-DO)

Este repositorio contiene un conjunto modular de plantillas HTML para impresión térmica de **80 mm**, orientado a sistemas de Punto de Venta (**POS**) y Sistemas de Gestión Hotelera (**PMS**) en la República Dominicana.

El kit sirve como base visual y estructural para flujos de restaurantes, bares, hoteles y control de caja. Incluye comprobantes operativos, documentos de referencia fiscal y formatos de auditoría que deben conectarse al motor de plantillas, la base de datos y el servicio de impresión del sistema integrador.

> **Alcance de esta versión:** los archivos son plantillas HTML estáticas y autocontenidas. Los nombres, fechas, montos, RNC, NCF/e-NCF, códigos y datos de “Hotel Vista Mar” que aparecen en pantalla son ilustrativos. Las plantillas no generan comprobantes fiscales electrónicos, no consultan la DGII, no crean códigos QR válidos, no persisten cargos y no sustituyen la certificación o validación legal correspondiente.

## Estructura del proyecto

```text
Restapp/
├── Factura/
│   ├── factura_de_consumidor_final_e_cf.html
│   ├── factura_de_cr_dito_fiscal_e_cf.html
│   └── pre_cuenta.html
├── hotel/
│   ├── cargo_a_habitaci_n.html
│   ├── comanda_de_room_service.html
│   ├── comprobante_de_check_in.html
│   └── factura_de_check_out.html
├── kot/
│   ├── comanda_de_bar.html
│   ├── comanda_de_cocina.html
│   └── comanda_de_estaciones_parrilla_y_postres.html
├── Nota de Crédito o Débito/
│   └── nota_de_cr_dito_fiscal.html
├── reporte/
│   └── reporte_de_propina_legal.html
└── Ticket de Cortesía o Consumo de Personal (Comps)/
    └── ticket_de_cortes_a.html
```

## Plantillas incluidas

### Facturación y documentos comerciales (`Factura/`)

- **Pre-cuenta:** muestra subtotal, ITBIS estimado y propina de ley estimada, con la leyenda `PRE-CUENTA - NO VÁLIDA PARA CRÉDITO FISCAL`.
- **Consumidor final e-CF:** estructura visual para un comprobante de consumidor final de la serie B02/e-CF E32, incluyendo área para NCF/e-NCF, datos fiscales y QR.
- **Crédito fiscal e-CF:** estructura visual para cliente corporativo de la serie B01/e-CF E31, con espacios para RNC, razón social, firma de seguridad y QR.

El backend integrador debe decidir cuándo solicitar cédula, pasaporte, nombre, RNC o razón social, validar los límites y reglas vigentes, asignar el NCF/e-NCF autorizado y generar los datos auténticos del comprobante.

### Comandas operativas (`kot/`)

- Comanda de **cocina**.
- Comanda de **bar**.
- Comanda de **estaciones secundarias**, como parrilla y postres.

Están diseñadas para priorizar la lectura operativa: artículo, cantidad, mesa, hora, notas y estación. No deben mostrar importes cuando el flujo de preparación no los requiere.

### Operación hotelera (`hotel/`)

- Comprobante de **check-in**.
- **Cargo a la habitación**, con espacio para aceptación y firma del huésped.
- **Room service**, con número de habitación destacado, huésped, notas de montaje y firma de despacho.
- Factura de **check-out**, con separación visual de alojamiento, alimentos y bebidas, ITBIS y propina.

El PMS debe controlar la estancia, el folio, la habitación, el huésped, los consumos, la autorización del cargo y el cierre. La plantilla no crea ni modifica esos registros.

### Ajustes, auditoría y cortesía

- `Nota de Crédito o Débito/nota_de_cr_dito_fiscal.html`: formato de referencia para una nota vinculada a un comprobante original.
- `reporte/reporte_de_propina_legal.html`: resumen de propina de ley y distribución operativa.
- `Ticket de Cortesía o Consumo de Personal (Comps)/ticket_de_cortes_a.html`: registro de cortesías y consumos internos con valor cero para auditoría.

## Integración con un sistema POS/PMS

### Motor de plantillas

Reemplace los datos de muestra por variables del stack utilizado. Por ejemplo:

- EJS/Node.js: `<%= factura.ncf %>`
- Jinja2/Python: `{{ factura.ncf }}`
- Blade/PHP: `{{ $ncf }}`

Mantenga separadas la presentación y las reglas de negocio. El integrador debe escapar los valores que se inserten en HTML y aplicar una política de datos que evite exponer información de otros restaurantes, sucursales, huéspedes o cajas.

### Validaciones fiscales y de negocio

Antes de permitir una impresión fiscal, el backend debe, como mínimo:

1. Confirmar el tipo de comprobante y la identidad del emisor.
2. Validar la secuencia NCF/e-NCF, su vigencia y su estado según la configuración autorizada.
3. Validar los datos obligatorios del cliente para el tipo de comprobante.
4. Calcular subtotal, ITBIS, propina, descuentos y total a partir de datos persistidos, no de valores recibidos únicamente desde el navegador.
5. Vincular notas de crédito o débito con el comprobante original.
6. Registrar la emisión, el usuario, la sucursal, la impresora y el resultado de la operación.
7. Generar la firma, el código de seguridad y el QR auténticos cuando el flujo de e-CF los requiera.

Las tasas y reglas mostradas en estas plantillas —incluyendo ITBIS de 18% y propina de ley de 10%— deben confirmarse con la normativa vigente y con el asesor fiscal o proveedor autorizado antes de usarse en producción.

### Impresión térmica

Las hojas de estilo incluyen reglas `@media print` para ocultar elementos de pantalla y conservar un formato de rollo térmico. Para un POS web basado en Chromium, el integrador puede evaluar un modo de impresión administrado, por ejemplo `--kiosk --kiosk-printing`, siempre que haya controles de seguridad, permisos y pruebas específicas de cada equipo.

La aceptación física requiere probar cada combinación de navegador o agente, impresora, controlador, ancho real del rollo, densidad, corte y conectividad. Una vista correcta en pantalla no demuestra por sí sola que el ticket salga correctamente en una impresora instalada.

## Uso local

No se requiere un framework ni un proceso de compilación para revisar las plantillas. Abra cualquier archivo `.html` en un navegador y use la vista de impresión. Para impresión de 80 mm, compruebe la configuración de tamaño de papel y márgenes del navegador o del agente de impresión.

## Estado y próximos pasos

Esta publicación entrega la capa de plantillas visuales. Quedan a cargo del sistema integrador:

- conexión con POS/PMS y persistencia de datos;
- cálculo y validación server-side;
- autorización por restaurante, sucursal y rol;
- emisión y consulta real de NCF/e-CF;
- generación de QR y firma digital;
- sincronización de folios hoteleros y cargos a habitación;
- cola, reintentos e idempotencia de impresión;
- pruebas fiscales, funcionales, de accesibilidad y de hardware.

La integración web real con RestaAPP y Mesero está documentada en
[`INTEGRACION_RESTAAPP_MESERO.md`](INTEGRACION_RESTAAPP_MESERO.md). El kit
continúa siendo la referencia visual; el backend mantiene la autoridad sobre
datos de la orden, fiscalidad, aislamiento por sucursal, PrintJob e impresión.

## Autoría

Desarrollado por **CodeMorf** para integrarse con productos POS/PMS de la familia correspondiente. La marca visible del establecimiento, restaurante u hotel debe configurarse en el sistema cliente y no asumirse desde estos ejemplos.
