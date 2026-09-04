# Borrador del sitio de docs (Mintlify) — INTERNO

Staging PRIVADO del repo público `darkfunnels-ai/optimind-docs` → `docs.darkfunnels.ai`
(plan §12: ES-first, espejo EN de quickstarts + seguridad en fase D/E). Nada de
esta carpeta se publica directo: la mudanza pasa por el mismo filtro anti-fugas
del repo de skills (checklist en `docs/skills-draft/README.md`).

## Qué hay

- `docs.json` — config Mintlify (nav, branding mínimo).
- `index.mdx` + `quickstarts/{claude-ai,claude-code,codex,chatgpt}.mdx` + `seguridad.mdx`.
- `en/` — espejo EN de index, quickstarts y seguridad (lo que leen los
  revisores de los directorios). El sitio es bilingüe vía
  `navigation.languages` con `es` de default; la referencia de tools NO se
  espeja: se genera desde las descripciones reales, que son las que el conector
  le habla al modelo, y traducirlas a mano sería drift garantizado.
- `referencia/tools.mdx` — **GENERADA** por `scripts/gen-tools-reference.mjs`
  desde el registry real del mcp-server (cero drift; `--check` para CI).
  No editar a mano.

## Antes de publicar: `mint validate`, no `broken-links`

```bash
~/.npm/_npx/45ad5ad5343d10be/node_modules/.bin/mint validate
```

`mint broken-links` solo mira los enlaces; `mint validate` compila el sitio en
modo estricto y falla también con warnings. Es la red que hace falta, porque el
push al repo público redespliega solo y un `docs.json` malformado tumbaría el
sitio entero sin avisar.

## Estado: PUBLICADO (26-ago-2026)

`https://docs.darkfunnels.ai` sirve 200 con nuestro contenido; el repo público
`darkfunnels-ai/optimind-docs` (rama `main`, `docs.json` en la raíz) está
conectado a Mintlify y cada push redespliega.

Lo que queda, ya solo contenido:

1. Guías largas (`guias/{manual,catalogo,libreria,conversaciones,agencias}`)
   y espejo EN — fase D del plan.
2. `llms.txt` lo autogenera Mintlify (cortesía, no canal) — ya responde 200.

El `search_docs` del puente sirve la doc embebida del server; los deep-links de
las tools apuntan a este sitio.

## DNS de `docs.darkfunnels.ai` (Mintlify) — LOS 3 CREADOS 26-ago-2026

Zona `darkfunnels.ai` en Cloudflare. El orden importó: primero los dos TXT y,
solo cuando Mintlify los validó (✓ verde en Domain setup), el CNAME.

| Tipo | Nombre | Valor | Proxy | Estado |
|---|---|---|---|---|
| TXT | `_acme-challenge.docs` | `wenFO1PLfOWESvVVZuaepCmrklsOyBFkLv-g45pvDnw` | — | ✅ validado |
| TXT | `_cf-custom-hostname.docs` | `dbc9f162-ee8a-4ff1-a9f2-2f0cc6368e80` | — | ✅ validado |
| CNAME | `docs` | `cname.mintlify.builders` | **DNS only** (nube gris) | ✅ dominio «Connected» |

El `_cf-custom-hostname` indica que Mintlify sirve por Cloudflare for SaaS: el
CNAME va SIN proxy nuestro para que ellos emitan el certificado.

## Trampa que costó la tarde: instalar la GitHub App DESDE GitHub no conecta nada

La propia página de la app lo dice en un aviso: *«Do not install the Mintlify
GitHub App through GitHub — you must do it through the dashboard in order for
the connection point from your Mintlify account to your GitHub account to be
made»*. Instalada a mano desde GitHub, la app queda viva y con acceso al repo,
pero el desplegable «Select organization» del panel sale **vacío** («No options
available») — sin ningún error que lo explique.

Faltaba además un paso propio, en **Workspace → My profile → Integrations →
GitHub authorization** (estaba en `Inactive`): es una autorización de usuario
distinta de la instalación de la app, y el asistente de Git settings no la pide
ni la menciona. Con esa autorización activa y la app reinstalada desde el panel,
la organización aparece al instante.
