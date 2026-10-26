# Quiz Normalibs® "Apunta a ti"

Quiz de 4 preguntas que recomienda Normalibs® Cápsulas (adultos) o Sobres (niños), Después de las preguntas muestra cómo actúa Normalibs® semana a semana y ofrece desbloquear 10% extra con el correo. El resultado incluye lo que más le afecta, tips de alimentación, su presentación y el Plan Paciente 2+1 en GoPharma Care (con el código ya aplicado si lo desbloqueó).

- Un solo archivo `index.html` con HTML, CSS y JavaScript. No necesita compilación ni dependencias.
- Única dependencia externa: la tipografía Montserrat desde Google Fonts.
- Pensado primero para móvil, con modo claro y oscuro.
- Revisado contra WCAG 2.2 AA (contraste, áreas táctiles, lectores de pantalla y movimiento reducido).

## Flujo

Inicio → 4 preguntas → "Conozca Normalibs® Cápsulas/Sobres" → "Normalibs® actúa de forma progresiva" → "Desbloquee 10% extra" (solo el correo, sin casilla) → resultado.

## Estructura

```
normalibs-quiz/
├── index.html
├── README.md
├── emails/                     Correo 1 (código), correo 2 (recordatorio), imágenes y guía
└── assets/
    ├── petri.webp                  Arte de cultivo (portada y resultado)
    ├── para-mi.webp                Foto "Para mí"
    ├── para-mi-hijo.webp           Foto "Para mi hijo o hija"
    ├── normalibs-capsulas.webp     Caja de Cápsulas
    ├── normalibs-sobres.webp       Caja de Sobres
    ├── normalibs-capsulas-caja.webp  Caja de Cápsulas recortada (pantalla "Conozca")
    ├── normalibs-sobres-caja.webp    Caja de Sobres recortada (pantalla "Conozca")
    ├── normalibs-logo.webp         Logo en color (modo claro)
    ├── normalibs-logo-blanco.webp  Logo blanco (modo oscuro y portada)
    ├── innova-blanco.webp          Logo Innova
    └── novonesis-blanco.webp       Logo Novonesis
```

## Cómo publicarlo

Elijan una de estas opciones:

1. **Página propia (recomendado):** suban la carpeta completa a la misma hosting de la landing, por ejemplo en `normalibs-centroamerica.vercel.app/quiz`. En Vercel basta con poner la carpeta en `/public/quiz/` o en la raíz de un proyecto nuevo.
2. **Dentro de una página existente:** inserten el quiz con un iframe.
   ```html
   <iframe src="/quiz/index.html" title="Quiz Normalibs" style="width:100%;height:100vh;border:0"></iframe>
   ```
3. **Shopify (GoPharma Care):** suban las imágenes a *Contenido → Archivos*, creen una plantilla de página y peguen el contenido de `index.html`. Después reemplacen las rutas `assets/...` por las URLs que da Shopify.

## Configuración

Todo lo editable está en el bloque `CONFIG`, al inicio del `<script>` en `index.html`:

```js
const CONFIG={
  store:"https://gopharmacare.com",
  coupon:{pct:10,code:"QUIZ10"},
  klaviyo:{publicKey:"",listId:""},
  leadEndpoint:"",
  showTeamPanel:false
};
```

| Campo | Para qué sirve |
|---|---|
| `store` | Dominio de la tienda Shopify. |
| `coupon` | Porcentaje y código que se muestran al desbloquear. **El código debe existir en Shopify:** *Descuentos → Crear descuento → Código*, con 10% sobre los productos Plan Paciente 2+1 y un uso por cliente. |
| `klaviyo` | Clave pública y lista de Klaviyo. Con esto, cada correo del quiz dispara los correos automáticos. Ver `emails/GUIA-CORREOS.md`. |
| `leadEndpoint` | URL que recibe el correo y las respuestas por `POST` en JSON. Puede ser un webhook de Klaviyo, Mailchimp, HubSpot, Zapier o Make. Si se deja vacío, no se envía nada. |
| `showTeamPanel` | En `true` muestra el panel "Vista del equipo", con los datos del lead y la secuencia de correos. Es solo para revisión interna: en producción debe estar en `false`. |

### Datos que se envían a `leadEndpoint`

```json
{
  "email": "nombre@correo.com",
  "consent": true,
  "para": "adulto | hijo",
  "presentacion": "capsulas | sobres",
  "molestia_principal": "hinchazon | molestias | ritmo | varias",
  "momento": "comidas | trabajo | planes | estres | escuela | comer | paseos | nervios",
  "diagnostico": "si | sospecha | no",
  "sintomas": ["dolor", "distension", "gases", "heces", "urgencia", "estres"],
  "cupon": "QUIZ10",
  "fecha": "2026-10-15T20:00:00.000Z"
}
```

Con esos campos se segmenta la secuencia de email marketing:
- **Inmediato:** recomendación, tips y código.
- **Día 1:** recordatorio con sus síntomas.
- **Día 3:** "3 meses de constancia".
- **Día 7:** "su código vence pronto".

La secuencia se detiene cuando la persona compra.

## Enlaces de compra

- **Pago único:** va directo al checkout con el producto ya en el carrito (`/cart/{variante}:1`).
  - Cápsulas: variante `46664448442567`, ¢48.000.
  - Sobres: variante `46958985576647`, ¢63.000.
- **Plan fraccionado:** va a `/pages/contact`, porque lo coordina el equipo de GoPharma Care.
- **Con cupón:** el mismo checkout, con `&discount=QUIZ10` para que el código quede aplicado solo.
- **Medición:** todos los enlaces llevan UTM (`utm_source=quiz_normalibs`, `utm_campaign=apunta_a_ti`, `utm_content=presentacion_modo`), así que las ventas del quiz se pueden filtrar en Shopify Analytics.

Si cambian precios o variantes en Shopify, actualicen el objeto `PRES` en `index.html`.

## Analítica

El quiz envía eventos a `window.dataLayer`, compatible con Google Tag Manager, GA4 y Meta Pixel vía GTM:

| Evento | Cuándo |
|---|---|
| `quiz_complete` | La persona llega al resultado. Incluye `para`, `presentacion`, `molestia_principal`, `momento`, `diagnostico` y `sintomas`. |
| `quiz_lead` | Deja su correo y desbloquea el cupón. |
| `quiz_checkout_click` | Toca un botón que lleva a GoPharma Care. |
| `quiz_buybar_click` | Toca el botón "Comprar −10%" del encabezado en el resultado. |

## Pendientes antes de lanzar

- [ ] Crear el código `QUIZ10` en Shopify.
- [ ] Conectar Klaviyo (`CONFIG.klaviyo`) y crear el flujo de correos según `emails/GUIA-CORREOS.md`.
- [ ] Revisar con Legal el formulario de correo: no tiene texto de consentimiento (Ley 8968). Si lo piden, se agrega una línea bajo el botón.
- [ ] Confirmar con Regulatorio que la página puede ir sin la leyenda "suplemento alimenticio".
- [ ] Validar con Regulatorio el fraseo de beneficio y los tips de alimentación.
- [ ] Instalar GTM o Pixel en la página donde se publique el quiz.
