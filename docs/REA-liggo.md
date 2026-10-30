# REA: Liggo (liggo.love)

Análisis de la app de referencia para construir un producto propio del mismo tipo.

Fuentes: web pública (HTML, `robots.txt`, `sitemap.xml`, bundles JS del frontend, páginas legales)
y fichas de las tiendas. No se entró a zonas con login ni se consultó su base de datos.
Revisado el 30-10-2026.

Leyenda: ✅ visto directamente · 🔶 inferido (hay que confirmarlo con cuenta o dentro de la app).

---

## 1. Qué es

**Asistente de respuestas con IA para ligar** (no es una app de citas). Subes una captura del chat
o de una story de Instagram, eliges un estilo y recibe varias respuestas listas para copiar.

| Dato | Valor | |
|---|---|---|
| Eslogan | "Lleva tus conversaciones al siguiente nivel" / "Tu asistente de IA para conquistar" | ✅ |
| Cifras que publican | 200k+ usuarios, 5M+ respuestas, valoración 4,8 (son cifras de marketing) | ✅ |
| Plataformas | iOS, Android y web app | ✅ |
| iOS | App Store `id6751428318` ("Liggo: Respuestas con IA") | ✅ |
| Android | `com.cupidoai.liggo`, lo que sugiere que la marca interna/empresa es "Cupido AI" | ✅ |
| Idiomas | Español (principal) e inglés; las páginas de descarga también están en italiano y francés | ✅ |
| Mercado | España + LATAM; legislación española (AEPD) | ✅ |
| Edad mínima | 18+ según los TOS (la tienda dice 9+) | ✅ |
| Contacto | `contacta@liggo.love` | ✅ |
| Redes | Instagram y TikTok `@liggo.love`; captación por TikTok/UGC | ✅ |

---

## 2. Funcionalidades

### Producto principal

| Función | Detalle | |
|---|---|---|
| **Responder a un chat** | "Sube tu chat": captura → la IA entiende el contexto → varias respuestas | ✅ |
| **Responder a una story** | "Sube captura de la historia a responder" → openers | ✅ |
| **5 modos/estilos** | Gracioso · Coquetear · Provocativo (vacilar) · Enamorado · **Salvada Épica** (rescatar un chat muerto) | ✅ |
| Varias respuestas | "Recibe múltiples respuestas… elige tu favorita"; se pueden editar | ✅ |
| Respuesta principal + alternativa ("flash") | Visto en el formulario interno de configuración de respuestas | ✅ |
| Usos más allá de ligar | En el FAQ: "cualquier conversación casual o social" | ✅ |

### Secciones de la web app (rutas privadas, sacadas de `robots.txt` y del bundle)

| Ruta | Qué parece ser | |
|---|---|---|
| `/cupido` | El asistente principal ("Cupido") donde se generan respuestas; tiene su propio experimento de paywall | ✅ ruta / 🔶 función |
| `/practicar` | **Práctica de conversación** con la IA | ✅ ruta / 🔶 función |
| `/simulador` | **Simulador de práctica**: una función premium según su FAQ ("funciones extra como el simulador de práctica") | ✅ |
| `/biblioteca` | Biblioteca de frases/openers ("abridores") | 🔶 |
| `/victorias` | Registro de "victorias" (citas o números conseguidos): gamificación o prueba social | 🔶 |
| `/profile`, `/settings` | Cuenta, plan, ajustes | ✅ |
| `/login`, `/register` | Email + contraseña o **Google** | ✅ |

### Back-office interno (rutas `/dashboard-*`)

Te muestra cómo opera el negocio por dentro:

- `dashboard-general`, `-negocio`, `-usuarios`, `-charts`, `-logs`, `-errores`: métricas y operaciones.
- `dashboard-experimentos`: **A/B tests del paywall** (confirmado: `/api/paywall/mobile-experiment` devuelve variante y flag).
- `dashboard-abridores` / `dashboard-analyze` / `dashboard-practicar`: curación de prompts, openers y análisis.
- `dashboard-articulos`: contenido SEO (las guías).
- `dashboard-influencers/*` (creators, calendar, videos, ranking, requests): **gestión de creadores UGC en TikTok**. Es su motor de adquisición.
- `dashboard-roadmap`.

---

## 3. Monetización

