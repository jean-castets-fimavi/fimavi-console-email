# FIMAVI · Marketplace Cowork

Marketplace de plugins internes pour **Claude Cowork / Claude Code** du Groupe FIMAVI.

## Plugin disponible

### `cockpit-matinal`
Installe pour chaque collaborateur :
- un **Live Artifact « Cockpit Matinal »** — briefing quotidien de sa boîte Microsoft 365
  (mails non traités classés par priorité, tâches issues des CR de réunion, agenda de la semaine) ;
- une **routine matinale** qui le rafraîchit automatiquement.

Tout est **scopé sur le compte M365 de chaque utilisateur** — aucune donnée partagée entre comptes.

---

## Déploiement

### 1. Côté admin (une seule fois)
1. **Connecteur Microsoft 365 au niveau org** : donner le consentement administrateur à l'app Claude
   (Entra ID / Microsoft 365 admin center) pour que chaque collaborateur connecte son compte en 1 clic.
2. **Autoriser ce marketplace** dans les *managed settings* de l'org (console Team/Enterprise) si
   `strictKnownMarketplaces` est activé.

### 2. Côté chaque collaborateur (≈ 2 min, une fois)
1. Connecter **son** Microsoft 365 : https://claude.ai/customize/connectors → Microsoft 365.
2. Dans Cowork (ou Claude Code) :
   ```
   /plugin marketplace add jean-castets-fimavi/fimavi-console-email
   /plugin install cockpit-matinal
   /cockpit-matinal:cockpit-setup
   ```
3. Valider la création de la routine quand Claude la propose (heure par défaut : 07:30, jours ouvrés).

C'est tout : le cockpit se remplit immédiatement, puis se rafraîchit chaque matin.

---

## Mises à jour
Pousse une nouvelle version sur ce repo (incrémente `version` dans `plugin.json`) ; les
collaborateurs récupèrent la MAJ via `/plugin marketplace update fimavi-cowork`.

## ⚠️ Limites connues (à la date d'écriture)
- Les **Routines** sont en *research preview* → comportement susceptible d'évoluer.
- Le **connecteur M365** s'autorise **par utilisateur** (OAuth) — pas de provisioning silencieux en masse.
- Un **Live Artifact** n'est pas « partageable » d'un compte à l'autre nativement : c'est le skill
  qui le **recrée** dans chaque compte à partir du template `cockpit-matinal.html`.
- La création de routine passe par Claude (skill `schedule`) avec l'utilisateur présent — ce n'est
  pas une API 100 % silencieuse, mais ça tient dans le « coller / lancer une fois ».

## Structure
```
fimavi-cowork-plugin/                       # ce repo = le marketplace
├── .claude-plugin/marketplace.json         # liste des plugins de l'org
└── plugins/
    └── cockpit-matinal/
        ├── .claude-plugin/plugin.json
        └── skills/cockpit-setup/
            ├── SKILL.md                     # orchestre : artifact + 1ère synchro + routine
            ├── cockpit-matinal.html         # template du Live Artifact (DATA vide)
            └── generation-data-cockpit.md   # logique de génération du DATA (M365)
```
