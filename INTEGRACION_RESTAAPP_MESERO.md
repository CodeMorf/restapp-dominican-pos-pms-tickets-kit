# Integración RestaAPP y Mesero Web (es-DO)

Este documento fija la integración real del kit visual con RestaAPP y con
Mesero Web. Las plantillas de este repositorio siguen siendo referencias
visuales autocontenidas; la orden, los importes, el cliente, los impuestos,
el comprobante, la cola y la impresora siempre pertenecen al backend de la
sucursal autenticada.

## Fuente de verdad

- Backend de RestaAPP: `https://restapp.allsender.tech`.
- API de integración: `/api/application-integration`.
- Configuración de recibos: `/platform/receipt-settings`.
- Capacidades fiscales reales: `/platform/fiscal-capabilities`.
- Emisión tradicional: `POST /pos/orders/{id}/fiscal`.
- Impresión de la orden: `POST /pos/orders/{id}/print`.
- KOT: `/pos/orders/{id}/kot` y `/pos/kots`.

Mesero Web consume esos contratos con el mismo Bearer token, restaurante,
sucursal y permisos. No copia las plantillas HTML dentro del navegador y no
calcula ITBIS, propina, totales, NCF o e-NCF.

## Flujo de cuenta y comprobante

1. Antes del cobro, el personal puede solicitar una **precuenta**. Es
   informativa, no fiscal, no consume NCF y no cierra la orden.
2. Al cobrar, el cajero selecciona el documento que el cliente necesita.
3. Para B01/B02/B14/B15/B16, el backend verifica la capacidad tradicional,
   la secuencia activa y los datos obligatorios del receptor.
4. El backend emite el documento con su servicio fiscal existente y luego
   genera el ticket con el diseño de la sucursal.
5. La impresión online se encola en la impresora configurada. Una respuesta
   HTTP correcta no se interpreta como confirmación de papel físico.

El receptor nunca es el restaurante. El restaurante y la sucursal son el
emisor; el receptor es el cliente asociado a la orden. En B02 puede ser
Consumidor Final. En B01 y B15 deben persistirse y validarse RNC/Cédula y
razón social o institución.

## Tradicional y electrónico

La aplicación debe consultar `/platform/fiscal-capabilities` antes de mostrar
opciones. Si `traditional.ready` es falso, no debe presentar B01/B02 como
disponibles para emisión fiscal. Si `electronic.ready` es falso, no debe
prometer E31/E32 ni inventar una emisión electrónica local.

El e-CF queda bloqueado hasta que el backend confirme módulo, identidad del
emisor, certificado, conector DGII, aceptación real y ambiente de producción.
El kit no contiene certificados, claves, secuencias ni datos fiscales reales.

## Diseño y operación

La interfaz puede presentar selección de documento, cliente, habitación,
mesa, notas, variantes y suplementos, pero esos datos se validan otra vez en
el backend. La misma identidad visual del kit se mantiene para factura,
precuenta, KOT, hotel, cortesía, reporte y documentos divididos.

No se debe abrir una ventana de precuenta después de un pago confirmado. Una
orden pagada debe esperar la selección explícita del documento y solo después
debe enviar el ticket final a la cola del backend.

## Comprobación mínima antes de publicar

- Confirmar `git status`, rama y SHA de cada repositorio.
- Ejecutar lint, pruebas y build de Mesero Web.
- Probar una orden no pagada y verificar que solo genera precuenta.
- Probar una orden pagada sin emitir dos NCF ni duplicar PrintJob.
- Probar B02 y B01 con un cliente controlado; no usar datos ficticios como
  identidad fiscal real.
- Revisar KOT, variantes, suplementos, notas y tipo de servicio.
- Revisar la impresora y el PrintJob del backend; no afirmar impresión física
  sin observar el papel.
- Probar móvil y escritorio.

Esta guía documenta la integración web. No genera APK ni aplicación Windows.
