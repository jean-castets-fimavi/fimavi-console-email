# Plugin · cockpit-matinal

Skill `cockpit-setup` : installe le Cockpit Matinal pour l'utilisateur courant (Live Artifact +
routine de rafraîchissement), scopé sur son propre Microsoft 365.

## Usage
```
/plugin marketplace add appsoluteown/fimavi-cowork-plugin
/plugin install cockpit-matinal
/cockpit-matinal:cockpit-setup
```

Pré-requis : connecteur Microsoft 365 connecté (https://claude.ai/customize/connectors).

Le skill : (1) crée le Live Artifact depuis `cockpit-matinal.html`, (2) lance une 1ère synchro M365
via `generation-data-cockpit.md`, (3) crée la routine matinale « cockpit-matinal » (07:30, jours
ouvrés par défaut). Détails dans `skills/cockpit-setup/SKILL.md`.
