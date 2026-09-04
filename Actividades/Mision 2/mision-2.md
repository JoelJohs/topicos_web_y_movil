# Misión 2 — Una petición, varios patrones

## Hernández Saucedo Joel Josafat 20121000

Recorrido del POST /pagos para pagar la inscripción, desde el clic hasta persistir y notificar.

1. Cliente envía POST /pagos con {medio, monto, folio, idempotencyKey}. Patrón: REST como recurso, no como acción (no /pagar).
2. Gateway / borde recibe la petición. Patrón: API Gateway si hay varios servicios, o Front Controller del servicio.
3. Enrutador del marco (DispatcherServlet en Spring, Router en Laravel, express.Router en Express) matchea la ruta con PagosController#create. Patrón: Front Controller.
4. Cadena de middleware: sesión, bitácora, cupo. Patrón: Chain of Responsibility.
5. Controlador delgado valida el idempotencyKey y delega. Patrón: Controller del MVC (papel fino).
6. Servicio ServicioPagos#registrarPago orquesta el caso de uso. Patrón: Service Layer.
7. Se consulta la estrategia del medio elegido (tarjeta / SPEI / ventanilla). Patrón: Strategy.
8. La estrategia entrega un adaptador del banco concreto. Patrón: Adapter (traduce crédito/00 a pagoInscripcion/acreditado).
9. Se abre la transacción del ORM que cubre cobro + alta de materias. Patrón: Unit of Work.
10. Repository persiste Pago y AltaMateria por agregados. Patrón: Repository.
11. Tras el commit se publica el evento PagoAcreditado. Patrón: Observer.
12. Suscriptores encolan el correo y el aviso a caja (en proceso o cola según SLA). Patrón: Observer materializado.
13. Controlador responde 201 Created con Location: /pagos/{id}. Patrón: REST.

Lo que no hay en el recorrido:
- No hay PagoFacade sobre el banco: el banco no es subsistema propio, es externo, le toca Adapter.
- No hay DTO intermediario que solo renombre entre controlador y servicio.
- No se reimplementa el enrutador: el marco ya lo trae.
- No se mete el SMTP en el controlador: el correo es Observer posterior al commit.