---
title: Sesión expirada — WeRock
description: La sesión de configuración Stripe ha expirado. Reinicia el proceso desde la app WeRock.
permalink: /__stripe_refresh
sitemap: false
robots: noindex
---

<section class="status status-warning">
  <div class="status-icon">⏱️</div>
  <h1>Sesión expirada</h1>
  <p class="lead">
    El enlace de configuración de Stripe ha caducado por seguridad.
    No te preocupes: <strong>no se ha guardado ningún dato sensible</strong>.
    Vuelve a la app WeRock y reintenta el proceso.
  </p>

  <div class="cta-row">
    <a class="btn btn-primary" href="werock://stripe-refresh">Reintentar en WeRock</a>
  </div>

  <p class="small">
    Si el botón no abre la app, ábrela manualmente y entra de nuevo a
    la sección "Configurar pagos" desde tu perfil profesional.
  </p>

  <hr>

  <h2 class="small-heading">¿Por qué pasa esto?</h2>
  <p>
    Stripe Connect emite enlaces de configuración con duración limitada
    (24h aproximadamente) por motivos de seguridad. Si tardas en
    completar el proceso o cierras el navegador, el enlace caduca y
    hay que generar uno nuevo desde la app.
  </p>

  <p>
    Tu progreso anterior (datos bancarios, verificación de identidad)
    <strong>se conserva en Stripe</strong>. Al reintentar, reanudarás
    desde donde lo dejaste.
  </p>

  <p class="small">
    ¿Sigues con problemas? Escríbenos a
    <a href="mailto:support@werockapp.com">support@werockapp.com</a>
    y te ayudamos a desbloquear la cuenta.
  </p>

  <p class="small">
    <a href="/">← Volver a WeRock</a>
  </p>
</section>
