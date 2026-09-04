# Misiones — Patrones de software para aplicaciones web

> Apartado de referencia: Patrones de software para aplicaciones web. Entrega escrita, individual o en pareja. La pauta de revisión se consulta **únicamente después** de intentar cada misión.

---

## El problema

Una universidad pública mantiene el alta de materias, el pago de inscripción, las constancias y las becas en decenas de páginas sueltas (PHP, ASP clásico y un par de servicios nuevos). Se requiere un portal web único para el siguiente ciclo.

### Estado actual (documentado por control escolar y por caja)

- Cada trámite es un archivo distinto. En todos se copia el mismo bloque de «¿hay sesión?», el mismo registro en bitácora y el mismo encabezado HTML. Cuando cambia la regla de caducidad de la sesión, hay que tocar cuarenta archivos; siempre se olvida uno.
- El pago de inscripción admite tarjeta, transferencia SPEI y referencia de ventanilla. El script de `pagar.php` es un `switch` de doscientas líneas. Cada banco nuevo obliga a editar ese archivo. El protocolo de un banco habla de «créditos» y códigos `00/01`; el reglamento interno habla de «pago de inscripción» y estados `pendiente / acreditado / rechazado`.
- La plantilla del kardex ejecuta consultas SQL para armar la tabla de calificaciones. Los reportes de constancias duplican esas consultas con otro formato.
- Cuando el pago se acredita, el mismo script llama a control escolar (alta de materias), dispara un correo al estudiante y avisa a caja. Si el correo falla, a veces no se registra el alta. Si el estudiante pulsa dos veces «pagar» porque la página tarda, se han cobrado dos cargos.
- La aplicación móvil de la universidad y el kiosco de la biblioteca deben mostrar el mismo trámite. La app pide un JSON mínimo (folio, saldo, plazo). El kiosco pide una página HTML con el escudo y la tabla de vencimientos. Hoy el equipo de la app hace doce peticiones para pintar la pantalla de inicio.
- El servicio de un banco y el de un validador de CURP externo se caen con frecuencia. Mientras no responden, el estudiante ve la rueda de espera y no puede ni consultar el kardex, que no depende de esos colaboradores.
- Un proveedor propone, para la descarga de una constancia en PDF, **Event Sourcing**, **CQRS**, una malla de microservicios y un almacén global Redux en el navegador. El trámite de la constancia es: autenticar, consultar un registro ya existente y generar un archivo.

### Marco

El portal nuevo puede construirse en **Spring**, en **Laravel** o en **Express**: el análisis de patrones no espera un marco concreto. Sí espera que se use lo que el marco ya instancia (enrutador, middleware, transacción del ORM) y que no se copie un diagrama UML al lado.

---

## Qué se evalúa

| Criterio | Se espera |
|---|---|
| Composición | El portal no se etiqueta con un solo patrón. Cada conflicto tiene una estructura y una capa. |
| Cuándo y por qué | Se justifica con una fuerza del problema, no con «porque sale en el temario». |
| Vecino rechazado | Adapter no se confunde con Facade; Observer no sustituye a Unit of Work. |
| Marco | Se nombra qué no hay que reescribir si el equipo usa Spring, Laravel o Express. |
| Sobreingeniería | Se rechaza el catálogo donde no hay problema. |

---

## Misión 1 — El portal no es un patrón

Enuncie **seis problemas distintos** del relato (un renglón cada uno). Para cada uno indique:

- la capa (presentación, políticas transversales, aplicación/dominio, datos, integración);
- el patrón (o la pareja de patrones) que corresponde;
- por qué ese y no el vecino más fácil de confundir (p. ej. Adapter frente a Facade, Observer frente a Unit of Work);
- cuándo **no** aplicaría, aunque el nombre «quede bonito».

**Entregable:** una tabla de seis filas. El portal **no puede** aparecer como «es MVC» ni como «es hexagonal».

---

## Misión 2 — Una petición, varios patrones

Siga el caso de uso **pagar la inscripción** desde el clic (o desde `POST`) hasta persistir y notificar. Liste, en orden, las estructuras que atraviesa la petición. Para cada paso: qué objeto o mecanismo es (enrutador, filtro, servicio, repositorio…) y qué patrón está realizando.

Apóyese en el esquema de **composición a lo largo de una petición HTTP** del apartado web. **No invente una capa vacía que solo delega.**

---

## Misión 3 — Políticas transversales

La caducidad de sesión y la bitácora no pueden seguir copiadas en cuarenta archivos.

1. ¿Cuándo corresponde **Front Controller** y por qué?
2. ¿Qué mecanismo ya ofrece **Spring**, **Laravel** o **Express** para no escribir un segundo frontal ni una cadena GoF casera?
3. Proponga el **orden de la cadena** (autenticación, cupo de inscripciones, bitácora, caso de uso). Justifique por qué **autenticar después del cobro** sería un error de diseño, no un detalle de implementación.
4. ¿Qué **no** debe hacer esa cadena? (El apartado web lo enuncia: las políticas transversales no son el reglamento.)

