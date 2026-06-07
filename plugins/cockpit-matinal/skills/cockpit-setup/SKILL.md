---
name: cockpit-setup
description: Installe le « Cockpit Matinal » pour l'utilisateur courant — crée le Live Artifact de briefing Microsoft 365 (mails non traités, tâches de réunion, agenda) ET la routine matinale qui le rafraîchit, le tout scopé sur le compte M365 de la personne qui exécute le skill. Se déclenche sur : « installe le cockpit », « setup cockpit matinal », « configure mon briefing M365 », « cockpit matinal ».
---

# Setup du Cockpit Matinal (par utilisateur)

Ton rôle : installer le Cockpit Matinal pour **la personne qui exécute ce skill**, en utilisant
**SON propre connecteur Microsoft 365**. Règle absolue : ne jamais coder en dur une adresse mail —
tout est scopé sur la boîte M365 connectée de l'utilisateur courant (détecte-la dynamiquement).

Les fichiers de référence sont dans le dossier de ce skill :
- `${CLAUDE_PLUGIN_ROOT}/skills/cockpit-setup/cockpit-matinal.html` — le template du Live Artifact (DATA vide)
- `${CLAUDE_PLUGIN_ROOT}/skills/cockpit-setup/generation-data-cockpit.md` — la logique de génération du DATA

## Étape 0 — Pré-requis : connecteur Microsoft 365
Vérifie que le connecteur Microsoft 365 est disponible pour l'utilisateur. S'il ne l'est pas,
arrête-toi et demande-lui de le connecter sur **https://claude.ai/customize/connectors**
(Microsoft 365), puis de relancer `/cockpit-matinal:cockpit-setup`. Ne continue pas sans connecteur.

## Étape 1 — Créer le Live Artifact
Lis `cockpit-matinal.html` (dans ce dossier de skill) et crée un **Live Artifact** nommé
exactement **« Cockpit Matinal »** avec ce contenu HTML tel quel. Le bloc `DATA` est volontairement
vide (état « en attente de la première synchro ») — c'est normal.

## Étape 2 — Première synchro (remplir le DATA)
Lis `generation-data-cockpit.md` et **exécute sa logique sur la boîte M365 de l'utilisateur** :
- mails NON LUS de l'Inbox sur ~30 jours (= non traités),
- « Récap réunion » des ~30 derniers jours où l'utilisateur est participant → tâches qui lui reviennent,
- agenda de la semaine,
- TODO Teams des 7 derniers jours.
Produis le bloc `DATA` JSON conforme au schéma, puis **remplace le contenu entre `/*DATA_START*/`
et `/*DATA_END*/`** dans le Live Artifact « Cockpit Matinal ». L'utilisateur voit immédiatement son
premier briefing.

## Étape 3 — Créer la routine matinale
Crée une **routine planifiée** (tâche planifiée / scheduled task) nommée **« cockpit-matinal »** :
- **Cadence** : chaque jour ouvré à **07:30** heure locale de l'utilisateur. Propose-lui d'ajuster
  l'heure/les jours avant de valider.
- **Prompt de la routine** (texte exact à enregistrer) :
  > « Régénère le DATA du Cockpit Matinal : exécute la logique de `generation-data-cockpit.md` sur
  > MA boîte Microsoft 365 connectée (mails non lus 30 j, Récap réunion 30 j, agenda semaine,
  > Teams 7 j), produis le bloc DATA JSON, puis mets à jour le Live Artifact "Cockpit Matinal" en
  > remplaçant tout ce qui se trouve entre /*DATA_START*/ et /*DATA_END*/. Ne change jamais le
  > format des marqueurs. »
- **Connecteur** : Microsoft 365 (celui de l'utilisateur courant, inclus automatiquement).
Utilise le mécanisme de création de routine disponible (skill/outil `schedule` /
`create_scheduled_task`). Si la création automatique n'est pas possible dans l'environnement,
donne à l'utilisateur la commande exacte à lancer (`/schedule …`) avec ce prompt.

## Étape 4 — Confirmer
Récapitule à l'utilisateur :
- ✅ Live Artifact « Cockpit Matinal » créé,
- ✅ première synchro faite (rappelle les chiffres clés : nb de relances, tâches CR, RDV),
- ✅ routine active (jour + heure),
- ℹ️ comment forcer un refresh : bouton « 🔄 Rafraîchir » du cockpit, ou relancer la routine.

## Règles
- **Ne jamais** modifier le format des marqueurs `/*DATA_START*/` `/*DATA_END*/`.
- **Toujours** scoper sur la boîte M365 connectée de l'utilisateur — jamais une adresse en dur.
- Si une source (Teams, agenda) est indisponible (licence/permission), marque `"available": false`
  avec une note explicative, sans bloquer le reste.
- JSON strictement valide (guillemets droits, pas de virgule finale).
