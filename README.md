# Site_Saliou
Objectif : rendre le site mobile (Android/iPhone, Chrome/Safari) “app-like” (header + bottom-nav fixes) sans casser la version PC.

Bug actuel : sur mobile, la page inscription/connexion ne peut plus défiler car `html, body { overflow:hidden; }` est appliqué globalement.

À faire dans index.html :
1) Repérer le bloc `@media (max-width: 768px)` (ou créer un bloc si absent)
2) Sur mobile, laisser le scroll NORMAL sur tout le site (inscription/connexion)
3) Activer le “mode app fixe” uniquement quand l’espace app est affiché (quand `#espaceEleveSection` a la classe `.active`)
4) Ne rien toucher au desktop : tout doit rester dans le media query.

PATCH CSS À APPLIQUER (remplacer la partie mobile qui bloque le scroll global) :

@media (max-width: 768px) {
  :root { --app-vh: 100vh; }
  @supports (height: 100dvh) {
    :root { --app-vh: 100dvh; }
  }

  /* Mobile: par défaut, on SCROLL (inscription/connexion OK) */
  html, body {
    min-height: var(--app-vh);
    overflow-y: auto;
    -webkit-overflow-scrolling: touch;
  }

  /* Mobile: mode "app" seulement quand l'espace app est visible */
  #espaceEleveSection.active {
    height: var(--app-vh);
    overflow: hidden;
    padding-bottom: 0;
  }

  /* Le contenu interne scroll, header + bottom-nav restent fixes */
  #espaceEleveSection.active .container {
    height: calc(var(--app-vh) - 70px - 80px);
    overflow-y: auto;
    -webkit-overflow-scrolling: touch;
    padding: 12px;
  }

  /* Sections iframe: la card prend la place utile */
  #panneaux .card, #test .card, #videos .card, #examen-pl .card, #examen-pld .card {
    height: calc(var(--app-vh) - 70px - 80px - 60px);
    padding: 0;
  }

  .external-page { width: 100%; height: 100%; border: 0; }

  /* UI mobile un peu plus lisible */
  .nav-grid { grid-template-columns: 1fr 1fr; gap: 12px; }
  .nav-card { padding: 16px 12px; border-radius: 18px; }
  .nav-icon { width: 52px; height: 52px; font-size: 1.2rem; }
  .welcome-card { padding: 18px; }
  .bottom-nav { padding: 10px 8px; }
}

À vérifier :
- Sur mobile : inscription/connexion scrollent normalement
- Dans l’espace app : header + bottom-nav restent fixes, seul le contenu scroll
- PC : aucune différence