---

## Misión 4 — Pago: Strategy, Adapter y Factory

Tres hechos conviven en el pago: varias políticas (tarjeta, SPEI, ventanilla); un protocolo ajeno del banco; la creación del cobrador según lo que elija el estudiante.

- Asigne **Strategy**, **Adapter** y **Factory** a esos tres hechos. Una frase de por qué en cada uno.
- ¿Qué forma tendría **Strategy** si el lenguaje fuera Java y si fuera JavaScript? (El apartado distingue **interfaz** frente a **función**.)
- El banco dice «crédito» y `00`. El dominio dice «pago de inscripción» y `acreditado`. ¿Eso es **Adapter** o **Facade**? ¿Por qué no el otro?

---

## Misión 5 — Atomicidad frente a aviso

El alta en control escolar no puede quedar a medias respecto del cobro. El correo al estudiante sí puede retrasarse unos minutos.

1. ¿Qué patrón cubre **cobro + alta juntos**, y por qué **Observer** no basta para ese par?
2. ¿Qué patrón cubre el **correo** y el **aviso a caja**, y en qué condición (en proceso frente a cola)?
3. El doble clic genera dos cargos. ¿Qué **patrón de integración (no GoF)** exige el apartado web, y sobre qué recurso REST lo colocaría (`POST /pagos` u otro)?

---

## Misión 6 — Varios clientes y un colaborador inestable

1. App móvil (JSON mínimo) y kiosco (HTML con escudo): ¿**BFF**, **API Gateway**, o ambos? ¿Cuándo no haría falta un BFF?
2. Proponga un **recurso REST** para la inscripción (identificador y representación). ¿Por qué `/inscribir` o `/ganar` sería una **acción disfrazada**?
3. El banco o el validador de CURP no responden. El kardex sí debería seguir consultable. ¿**Timeout**, **Retry** o **Circuit Breaker** en cada caso? ¿Qué no se reintenta?
4. Un proveedor quiere **Event Sourcing** y **Redux** para la constancia en PDF. Con las reglas de **sobreingeniería** del apartado web, argumente en un párrafo si se acepta o se rechaza, y qué estructuras sí bastan (autenticación, consulta, Template o Transform View).

---

## Misión 7 — El marco ya lo trae

El equipo se divide: mitad **Spring Boot**, mitad **Express**. Sin escribir código, complete:

| Intención | En Spring se usa… | En Express se usa… | Lo que no se reescribe |
|---|---|---|---|
| Punto único de entrada | | | |
| Cadena de políticas | | | |
| Unidad de trabajo | | | |
| Observer en el navegador de la SPA | | | |

La última columna debe decir, en cada fila, el error de duplicar el mecanismo nativo.

---

## Pauta de revisión

> Consultar **después** de entregar. No es la única redacción válida; sí es el mapa de fuerzas del relato.

| Hecho del portal | Capa | Patrón(es) | Vecino que no es |
|---|---|---|---|
| Sesión y bitácora copiadas en cuarenta archivos | Políticas / presentación servidor | Front Controller + Chain of Responsibility (middleware) | Page Controller solo, sin frontal compartido |
| `switch` de pasarelas de pago | Aplicación | Strategy | Adapter (el Adapter traduce al banco, no elige la política) |
| Protocolo del banco («crédito», `00`) | Integración | Adapter (Anti-Corruption) | Facade (no es un subsistema propio) |
| Elegir el cobrador según el medio | Aplicación | Factory | `new` en la ruta HTTP |
| SQL en la plantilla del kardex | Datos + vista | Repository + Template/Transform View; Service Layer | MVC degradado (controlador-dios) |
| Cobro y alta deben ir juntos | Datos | Unit of Work (transacción del ORM) | Observer asíncrono ingenuo |
| Correo y aviso a caja | Aplicación / integración | Observer (en proceso o cola) | Meter el SMTP en el controlador |
| Doble clic, dos cargos | Integración | Clave de idempotencia sobre el recurso de pago | Reintento ciego |
| App y kiosco con hambre distinta | Borde | BFF (y Gateway si hay varios servicios) | Factory «que falte» |
| Banco o CURP caídos; kardex vivo | Integración | Timeout + Circuit Breaker; no reintentar `400` | `await` infinito |
| Constancia PDF | — | Autenticación + consulta + vista; **no** Event Sourcing ni Redux | Golden Hammer |

**Orden razonable de pagar inscripción:**

`borde (Gateway) → Front Controller del marco → middleware (sesión, bitácora) → controlador delgado → Service Layer registrarPago → Strategy de medio + Adapter del banco → Unit of Work (pago + alta) → Observer del correo`.

Ningún paso es «la capa `=util/=`».