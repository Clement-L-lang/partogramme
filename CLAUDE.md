# Projet partogramme

Application de suivi du travail en salle de naissance (TP de culture numérique, données fictives).
Site statique : `index.html` + `style.css` + plusieurs fichiers JS chargés par `index.html`.
Le rendu attendu est la maquette `maquette.png`.

## Base de données (Supabase)

- Projet Supabase : `bobfwelfuotnknbnumbv` (connecté au MCP `supabase`).
- L'URL et la clé publishable sont dans `config.js` (`window.SUPABASE_URL` / `window.SUPABASE_KEY`),
  lu par `app.js`. Ce fichier a été écrit par `Connecter-Supabase.bat` : ne jamais y mettre la clé secrète.
- Tables (toutes en minuscules — les anciennes `Praticien` et `Orientation` avec majuscule ont été
  renommées le 01/10/2026) : `patiente`, `grossesse`, `examen`, `enfant`, `praticien`, `orientation`.
- Types énumérés Postgres : `groupe_sanguin` (A+ … AB-, inconnu), `specialites` (SF, Anest,
  Obstetricien, Pediatre), `sexe` (garcon, fille).
- Colonnes de liens : `grossesse` référence `patiente` et 4 praticiens (`id_sage_femme`,
  `id_obstetricien`, `id_anesthesite` — sans second « t » —, `id_pediatre`) ; `examen` référence
  `grossesse` et `orientation` ; `enfant` référence `grossesse`.
- RLS activée partout sauf sur `enfant` (désactivée — alerte de sécurité signalée à
  l'utilisateur le 01/10/2026, décision en attente : l'activer exigera d'ajouter des règles
  d'accès en même temps, sinon le site ne pourra plus lire les enfants).

## Mise en ligne

- Dépôt GitHub public : https://github.com/Clement-L-lang/partogramme (compte `Clement-L-lang`).
- Publier = `git add` + `git commit` + `git push` : le workflow `.github/workflows/deploy.yml`
  met le site en ligne sur https://sps-g20-parto.professeurpetitchat.com/ (premier déploiement
  réussi le 01/10/2026, vérifié dans le navigateur).
- `gh` se connecte au compte via `gh auth login` (méthode code d'appareil, jeton stocké dans le
  trousseau). Attention sur ce PC : passer le jeton par le pipe PowerShell (`Get-Content | gh auth
  login --with-token`) échoue avec « Bad credentials » ; utiliser `cmd /c "gh auth login -h
  github.com --with-token < %TEMP%\jeton.txt"`.

## Lancer le site en local

- Python n'est PAS installé sur ce PC (`python -m http.server` échoue), et `npx` est bloqué
  par la stratégie d'exécution PowerShell (script `npx.ps1` non signé). Node fonctionne (`node`).
- Donc : `node <chemin d'un petit serveur statique> partogramme 8000` puis ouvrir
  http://localhost:8000/ dans le navigateur (MCP Playwright).

## Points de fonctionnement

- 5 onglets pilotés par `data-page` dans `index.html` : suivi (`app.js` + `examens.js`),
  patientes (`app.js`), grossesses (`grossesses.js`), enfants (`enfants.js`), praticiens (`praticiens.js`).
- « Exporter en Excel » (bouton d'en-tête) : `export.js`, désactivé tant qu'aucune grossesse
  n'est choisie dans le suivi.
- Les messages d'état s'affichent via `afficherMessage(element, texte, "erreur"|"ok")`.
- Affichage des dates : toujours jour/mois/année (jj/mm/aaaa), jamais en format ISO ou américain.