| Canal | Planes | |
|---|---|---|
| Paywall web (Stripe) | **Pro Semanal 4,99 $** · **Pro Anual 39,99 $** ("Mejor Oferta"), con botón "Restaurar Compras" | ✅ |
| App Store (según listados) | Semanal 7,99 $ · Mensual 14,99 $ (prueba de 3 días) · Anual 34,99 $ | ✅ (de las tiendas) |
| Gratis | "Empieza gratis, sin tarjeta". Premium = **uso ilimitado** + simulador | ✅ |
| Cuota | Tabla `usage_tracking` en Supabase, así que hay un límite de usos gratis | ✅ tabla / 🔶 límite |
| Anuncios | En la app móvil (según la ficha de la tienda) | ✅ |

Los precios son distintos en web y en la tienda (pagar en web evita la comisión de Apple y Google).
Optimizan el paywall con A/B tests permanentes.

---

## 4. Stack técnico

| Capa | Tecnología | Evidencia |
|---|---|---|
| Frontend web | **Next.js (App Router) con Turbopack**, páginas prerenderizadas con ISR | Cabeceras `x-powered-by: Next.js`, `x-nextjs-prerender`, chunks `turbopack-*.js` |
| Hosting | nginx delante (no es Vercel directo); cabeceras `x-id-fe` de un edge propio | Cabeceras |
| Auth + DB | **Supabase** (Auth con email y Google OAuth, Postgres) | Bundle y política de privacidad |
| Tablas vistas | `user_profiles`, `usage_tracking`, RPC `get_user_complete_state` | Bundle |
| API propia | Rutas de Next: `/api/checkout`, `/api/broadcast`, `/api/cupido/paywall-experiment`, `/api/paywall/mobile-experiment`, `/api/admin/...` | Bundle |
| Pagos web | **Stripe Checkout** (precios `price_...`) | Bundle y privacidad |
| Pagos móvil | StoreKit / Play Billing (probablemente vía RevenueCat o similar) | 🔶 |
| IA | **OpenAI, Anthropic y Google** (multi-proveedor) | Política de privacidad |
| Errores | **Sentry** | Bundle |
| Analítica | **DataFast** (datafa.st) + **Google Ads** con conversiones (`AW-18379635978`) | HTML |
| Animaciones | Lottie (`hero-new.json`) | HTML |
| Emojis | emoji-datasource-apple (jsDelivr) | Bundle |

**Privacidad (de su política):** las capturas "quedan asociadas a tu cuenta" (las guardan). Usan las
Cláusulas Contractuales Tipo para transferir datos fuera del EEE. Cookies de Supabase y Stripe como
imprescindibles; Google Ads con Consent Mode.

---

## 5. Captación (growth)

1. **UGC/influencers en TikTok** con back-office propio para gestionarlos.
2. **SEO programático**: `/guias/*` (te dejan en visto, respuestas para Tinder, WhatsApp e Instagram) en ES y EN.
3. **Páginas contra la competencia**: `/liggo-vs-bombiny` y `/alternativa-a-bombiny`. Bombiny es el competidor directo (coach de ligue web, unos 8k usuarios).
4. **`/descargar?store=ios|android&source=...`**: redirección con seguimiento por origen. Detecta navegadores dentro de apps (TikTok/IG) y enseña "toca los tres puntos → abrir en navegador".
5. Google Ads con conversión de compra.

---

## 6. Flujo de usuario

```
TikTok / Google ─► Landing ─► /descargar ─► App Store / Play
                        └─► /register (email o Google) ─► web app
App/web: onboarding ─► paywall (Semanal / Anual "Mejor Oferta", A/B test)
   └─► Cupido: [subir captura de chat | story] ─► elegir modo (5) ─► N respuestas ─► copiar/editar
   ├─► Practicar / Simulador (premium): entrenar conversación con IA
   ├─► Biblioteca de abridores
   └─► Victorias / Perfil / Ajustes
```

---

## 7. Oportunidades para tu app

1. **Precio y confianza:** guardan las capturas en tu cuenta. Puedes ofrecer "no guardamos tus capturas" como argumento de venta.
2. **Diferenciar los modos:** sus 5 estilos son genéricos. Puedes añadir "aprende tu estilo", tonos personalizados y respuesta según la etapa de la conversación.
3. **Coaching + respuesta:** Liggo da respuestas y Bombiny da coaching. Combinar ambos (respuesta + por qué funciona) es un hueco.
4. **Teclado del móvil:** responder sin salir de Tinder o IG. Ellos no lo tienen.
5. **Copiar su motor de growth (no su marca):** UGC en TikTok, guías SEO, páginas comparativas y A/B tests del paywall desde el día 1.
