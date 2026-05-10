# werock-marketing — `werockapp.com` mono-domain mini-site

Mini-site Jekyll del **dominio raíz `werockapp.com`** servido vía
**GitHub Pages**. Ataca el item M0+1 #1 del
[`PLAY_STORE_M0_RELEASE_PLAN.md`](../werock/docs/operations/PLAY_STORE_M0_RELEASE_PLAN.md):

1. Stripe Connect Express redirige a `/__stripe_return` y `/__stripe_refresh` —
   sin dominio root vivo, el rider ve `DNS_PROBE_FINISHED_NXDOMAIN`.
2. Play Console "Sitio web" de la ficha — `https://werockapp.com` queda
   vacío hasta que esté hosteado (riesgo rejection "Broken Functionality").
3. Branding general — cualquier rider que teclee `werockapp.com` cae en blanco.

> **Decisión director (10 may 2026 ~00:08 UTC+2):** mono-dominio con
> **paths reservados** para cuando llegue el contenido de M1.
> `/legal` enlaza al subdominio `legal.werockapp.com` ya vivo (HSP-7
> cerrado el 6 may, no se rompen referencias en la app).

## Estructura

```
werock-marketing/
├── _config.yml          Jekyll config (theme cayman, lang es)
├── CNAME                werockapp.com (root domain)
├── index.md             Hero landing
├── __stripe_return.md   Stripe Connect onboarding success
├── __stripe_refresh.md  Stripe Connect onboarding refresh (sesión expirada)
├── pro/
│   └── index.md         Placeholder Pro Basic — "llega en M1"
├── legal/
│   └── index.md         Hub que enlaza a legal.werockapp.com
├── assets/css/style.scss   Override del tema Cayman con branding
│                            verde-lima de la app (Light Green A400)
├── .gitignore
└── README.md            (este archivo)
```

## Caminos de despliegue

Hay **3 opciones**. La más limpia y la recomendada es la (B) por
mantener un repo dedicado fácil de auditar y de transferir
ownership cuando entre un equipo de marketing en M1+.

### (A) Repo independiente `werock-marketing` (recomendado, ~10 min)

```bash
# Desde la raíz del workspace, con `gh` autenticado:
cd werock-marketing
gh repo create werock-marketing --public \
  --description "WeRock — landing y páginas Stripe Connect" \
  --source=. --push
```

Después en `https://github.com/<owner>/werock-marketing/settings/pages`:

1. **Build and deployment** → Source = `Deploy from a branch`
2. **Branch** = `main`, folder = `/` (root)
3. **Custom domain** = `werockapp.com`
4. **Enforce HTTPS** = ON (espera 5-10 min al cert ACME)

### (B) Subtree push desde el monorepo

Mantener el código aquí y empujar la subcarpeta a un repo dedicado:

```bash
# Una vez creado werock-marketing en GitHub vacío:
git subtree push --prefix=werock-marketing \
  git@github.com:<owner>/werock-marketing.git main
```

Coste cada cambio: un comando extra. Ventaja: histórico unificado en
este repo, fácil de revisar PRs.

### (C) GitHub Actions desde `werock` repo (más complejo)

Workflow que hace `jekyll build` desde `werock-marketing/` y publica
en una orphan branch `gh-pages` del repo `werock` con custom domain
`werockapp.com`. Requiere config separada y rompe si en M1 alguien
quiere editar el sitio sin acceso al repo de la app. **No recomendado.**

## DNS Cloudflare (~5 min)

Una vez GitHub Pages activo y verificado:

| Tipo | Nombre | Valor | Proxy | TTL |
| --- | --- | --- | --- | --- |
| A | `@` | `185.199.108.153` | DNS-only | Auto |
| A | `@` | `185.199.109.153` | DNS-only | Auto |
| A | `@` | `185.199.110.153` | DNS-only | Auto |
| A | `@` | `185.199.111.153` | DNS-only | Auto |
| AAAA | `@` | `2606:50c0:8000::153` | DNS-only | Auto |
| AAAA | `@` | `2606:50c0:8001::153` | DNS-only | Auto |
| AAAA | `@` | `2606:50c0:8002::153` | DNS-only | Auto |
| AAAA | `@` | `2606:50c0:8003::153` | DNS-only | Auto |

> ⚠️ **Proxy = DNS-only** (cloud gris, no naranja). El proxy de
> Cloudflare interfiere con la verificación de dominio de GitHub
> Pages y con la emisión del certificado ACME. Si más adelante quieres
> CDN/WAF, GitHub Pages ya sirve detrás de Fastly y CDN77, suficiente
> para una landing con 4 páginas.

`legal.werockapp.com` (subdominio existente) **no se toca**: sigue
apuntando al CNAME del repo `werock-legal` y a sus 4 páginas markdown.
El nuevo `werockapp.com/legal/` actúa como hub que enlaza ahí.

## Smoke tests post-deploy

```bash
# 1. DNS resuelve
dig werockapp.com +short
# Debe devolver al menos una de 185.199.108-111.153

# 2. HTTPS sirve la landing
curl -sI https://werockapp.com | head -n 5
# HTTP/2 200 + content-type text/html

# 3. Stripe paths funcionan
curl -sI https://werockapp.com/__stripe_return | head -n 1
curl -sI https://werockapp.com/__stripe_refresh | head -n 1
# Ambos HTTP/2 200

# 4. Hub legal funciona
curl -sI https://werockapp.com/legal/ | head -n 1
# HTTP/2 200, links abren legal.werockapp.com/*
```

## Tareas Play Console post-deploy

1. **Rellenar campo "Sitio web" en la ficha**: Play Console → Aumentar
   usuarios → Configuración de la tienda → Datos de contacto →
   Editar → Sitio web = `https://werockapp.com`. (Decisión director
   D5 ship-day 8 may 2026 11:11 UTC+2: campo dejó vacío en M0 para
   evitar rejection "Broken Functionality"; ya no aplica.)

2. **Verificar dominio en Search Console** (opcional pero recomendado):
   `https://search.google.com/search-console` → Añadir propiedad →
   Dominio → `werockapp.com` → DNS TXT record → copiar a Cloudflare.
   Mejora ASO Play Console y deja la puerta abierta a indexación
   organica.

## Versionado del sitio

Cualquier cambio en copy o estructura:

1. Commit con mensaje descriptivo (`docs(web): añadir sección X`).
2. GitHub Pages re-builds automáticamente al hacer push a `main`.
3. Tarda 1-3 min en propagarse (refresh hard del browser para ver cambios).

## TODOs M1+

- [ ] Cambiar el botón "Próximamente en Google Play" a un Play Store
      badge oficial cuando AAB v11 sea aprobado y la ficha pública
      esté visible en `https://play.google.com/store/apps/details?id=com.werock.app`.
- [ ] Considerar `/blog` o `/news` cuando arranque marketing
      orgánico (M1+).
- [ ] Migrar contenido de `legal.werockapp.com/*` a `/legal/*`
      (todo en mono-domain) si el dueño de la app lo prefiere — por
      ahora no es prioritario, los strings de la app apuntan al
      subdominio y mantenerlos vivos no cuesta nada.
- [ ] Internacionalización (`/en`, `/fr`...) cuando se active M2.

## Contacto

- General: [`support@werockapp.com`](mailto:support@werockapp.com)
- Privacidad: [`privacy@werockapp.com`](mailto:privacy@werockapp.com)
- Bugs sitio web: [`bugs@werockapp.com`](mailto:bugs@werockapp.com)
