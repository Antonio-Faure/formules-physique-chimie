# Formules PC

Explorateur de formules de physique-chimie pour le BAC.

Une page HTML unique qui charge les formules depuis un fichier CSV et les affiche avec rendu LaTeX (KaTeX).

## Fonctionnalités

- Recherche en temps réel
- Filtrage par chapitre
- Favoris (persistés dans localStorage)
- Mode révision (cache les détails, clique pour révéler)
- Filtre BAC — n'affiche que les formules du formulaire officiel
- Thème clair / sombre
- Badge doré sur les formules présentes dans le formulaire officiel BAC
- Chargement CSV automatique + fallback manuel

## Utilisation

Ouvre `index.html` dans un navigateur (pas de serveur nécessaire).

Les formules sont chargées depuis `formules.csv` au lancement. Si le fichier n'est pas trouvé, un bouton permet de le charger manuellement.

## Structure

- `index.html` — application complète (HTML + CSS + JS)
- `formules.csv` — base de données des formules
- `PATTERNS.md` — patterns de code réutilisables

## Stack

- Vanilla JS
- KaTeX (rendu LaTeX)
- Google Fonts (Crimson Pro + DM Sans)
- Pas de framework, pas de build
