# Comfy Workshop — Plan V1

**Estado:** borrador para la reunión de seguimiento del 2 de septiembre · **Autora:** Mariana (con ayuda de Claude) · **Última actualización:** 1 sep 2026

## 1. Fuentes

| Fuente | Qué aporta |
| --- | --- |
| [Comfy Workshop Kickoff (Fireflies, 1 sep, 58 min)](https://app.fireflies.ai/view/01M1CTE3WPAS98Z2DF3CCQA999) — Eric Sun (DRI), Robin, Rob, Maanil, Deep, Mariana, Bryan | Decisiones de scope V1. **Manda sobre el PRD cuando se contradicen.** |
| [PRD: Comfy Workshop (Notion)](https://app.notion.com/p/comfy-org/PRD-Comfy-Workshop-3c96d73d3650812ea4a8cdcb73a00633) | IA, user journeys, workstreams, métricas, riesgos. Fases 0–5. |
| [Workshop launch use-case map & workflow gaps](https://app.notion.com/p/3cd6d73d36508138b6b3c2e3fb880a21) | Taxonomía de use cases, P0/P1, cobertura del catálogo actual. |
| [Detailed Requirements: comfy.org Home — Playground & Hub (Robin, 27 ago)](https://app.notion.com/p/3c96d73d3650814f8f78c843a57da394) | Contratos técnicos F1–F12, milestones M1–M3, "ground truth" de repos y APIs. Base del TDD de Maanil. |
| [TDD: Workflow Hub Frontend Migration into comfyui_frontend](https://app.notion.com/p/38d6d73d365080679bb6cfd0b21fe616) · [GTM-359](https://linear.app/comfyorg/issue/f990586a-c622-400e-bf3d-e6b195917815) | Plan previo de Maanil para mover el Hub al monorepo. |
| Prototipo: <https://deployment-gallery.vercel.app/> | Referencia visual (no es el diseño final). |

## 2. Qué es V1 (scope cerrado en el kickoff)

V1 = lo que se lanza con el GA del Developer Platform. Dos productos detrás: **Models API** (Router, partner nodes vía SDK) y **Comfy API / serverless** (workflows desplegados como endpoint). Workshop es la puerta de entrada: browse → run → sign up → activación en los dos productos.

### 2.1 Hito 1 — Models playground

| Tema | Decisión |
| --- | --- |
| Fecha | PRD dice **15 sep**. Robin pidió **viernes 11 sep** como target y "intentar tenerlo este viernes" (4 sep). Eng valida viabilidad. |
| Catálogo | **Solo modelos que soporte Router** (todos partner nodes, "cientos"). Open source (LTX 2.5, MiniMax…) queda para un workstream aparte de Router; la página debe actualizarse sola con lo que Router exponga. |
| Página de modelo | Tabs **Playground · API · Examples**. Form de parámetros, upload, Run, output. Snippets Python / TypeScript / HTTP del Router SDK. |
| Auth | Página pública. Para **Run** hace falta sign in (cuenta Cloud). Header de comfy.org gana **Sign In** y estado logueado (avatar + créditos + workspace). |
| Créditos | **Sin free runs.** Si no hay créditos → comprar. **Billing fuera de scope**: redirigir a platform.comfy.org para comprar y volver. Cada modelo muestra su **precio en créditos** antes de correr. |
| Outputs | **No se guardan** en galería ni historial de servidor. Descargar. (Detailed Reqs: historial solo en localStorage, links expiran a 24 h). |
| Implementación | Robin: "usa el TypeScript SDK contra Router, y que viva en el repo frontend como ejemplo open source". Detailed Reqs matiza: el navegador no puede guardar API keys, así que los runs van por un **proxy autenticado con workspace JWT en cloud.comfy.org** (F5); el SDK es lo que enseñan los snippets. Maanil lo cierra en el TDD. |

### 2.2 Hito 2 — Workflows / apps

| Tema | Decisión |
| --- | --- |
| Fecha | **GA 30 sep**, "idealmente antes" (sin fecha aún; se estimará tras el TDD). |
| Contenido | **Solo 1P curado.** Nada de 3P, ratings, marketplace. |
| Qué es una "app" | **No es App Mode.** Una app se construye con dos primitivas — **modelos** y **workflows** — sin restricciones: puede ser un workflow, varios, o modelos + workflows. UI bespoke por caso de uso, con equilibrio: flexibilidad sin asustar (Eric). |
| Hero apps | Rob propone **3 ideas** tipo Higgsfield, muy genéricas ("todo el mundo quiere esto": video extend, etc.), calidad alta, ~2 semanas. El resto del catálogo Hub se migra con un agente eligiendo qué parámetros exponer. |
| Calidad vs cloud | **Máxima calidad de output** (partner nodes). **No hace falta que sea cloud-compatible**; custom nodes OK. Robin: "going forward we don't care about cloud at all". |
| Presets | Sí, estáticos (GCS o GitHub). Rob y Maanil acuerdan **formato JSON** para que el frontend los lea. |
| Run | Igual que modelos: sign in + créditos. Output no se guarda. |
| CTA secundario | **Copy / Deploy to Developer Platform** (solo workflows, nunca en modelos). Copia **build + deployment** al workspace del usuario en platform.comfy.org; el usuario lo ve en su lista de deployments, configura GPU/concurrencia y redeploya como endpoint propio. **No** se copia el workflow, **no** clone-to-Desktop, **no** save-to-Cloud, **no** ver el node graph. Dev Platform construye un endpoint "copy any deployment". |
| Wording | Robin: "Deploy to developer platform" / "Deploy your own endpoint". PRD: "Deploy your endpoint". Serverless se rebrandea a **Comfy API** ("un endpoint que puedes llamar y que escala solo"). |

### 2.3 Transversal

- **Landing y navegación:** el Workshop **no vive en la homepage**; hay una sección/entrada que lleva a él. Header con entrada(s) a modelos y workflows (misma página, distintas tabs). Homepage a limpiar (redundancias). Usuario también puede aterrizar directo por SEO. Mariana explora la IA.
- **Reemplazo, no añadido:** el Hub actual (`comfy.org/workflows`) y la página de supported models se **borran** al final ("blow all of this away"). El playground interno de platform (feature flag) también se borra.
- **Analytics:** funnel en PostHog — visitas → sign in → run → conversión. GTM va dentro del lanzamiento del Developer Platform, sin plan aparte.
- **Nombre:** code name **Workshop**; Yoland prefiere **Hub**. No decidido, no bloquea (Eric).
- **Proceso:** Maanil escribe **TDD** → consenso → tickets Linear. Reunión de flujos **2 sep**, **daily standups** desde el 3 sep.

### 2.4 Fuera de V1

Free runs · 3P publish/marketplace · ratings/comments · clone to Desktop · save to Cloud · guardar outputs/galería pública · billing nuevo · modelos open source en Router · unificar platform con cloud.

## 3. Contradicciones a resolver (PRD vs reunión vs Detailed Reqs)

1. **Free runs.** El resumen, la tabla de milestones y el FAQ del PRD siguen diciendo "5 free runs"; la sección *Confirmed Decisions* y la reunión dicen **no**. Actualizar el PRD para que nadie diseñe con free runs.
2. **Clone to Desktop / Save to Cloud.** Milestone 2 del PRD y *Confirmed Decisions* ("Save to the user's Comfy Cloud account") vs reunión ("no copy to cloud", "don't worry about cloud"). V1: **solo Deploy to Developer Platform**.
3. **Rutas.** PRD: `/workshop`, `/workshop/models/[slug]`, `/workshop/workflows/[slug]`. Detailed Reqs (D1): `/models/{provider}/{model}` con 301 desde `/p/supported-models`. Decidir antes de que eng empiece (SEO, redirects del Hub).
4. **Face swap como hero app.** Robin lo citó como ejemplo; el use-case map lo **excluye por brand safety** ("SFW identity only"). Rob debe elegir otros ejemplos (video extend, camera-motion presets, product ad, restore photos…).
5. **Cómo corre el playground.** "TS SDK desde el frontend" (reunión) vs proxy con workspace JWT (Detailed Reqs F5). Afecta a cloud backend y a la fecha del 11 sep.
6. **Decisión del 12 sep (save-format → API-format).** Sigue en el PRD pero V1 ya no necesita snippets genéricos de workflow: las apps 1P se despliegan como deployments preparados. Confirmar que se puede quitar del critical path.

## 4. Mi parte (diseño) — entregables y orden

Acordado en la reunión: **user flows primero, UI después**. Robin quiere mañana una visión de alto nivel de la experiencia para poder arrancar eng (sign in, etc.).

### 4.1 Para la reunión del 2 sep

1. **Mapa de flujos V1** (este doc, sección 5) con puntos de entrada, estados críticos y decisiones abiertas.
2. **IA propuesta** (sección 5.1): dónde vive Workshop respecto a homepage y header.
3. **Lista de estados** que eng necesita contratar (sección 5.4).
4. **Preguntas** para cerrar en la reunión (sección 8).

### 4.2 Después (hasta 11 sep, hito 1)

- Wireframes de: header (signed out / in), homepage section, `/workshop` tab Models, model detail (3 tabs), modal sign in, estados de créditos, estado running/result/error.
- Handoff a eng con specs responsive y accesibilidad (workstream 1 del PRD).
- Design QA sobre la rama de eng.

### 4.3 Después (hasta 30 sep, hito 2)

- Tab Workflows: categorías por use case (Create / Edit & enhance / Specialized outcomes), cards con precio.
- App detail: showcase → playground bespoke (con Rob, por app) → CTA Deploy.
- Flujo Deploy to Developer Platform (modal de confirmación → platform).
- Terminología final (Workshop/Hub, Deploy…).

## 5. Flujos V1 propuestos

### 5.1 Puntos de entrada e IA

```
comfy.org (homepage)            → sección "Explore the Workshop" (featured models + apps) → /workshop
Header (todas las páginas)      → WORKSHOP (primera posición) · Sign In / avatar+créditos
Header menú Products            → Models / Workflows (misma página, distinta tab)
Búsqueda / SEO                  → /workshop/models/[modelo] o /workshop/workflows/[app] directo
platform.comfy.org              → link de vuelta al Workshop ("try more models")

/workshop                       → tabs Models | Workflows
/workshop/models                → grid + búsqueda + filtros (modality, provider, task)
/workshop/models/[slug]         → Playground | API | Examples
/workshop/workflows             → categorías por use case + grid
/workshop/workflows/[slug]      → Showcase → Playground (bespoke) → Deploy CTA
```

Recomendación: mantener el prefijo `/workshop/...` del PRD y usar `{provider}/{model}` como slug de modelo (coincide con el id de Router). 301 desde `/models`, `/p/supported-models/*` y `/workflows/*` cuando se apague el Hub.

### 5.2 Flujo A — Probar un modelo (hito 1)

1. Llega a `/workshop` (signed out). Ve cards con thumbnail, nombre, provider, modalidad, **precio por run**.
2. Abre un modelo. Tab Playground: form generado del schema (prompt, uploads, sliders, seed), panel de resultado con ejemplo.
3. Pulsa **Run** → botón dice **"Sign in to run"**. Modal de sign in **en la misma página** (Google / GitHub / email). El form **no se pierde**.
4. Tras sign in **no se auto-ejecuta**: aviso "You're signed in — hit Run when ready". Header pasa a avatar + créditos + workspace.
5. Comprobación de créditos:
   - **Saldo suficiente** → Run → estado *running* (timer, cancel) → resultado (imagen/vídeo/audio), créditos usados, Download, "Use these settings in code".
   - **Saldo insuficiente / cero** → botón **"Buy credits to run"** → platform.comfy.org (top-up) → vuelve a la misma página con el form intacto → Run.
   - **Workspace con provider deshabilitado** → Run deshabilitado, "Disabled by your workspace policy".
6. Tab API: snippet Python / TS / HTTP con los valores del form; CTA "Get API key" → platform.comfy.org/profile/api-keys.
7. Tab Examples: outputs de ejemplo con "Open in Playground".

### 5.3 Flujo B — Probar y desplegar una app (hito 2)

1. `/workshop/workflows`: categorías (Create images/videos · Edit images/videos · Enhance · Identity & characters · Production assets), cards con precio estimado por run.
2. App detail: **Showcase** (outputs, descripción, qué modelos usa) → **Playground** bespoke (inputs propios de la app, presets estáticos) → Run (mismo flujo de auth/créditos que A).
3. CTA secundario **Deploy to Developer Platform**:
   - Signed out → sign in.
   - Modal de confirmación: qué se copia (build + deployment editable en tu workspace), que tendrá coste de infra, nombre.
   - Confirmar → platform.comfy.org → aparece en Deployments → configurar GPU/concurrencia → deploy → endpoint propio.
   - Volver al Workshop desde platform.
4. Cross-links: modelo → "Workflows using this model"; app → "Models in this app".

### 5.4 Estados críticos que eng debe soportar

| Superficie | Estados |
| --- | --- |
| Header | signed out · signed in (avatar, créditos, workspace switcher, sign out) · saldo cero |
| Catálogo | loading · vacío por filtro · modelo degraded/deprecated · sin thumbnail (placeholder) |
| Form | validación por campo · error 422 mapeado a campo · upload > 25 MB / tipo no permitido · popup bloqueado (Safari) → redirect con restauración |
| Run | signed out · running (hasta 600 s en vídeo, sin cola) · cancel · éxito · error de provider · rate limit (429) · policy (403) · sin créditos (402) · modelo no disponible |
| Resultado | expira a 24 h (banner) · NSFW blur click-to-reveal · descarga |
| Deploy | signed out · sin acceso a platform (waitlist) · copia OK · copia fallida |

### 5.5 Instrumentación (PostHog)

Eventos mínimos: `workshop_viewed`, `model_viewed`, `run_clicked {signed_in}`, `signin_wall_shown`, `signin_completed`, `topup_started/completed`, `run_succeeded/failed`, `snippet_copied`, `deploy_clicked`, `deploy_completed`. Todos con `surface`, `workspace_id`, UTMs (`utm_source=comfy_org`, `utm_campaign=dev_platform`). Detalle en Detailed Reqs F11.

## 6. Workstreams, owners y fechas

| # | Workstream | Owner | Target |
| --- | --- | --- | --- |
| 1 | Product design & experience (IA, flujos, estados, handoff, QA) | **Mariana** | Hito 1: 11–15 sep · Hito 2: 30 sep |
| 2 | Workshop surface & discovery (nav, homepage, landing, listados, detalle, SEO, redirects) | **Maanil** (eng DRI; TDD → tickets). FE: Shichi o Ben Cooley (Deep ocupado, aconseja) | Fundación 15 sep · completo 30 sep |
| 3 | Model playground & Router (catálogo, form desde OpenAPI, proxy, output, precio, snippets) | por asignar (site + cloud backend) | 11–15 sep |
| 4 | Identity, workspace & billing (sign in cross-domain, workspace, saldo, compra) | por asignar (cloud) | 15 sep |
| 5 | Workflow runtime & Dev Platform handoff (run de apps, endpoint "copy deployment") | por asignar (Dev Platform) | 30 sep |
| 6 | Catálogo y contenido (3 hero apps, migración del Hub, presets, precios) | **Rob** | 3 ideas esta semana · 30 sep |
| 7 | Measurement & attribution (PostHog) | por asignar | core 15 sep · funnel 30 sep |
| 8 | Launch readiness & GTM (QA, **docs**, pricing, copy, soporte) | Eric + PMM | 15 sep / 30 sep |

**Calendario**

| Fecha | Qué |
| --- | --- |
| Mar 1 sep | Kickoff ✅ |
| Mié 2 sep | Reunión de flujos/diseño (Mariana presenta). Maanil arranca TDD. Rob: 3 app ideas. |
| Jue 3 sep → | Daily standups. |
| Vie 4 sep | Aspiracional de Robin para el models playground. |
| Vie 11 sep | Target models playground. |
| Vie 12 sep | Decision point del PRD (API-format) — confirmar si sigue aplicando. |
| Mar 15 sep | Fase 0+1 según PRD (nav, sign in, homepage, /workshop models). |
| Vie 26 sep | M2 Detailed Reqs (metadata dinámica de Router, SEO completo). |
| Mié 30 sep | **GA**: workflows/apps + Deploy to Developer Platform. |

## 7. Dónde vive el código y cómo trabajar

### 7.1 Mapa de repos (verificado)

| Superficie | Repo / ruta | Stack |
| --- | --- | --- |
| comfy.org (marketing, homepage, header, `/models`, `/p/supported-models`) | `Comfy-Org/ComfyUI_frontend` → **`apps/website`** | Astro 7 + Vue islands + Tailwind, static, Vercel |
| Comfy Hub (`comfy.org/workflows/*`) | `Comfy-Org/workflow_templates/site` (Astro), enrutado por un Cloudflare Worker (`Comfy-Org/comfy-router`) | Astro; lee Hub API en cloud.comfy.org |
| Editor / cloud.comfy.org (auth, workspaces, billing) | `ComfyUI_frontend` raíz `src/` | Vue SPA |
| platform.comfy.org (deployments, API keys, billing) | repo `platform` | — |
| Router (modelos partner) | `api.comfy.org/v1/models…`, spec en `comfy-python-sdk` | — |
| Docs (este repo) | `Comfy-Org/docs` (fork `marianamartinho/docs`) | Mintlify |

Decisión de Robin del 27 ago (D1): **el playground se construye en `apps/website`**, reutilizando design system, `generate-models.ts` y las páginas de modelos existentes.

Sobre el Hub, el TDD de Maanil (RFC, sin fechas, última edición 30 jul) propone **copiar** el frontend del Hub al monorepo (el Hub ya lee todo del Hub API en cloud.comfy.org, así que es una relocalización de frontend, no de datos). Dos opciones: **Opción 1 (recomendada, no vinculante)** = fusionar las rutas `/workflows/*` dentro de `apps/website` (una sola app, un solo deploy; exige pasar `apps/website` al adapter `@astrojs/vercel`); **Opción 2** = app aparte `apps/workflows-hub` con path-rewrite en el edge. URLs del Hub se preservan (301 de `/zh/*` a `/zh-CN/*`). El TDD no menciona Workshop, playground ni platform: hay que actualizarlo o escribir el nuevo TDD encima. Maanil lo decide; Robin: "para vibe-codear los diseños da igual dónde".

Implicación práctica: si va Opción 1, **modelos y workflows del Workshop viven juntos en `apps/website`** y las tabs son la misma app. Es lo más simple para la IA del PRD (`/workshop` con tabs).

Ramas upstream relevantes para mirar antes de empezar: `rh/hub`, `nav/models-v2-preview`, `uy/models-v2-collab`, `deepme987/cloud/comfyhub-to-workflows`, `feat/hub-template-api-migration`, `worktree-migrate-hub`.

### 7.2 Recomendación para mis cambios

1. **Prototipar en `apps/website` sobre mi fork** (`marianamartinho/comfy-home-v1`, fork de ComfyUI_frontend), en una rama `workshop`. El fork está en el commit del 24 ago; upstream ya va por 1.54.1 (1 sep) y tiene `models.astro`, `/p/supported-models`, `generated-models.json`. **Sincronizar con upstream main antes de tocar nada.**
2. Una sola rama compartida con eng cuando Maanil cierre el repo (Robin: "single branch there where we have all the designs").
3. Tratar el prototipo como desechable: lo que se lanza es la versión producción con el user journey completo.

### 7.3 Entorno local (hecho en esta sesión)

```bash
# Docs (este repo) — Mintlify
PLAYWRIGHT_SKIP_BROWSER_DOWNLOAD=1 npm i     # el postinstall de playwright falla sin esto
npm run dev                                    # http://localhost:3000

# Web comfy.org — fork de ComfyUI_frontend
git clone https://github.com/marianamartinho/comfy-home-v1
cd comfy-home-v1
git remote add upstream https://github.com/Comfy-Org/ComfyUI_frontend
git fetch upstream main && git merge upstream/main
pnpm install
pnpm --filter @comfyorg/website dev            # http://localhost:4321
```

Node: el fork pide 24 (`.nvmrc`); docs funciona con 22.

## 8. Preguntas para cerrar el 2 sep

1. **Rutas**: `/workshop/...` (PRD) o `/models` + `/workflows` (Detailed Reqs)? Plan de 301 del Hub actual.
2. **Nombre**: Workshop vs Hub. ¿Fecha límite para decidir?
3. **Sign in en comfy.org**: modal in-page (Detailed Reqs F4) o redirect a cloud/platform? Afecta a todo el header.
4. **Compra de créditos**: ¿top-up cross-origin desde comfy.org o redirect a platform con return URL?
5. **Deploy to Developer Platform**: ¿qué ve exactamente el usuario tras copiar (build + deployment)? ¿Qué pasa si no tiene acceso a platform (waitlist)? Wording final.
6. **Modelos sin metadata** (Router solo da id + billing): ¿quién escribe nombre, descripción, precio, thumbnail por modelo para el 11 sep? (`model-display-overrides.json`, Detailed Reqs F1/M1).
7. **Hero apps**: ¿cuáles 3? (face swap excluido por brand safety). ¿Cuántas apps migradas del Hub en GA?
8. **Presets**: formato JSON y dónde se alojan.
9. **Vídeo**: runs síncronos de hasta 10 min sin cola — ¿UX de espera aceptable para V1?
10. **Homepage**: ¿qué módulos se quitan para hacer sitio a Workshop? (el website team ya está reordenando: dev platform arriba, luego MiniMax, luego models).
11. **Workspace switcher** en el header desde V1, ¿sí? (Detailed Reqs D5: sí).

## 9. Docs (este repo) — tareas V1 (workstream 8)

Del *Post-Launch Checklist* del PRD: "Update docs with Workshop URLs and model playground guide".

- [ ] Página nueva **Comfy Workshop / Model Playground** (qué es, browse, run, créditos, API tab) — propuesta: `get_started/workshop.mdx` o dentro de `development/cloud/`.
- [ ] Enlazar Workshop desde `development/cloud/overview.mdx`, `development/api-development/getting-an-api-key.mdx` y `index.mdx`.
- [ ] Guía "Deploy an app from Workshop to Developer Platform" cuando exista el endpoint de copia.
- [ ] Añadir rutas a `docs.json` + redirects si se renombra algo.
- [ ] Traducciones con `npm run translate` (zh/ja/ko) tras cerrar el inglés.
- [ ] `llms.txt` / SEO metadata para las páginas nuevas.

## 10. Riesgos que veo

- **Fechas**: 4–11 sep para modelos depende de cloud backend (proxy F5, CORS en `/api/auth/token`, `/api/billing/balance`) y de metadata de modelos que Router aún no da. Sin eso solo hay browse.
- **Capacidad de Router** para tráfico consumer a ráfagas (riesgo *High* en el PRD).
- **Auth cross-domain**: primer sign in en comfy.org; popup bloqueado en Safari/iOS; flag `unified_cloud_auth` a medio migrar.
- **Endpoint "copy deployment"** no existe aún; su contrato define el flujo B.
- **Apps bespoke** = trabajo de diseño por app; limitar a 3 hero + plantilla genérica para el resto.
- **Dos documentos técnicos** (Detailed Reqs de Robin y TDD de Maanil) — que el TDD referencie el primero para no duplicar contratos.
- **Coherencia PRD**: las partes desactualizadas (free runs, clone, save to cloud) confundirán a quien entre nuevo.
