# Plan MVP: Chispa (nombre provisional)

Asistente con IA para responder chats y stories, con funciones propias. Usa marca, textos y diseño
originales: de Liggo solo se toma la idea, no los textos, el logo ni la UI.

## Stack recomendado

| Capa | Elección | Por qué |
|---|---|---|
| App móvil | **Expo (React Native) + TypeScript** | iOS y Android con un solo código. Android es el hueco que deja Liggo |
| Web | **Next.js** (landing + app web + `/profile`) | Es lo que usa Liggo; SEO para landing y guías |
| Backend | **Supabase** (Auth, Postgres, Storage, Edge Functions) | Rápido de montar; las Edge Functions hacen de proxy al LLM |
| IA | LLM con visión vía API (p. ej. Claude) | Lee la captura directamente y genera respuestas en JSON |
| Pagos | **RevenueCat** (iOS/Android) + Stripe (web) | Gestión de suscripciones y pruebas gratis sin pelearse con StoreKit |
| Analítica | PostHog (feature flags + A/B del paywall) + Sentry | Liggo hace A/B del paywall constantemente |

## Modelo de datos mínimo

```
profiles(id, created_at, gender, interested_in, default_tone, locale, plan, plan_expires_at)
generations(id, user_id, created_at, kind[screenshot|story|text], tone, input_ref, output jsonb, liked_index)
usage_daily(user_id, date, count)
```

## Endpoint principal

`POST /functions/v1/generate`

```json
{ "kind": "screenshot", "image_url": "...", "tone": "gracioso", "context": "opcional" }
→ { "replies": ["...", "...", "..."] }
```

1. Comprobar la sesión y la cuota (`usage_daily`; plan gratis = N al día).
2. Llamar al LLM con visión: *prompt* de sistema (persona, tono, idioma, reglas de seguridad) + imagen.
3. Pedir la salida en JSON estricto con 3-5 respuestas cortas.
4. Guardar en `generations`. **Borrar la imagen** cuando pasen X horas (privacidad de terceros, RGPD).

## Precios de referencia (Liggo)

- Web (Stripe): 4,99 $/semana · 39,99 $/año.
- Tienda: 7,99 $/semana · 14,99 $/mes (prueba de 3 días) · 34,99 $/año.
- Idea: vender más barato en web (sin comisión de Apple) y llevar a los usuarios de TikTok a la web.

## Fases

1. **Semana 1:** auth, subir captura → 3 respuestas, copiar. Solo web (Next.js + Supabase).
2. **Semana 2:** tonos, stories, historial, `/profile`.
3. **Semana 3:** app Expo + RevenueCat + paywall + cuota gratis.
4. **Semana 4:** analítica, landing, publicación en TestFlight y Play (pruebas internas).
5. **Growth:** `/descargar` con `source` y detección del navegador de TikTok/IG, guías SEO, página "vs competencia", panel de creadores UGC.

## Ideas de features propias (diferenciación)

- **Coach de conversación:** analiza todo el chat y da una puntuación de interés y consejos ("está respondiendo corto, cambia de tema").
- **Teclado iOS/Android:** genera respuestas sin salir de la app de chat.
- **Aprende tu estilo:** usa tus mensajes anteriores para que las respuestas suenen a ti.
- **Mejora tu perfil:** análisis de fotos y bio de Tinder/Hinge.
- **Plan para la cita:** ideas de planes según la conversación.
- **Modo amigos/trabajo:** no solo para ligar (aumenta el mercado).
- **Precio más justo** que la competencia y sin anuncios intrusivos.

## Legal y tiendas (importante)

- Política de privacidad clara: las capturas contienen **datos de terceros**. Mínima retención y sin entrenar modelos con ellas.
- Apple 5.1.1 / 3.1.2: el paywall necesita un botón de cerrar visible y los términos de la suscripción a la vista.
- No usar "Liggo", su logo ni sus textos. No decir que la app tiene relación con Instagram, Tinder o WhatsApp.
- Filtros de seguridad: nada de respuestas acosadoras ni contenido sexual con menores; bloquear insistencia tras un "no".
