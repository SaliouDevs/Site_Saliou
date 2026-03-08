# Site_Saliou
Pourquoi sur téléphone “c’est petit” et ça scroll bizarre ?

Le meilleur fix (sans impacter le PC)

Tu ajoutes juste un bloc CSS mobile à la fin de ton <style> (et rien d’autre).
Ça règle le téléphone sans toucher au PC.

Copie-colle exactement ceci tout en bas de ton CSS :

@media (max-width: 768px) {

  /* stop le scroll global du navigateur */
  html, body {
    height: 100%;
    overflow: hidden;
  }

  /* espace élève plein écran */
  .espace-eleve.active {
    height: 100vh;
    overflow: hidden;
    padding-bottom: 0;
  }

  /* contenu scrollable (accueil/profil/...) */
  .espace-eleve .container {
    height: calc(100vh - 70px - 80px); /* header + bottom nav */
    overflow-y: auto;
    -webkit-overflow-scrolling: touch;
    padding: 12px;
  }

  /* sections iframe : la card prend tout l'espace utile */
  #panneaux .card,
  #test .card,
  #videos .card,
  #examen-pl .card,
  #examen-pld .card {
    height: calc(100vh - 70px - 80px - 60px); /* header + nav + bouton retour */
    padding: 0;
  }

  /* iframe pleine taille dans la card */
  .external-page {
    height: 100%;
    width: 100%;
    border: 0;
  }

  /* UI plus lisible sur mobile */
  .nav-grid { grid-template-columns: 1fr 1fr; gap: 12px; }
  .nav-card { padding: 16px 12px; border-radius: 18px; }
  .nav-icon { width: 52px; height: 52px; font-size: 1.2rem; }
  .welcome-card { padding: 18px; }
  .bottom-nav { padding: 10px 8px; }
}
Résultat

PC : inchangé

Mobile : app “fixe” (plus de scroll global)

Quand tu ouvres panneaux/test, l’iframe remplit l’écran (plus “petit”)

IMPORTANT (sinon ça reste petit dans l’iframe)

Dans tes pages internes Panneaux.html, Test.html, Poids_Leger.html, Poids_Lourd.html, ajoute au moins :

html, body { margin: 0; padding: 0; }
img { max-width: 100%; height: auto; }
Donc pour répondre à ta question

✅ Oui, ton code est OK pour PC.
⚠️ Pour mobile, là ce n’est pas encore conforme tant que tu n’as pas mis le bloc @media ci-dessus + le petit CSS dans les pages internes.
Parfait — du coup on doit gérer le bug classique de 100vh sur iPhone Safari/Chrome (la barre d’adresse change la hauteur). Pour que ça soit nickel sur Android + iPhone (Chrome + Safari), on fait un “vrai viewport” avec 100dvh (ou fallback JS).

✅ 1) Ajoute ce CSS à la fin (mobile only, safe PC)
@media (max-width: 768px) {

  /* Viewport fiable sur iPhone/Android */
  :root { --app-vh: 100vh; }
  @supports (height: 100dvh) {
    :root { --app-vh: 100dvh; }
  }

  html, body {
    height: var(--app-vh);
    overflow: hidden;
  }

  .espace-eleve.active {
    height: var(--app-vh);
    overflow: hidden;
    padding-bottom: 0;
  }

  /* header et bottom nav restent fixes */
  .header { position: sticky; top: 0; z-index: 1000; }
  .bottom-nav { position: fixed; left: 0; right: 0; bottom: 0; z-index: 1000; }

  /* zone scrollable */
  .espace-eleve .container {
    height: calc(var(--app-vh) - 70px - 80px);
    overflow-y: auto;
    -webkit-overflow-scrolling: touch;
    padding: 12px;
  }

  /* sections iframe: carte plein écran utile */
  #panneaux .card,
  #test .card,
  #videos .card,
  #examen-pl .card,
  #examen-pld .card {
    height: calc(var(--app-vh) - 70px - 80px - 60px);
    padding: 0;
  }

  .external-page {
    width: 100%;
    height: 100%;
    border: 0;
    display: block;
  }

  /* UI mobile */
  .nav-grid { grid-template-columns: 1fr 1fr; gap: 12px; }
  .nav-card { padding: 16px 12px; border-radius: 18px; }
  .nav-icon { width: 52px; height: 52px; font-size: 1.2rem; }
  .welcome-card { padding: 18px; }
  .bottom-nav { padding: 10px 8px; }
}

✅ Ça règle déjà 90% des soucis sur iPhone Safari/Chrome.

✅ 2) (Option “béton”) Ajoute ce mini JS pour iPhone anciens

Juste avant </script> (ou au début du script), ajoute :

function setAppHeight() {
  document.documentElement.style.setProperty('--app-vh', window.innerHeight + 'px');
}
window.addEventListener('resize', setAppHeight);
window.addEventListener('orientationchange', setAppHeight);
setAppHeight();

➡️ Comme ça, même si 100dvh n’est pas géré, ça reste parfait.

✅ 3) Dans chaque page interne (Panneaux.html, Test.html, etc.)

Ajoute dans leur CSS :

html, body { margin:0; padding:0; }
img { max-width:100%; height:auto; }
Résultat final attendu

Android Chrome : plein écran, pas de double scroll ✅

iPhone Safari/Chrome : plein écran stable même quand la barre d’adresse bouge ✅

PC : inchangé ✅