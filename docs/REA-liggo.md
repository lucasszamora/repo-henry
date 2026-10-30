# REA: Liggo (liggo.love)

Análisis de la app de referencia para construir un producto propio del mismo tipo.

> **Estado:** primera versión. Solo usa información pública de búsquedas (fichas de App Store y agregadores).
> El sitio `liggo.love` y `apps.apple.com` estaban bloqueados por la red de la sesión, así que
> **todavía no se analizó la web real** (rutas, bundle JS, API, flujo de pago). Eso queda en
> [Pendiente](#pendiente-requiere-acceso-a-liggolove).

---

## 1. Qué es (verificado)

| Dato | Valor | Fuente |
|---|---|---|
| Nombre en App Store | "Liggo: Responder con IA" / "Liggo: Frases para Ligar IA" | App Store `id6751428318` |
| Categoría real | **Asistente de respuestas con IA para ligar**. No es una app de citas | Ficha de la tienda |
| Propuesta | Ganar confianza al chatear y ligar; respuestas originales a *stories* para romper el hielo | Ficha de la tienda |
| Integración | Funciona con Instagram, Tinder, WhatsApp y cualquier app de chat **subiendo capturas de pantalla** | Agregador appshunter |
| Plataformas | iOS + web (`liggo.love`, con ruta `/profile`) | App Store + usuario |
| Legal | Términos en `liggo.love/tos` | Ficha de la tienda |
| Edad | 9+ | Ficha de la tienda |
| Publicador | Figura como "VIRAL APPS SL" en un listado (sin confirmar) | Búsqueda |
| Idioma / mercado | Español (España / LATAM) | Textos de la ficha |
| Reseñas | Muy pocas (de 3 a unas 28 según la tienda) y mezcladas | Tienda |

### Monetización (verificado)

- Gratis con compras dentro de la app, **más anuncios**.
- Suscripción **Premium**:
  - Semanal: **7,99 $**
  - Mensual: **14,99 $** (prueba de 3 días)
  - Anual: **34,99 $**
- El precio semanal es caro a propósito para que el anual parezca barato: 34,99 $ al año contra unos 415 $ al año pagando semanal. Es el patrón típico de las apps "viral" con paywall en el onboarding.

---

## 2. Mapa de funcionalidades

Leyenda: ✅ verificado · 🔶 inferido por el tipo de app (comparado con Rizz, YourMove, Plug AI…). Hay que confirmarlo con el análisis de la web.

| # | Función | Estado | Descripción |
|---|---|---|---|
| F1 | Responder desde captura de pantalla | ✅ | El usuario sube una captura de un chat (IG, Tinder, WA). La IA lee la conversación (OCR o visión) y propone respuestas |
| F2 | Responder a *stories* | ✅ | Sube una story y la IA genera una respuesta original para abrir conversación |
| F3 | Frases para ligar / openers | ✅ (por el nombre) | Frases de entrada generadas o de una biblioteca |
| F4 | Tonos de respuesta | 🔶 | Gracioso, coqueto, atrevido, romántico, serio… |
| F5 | Varias opciones por petición | 🔶 | Normalmente de 3 a 5 respuestas, con botones de copiar y regenerar |
| F6 | Contexto manual | 🔶 | Escribir a mano lo que te dijeron, sin captura |
| F7 | Perfil de usuario (`/profile`) | ✅ (la ruta existe) | Cuenta, suscripción y probablemente preferencias (género, a quién busca, estilo) |
| F8 | Límite gratis + paywall | 🔶 | X respuestas gratis por día; luego paywall con los 3 planes |
| F9 | Anuncios | ✅ | Para usuarios gratis (probablemente intersticiales o con recompensa) |
| F10 | Historial | 🔶 | Conversaciones o respuestas guardadas |

---

## 3. Flujos de usuario (reconstruidos 🔶)

```
Onboarding (3-5 pantallas: valor → género/preferencias → "¿qué quieres mejorar?")
   └─► Paywall (prueba de 3 días del plan mensual, con la X de cerrar poco visible)
          └─► Inicio
                ├─ [Subir captura] → recorte/confirmación → elegir tono → N respuestas → copiar
                ├─ [Responder story] → subir imagen → N openers → copiar
                ├─ [Escribir contexto] → texto → N respuestas
                └─ Perfil → plan, idioma, preferencias, cerrar sesión, borrar cuenta
```

---

## 4. Arquitectura probable (🔶)

- **Cliente:** app iOS nativa o multiplataforma (React Native/Flutter) + web en `liggo.love`.
- **IA:** un LLM con visión (lee la captura directamente, sin OCR aparte) y un *prompt* de sistema
  con tono y persona. Ninguna parte necesita un modelo propio.
- **Backend:** API ligera (auth, cuota diaria, proxy al LLM para no exponer claves).
- **Pagos:** StoreKit en iOS (seguramente vía RevenueCat o Adapty) y Stripe en web.
- **Anuncios:** AdMob o similar.

---

## 5. Puntos débiles detectados (tu oportunidad)

1. **Pocas reseñas y valoración baja:** poca retención o calidad de respuesta irregular.
2. **Anuncios + suscripción cara:** experiencia agresiva; un plan gratis más generoso o un precio único compite bien.
3. **Solo iOS** (en las tiendas): Android está libre.
4. **Producto genérico:** cualquier "rizz app" hace lo mismo. Diferenciarse con funciones (ver `PLAN-MVP.md`).

---

## Pendiente (requiere acceso a liggo.love)

Cuando el dominio esté permitido en la red de la sesión:

- [ ] Recorrer la web: rutas (`/`, `/profile`, `/tos`, `/privacy`, login, pricing).
- [ ] Framework (Next.js, Vite, Flutter web…) por HTML, cabeceras y bundle.
- [ ] Endpoints de API llamados desde el front (auth, generate, billing).
- [ ] Proveedor de auth (Firebase, Supabase, Clerk…) y de pagos (Stripe o Paddle).
- [ ] Analítica y SDKs (GA, Meta Pixel, Mixpanel, PostHog…).
- [ ] Textos reales del onboarding y del paywall; límites del plan gratis.
- [ ] Leer TOS y política de privacidad (qué hacen con las capturas).

## Fuentes

- https://apps.apple.com/app/id6751428318
- https://appshunter.io/ios/app/liggo-frases-para-ligar-ia/id6751428318
