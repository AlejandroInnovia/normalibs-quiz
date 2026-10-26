# Correos del quiz Normalibs®

Dos correos automáticos para las personas que dejan su correo en el quiz:

| Correo | Cuándo sale | Qué lleva |
|---|---|---|
| `correo-1-codigo.html` | Apenas deja su correo | Su recomendación (Cápsulas o Sobres), el código QUIZ10, un tip para cada síntoma que marcó y el botón de compra con el código aplicado |
| `correo-2-recordatorio.html` | 2 días después, **solo si no compró con QUIZ10** | Cómo puede ayudarle Normalibs® con su molestia principal, el recorrido (semana 1, 62% a la semana 4, 97% a los 3 meses), su oferta y el plan fraccionado |

Los dos están hechos para **Klaviyo**, la plataforma de email más usada con Shopify. Klaviyo sabe si la persona compró y con qué código, y eso es lo que permite mandar el recordatorio solo a quien no lo usó.

## 1. Conectar el quiz con Klaviyo (una vez)

1. En Shopify, instale la app **Klaviyo** y conecte la tienda gopharmacare.com. Así Klaviyo recibe los pedidos y los códigos usados.
2. En Klaviyo, cree una lista llamada **Quiz Normalibs** y copie su **ID de lista** (Listas → la lista → Configuración).
3. Copie la **clave pública** de 6 caracteres (Configuración → Claves API → Clave pública). Es pública por diseño y puede ir en la página.
4. En `index.html`, llene el bloque `CONFIG`:
   ```js
   klaviyo:{publicKey:"AbC123",listId:"XyZ789"},
   ```
Desde ese momento, cada persona que deja su correo queda en la lista y se registra el evento **"Quiz Normalibs completado"** con sus respuestas.

## 2. Subir las imágenes

Suba los archivos de `emails/img/` a Klaviyo (Contenido → Biblioteca de imágenes) y reemplace en cada HTML las rutas `img/...` por la dirección que le da Klaviyo. Si el quiz se publica en Vercel, también sirven las direcciones de esa página (`https://su-dominio/emails/img/...`).

## 3. Crear el flujo

Klaviyo → Flujos → Crear desde cero → nombre "Quiz Normalibs":

1. **Disparador:** Métrica → `Quiz Normalibs completado`.
2. **Correo 1:** Plantilla → Importar HTML → pegue `correo-1-codigo.html`. Envío inmediato.
   - Asunto: `Su recomendación Normalibs® y su 10% extra`
   - Prueba A/B: `Su código QUIZ10 ya está listo`
3. **Espera:** 2 días.
4. **División condicional:** "Lo que alguien ha hecho" → `Placed Order` con `Discount Codes` contiene `QUIZ10`, **cero veces desde que inició este flujo**.
   - Rama **Sí** (no ha comprado): sigue al Correo 2.
   - Rama **No** (ya compró): termina.
5. **Correo 2:** importe `correo-2-recordatorio.html`.
   - Asunto: `Su 10% extra sigue disponible`
   - Prueba A/B: `Normalibs® puede ayudarle a aliviar {{ event.molestia_texto }}`
6. En la configuración del flujo, active **"Salir del flujo al hacer un pedido"** (filtro de flujo: `Placed Order` cero veces desde el inicio del flujo).

Antes de activarlo, use **Vista previa → Evento de ejemplo** para ver un correo con datos reales y mándese una prueba.

## Variables que llegan del quiz

| Variable | Ejemplo |
|---|---|
| `event.para` | `adulto` o `hijo` |
| `event.presentacion` / `event.presentacion_nombre` | `capsulas` / `Cápsulas` |
| `event.molestia_texto` | `la inflamación o hinchazón` |
| `event.sintomas_detalle` | Lista de `{titulo, tip}`, uno por síntoma marcado |
| `event.cupon` / `event.cupon_pct` | `QUIZ10` / `10` |
| `event.precio` / `event.precio_con_codigo` | `¢48.000` / `¢43.200` |
| `event.link_compra` | Checkout con el producto en el carrito y QUIZ10 aplicado |
| `event.link_fraccionado` | Página para solicitar el plan fraccionado |

## Antes de activar

- [ ] Crear el código **QUIZ10** en Shopify (Descuentos), 10% sobre Plan Paciente 2+1.
- [ ] Revisar con Regulatorio los textos de beneficio (62%, 97%, "puede ayudarle a aliviar").
- [ ] Revisar con Legal el consentimiento de marketing (Ley 8968).
- [ ] Llenar en Klaviyo el nombre y la dirección de la empresa (salen en el pie de cada correo, es obligatorio).
