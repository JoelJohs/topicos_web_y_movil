# Misión 1 — El portal no es un patrón

## Hernández Saucedo Joel Josafat 20121000

1. Sesión y bitácora copiadas en cuarenta archivos.
   - Capa: políticas transversales / presentación servidor.
   - Patrón: Front Controller + Chain of Responsibility (middleware del marco).
   - Vecino rechazado: Page Controller aislado, sin frontal compartido (cada archivo decide por sí mismo).
   - No aplica si la app es un solo trámite sin políticas compartidas.

2. Switch de pasarelas de pago (tarjeta, SPEI, ventanilla) en pagar.php.
   - Capa: aplicación / dominio.
   - Patrón: Strategy (política intercambiable por medio).
   - Vecino rechazado: Adapter, porque el Adapter traduce un protocolo ajeno, no elige entre políticas del propio dominio.
   - No aplica si solo hay un medio y no se planean más.

3. Banco habla de "crédito" y 00/01; el dominio habla de "pago de inscripción" y pendiente/acreditado/rechazado.
   - Capa: integración.
   - Patrón: Adapter (Anti-Corruption Layer) que traduce el vocabulario del banco al del dominio.
   - Vecino rechazado: Facade, porque el banco no es un subsistema propio; aquí se aísla una lengua ajena, no se ofrece una fachada cómoda.
   - No aplica si el banco ya expusiera los conceptos del dominio.

4. Si el correo falla, a veces no se registra el alta en control escolar.
   - Capa: datos.
   - Patrón: Unit of Work / transacción del ORM que ata cobro y alta al mismo commit.
   - Vecino rechazado: Observer asíncrono ingenuo, porque diferir el alta a un manejador "cuando llegue el correo" rompe la atomicidad.
   - No aplica si cobro y alta viven en sistemas distintos sin transacción distribuida (ahí toca Saga/outbox).

5. El doble clic cobra dos cargos.
   - Capa: integración.
   - Patrón: clave de idempotencia sobre el recurso de pago (patrón REST, no GoF).
   - Vecino rechazado: Retry ciego, porque reintentar sin identificar la petición duplica el efecto.
   - No aplica si el medio ya garantiza unicidad por sí mismo (folio físico de ventanilla).

6. App móvil pide JSON mínimo y kiosco pide HTML con escudo; el equipo hace 12 peticiones para una pantalla.
   - Capa: borde.
   - Patrón: BFF (un endpoint por forma de cliente); API Gateway si hay varios servicios aguas abajo.
   - Vecino rechazado: Factory "que falte", porque el problema no es construir variantes, sino dar a cada cliente una representación a medida.
   - No aplica si todos los clientes consumen la misma vista y el mismo JSON.