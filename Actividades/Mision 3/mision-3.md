# Misión 3 — Políticas transversales

## Hernández Saucedo Joel Josafat 20121000

1. ¿Cuándo corresponde Front Controller y por qué?
   - Cuando la misma política (sesión, bitácora, cupo) se repite en N controladores. El Front Controller concentra el despacho HTTP en un solo punto para que las políticas transversales vivan una sola vez.
   - No corresponde si la app es trivial y no hay políticas compartidas.
   - Vecino rechazado: Page Controller sin frontal, que es justo lo que produce la duplicación actual.

2. Mecanismo nativo del marco.
   - Spring: DispatcherServlet ya es el Front Controller; HandlerInterceptor y FilterChain dan la cadena.
   - Laravel: el kernel ya carga el framework y Router despacha; los Middleware se declaran en grupos de rutas.
   - Express: la instancia app o un Router montado con app.use(...) da el punto único; app.use(mw1, mw2, ...) da la cadena.
   - No se reescribe: sería un segundo frontal peleándose con el primero.

3. Orden de la cadena y por qué autenticar después del cobro es un error de diseño.
   - Orden: parseo/CORS → autenticación → autorización/cupo → bitácora → controlador → caso de uso.
   - Autenticar después del cobro rompe la atomicidad lógica:
     * No se sabe a quién cobrar, así que el cargo queda sin sujeto conciliable.
     * Se cobra a un desconocido: el portal se vuelve proxy de cobros al aire.
     * La bitácora queda sin identidad útil para auditoría.
     * El alta posterior no se puede atribuir a un expediente autenticado.
     * La clave de idempotencia no tiene a quién atar la unicidad.
   - Por eso no es un detalle de implementación: es un error de diseño.

4. Qué no debe hacer la cadena.
   - No debe conocer reglas de negocio (montos, horarios, validaciones de modelo).
   - No debe persistir entidades de negocio (pagos, altas). Sí puede escribir bitácora.
   - No debe construir respuestas de dominio; puede responder 401/403/429, no 201 con el recurso creado.
   - No debe orquestar el caso de uso; si llama al repo o al SMTP, se vuelve god middleware.
   - En una frase: la cadena deja pasar una petición bien formada, autenticada y autorizada, con su rastro; no decide qué hacer con ella. Las políticas transversales no son el reglamento.