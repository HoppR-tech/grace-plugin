# Grace Lite : plugin pour Codex, Claude Code et GitHub Copilot CLI

Cette branche `grace-lite` distribue la variante du plugin Grace acceptée par les annuaires publics. Le catalogue s’appelle `grace-lite`, le plugin `hoppr-grace`. Il connecte votre outil à Grace avec votre compte (OAuth) et fournit un skill `grace` qui fait reconnaître le projet à partir du dépôt Git dans lequel vous travaillez.

Cette variante ne contient **ni hook ni exécutable** : seulement les manifestes, la connexion au serveur MCP distant de Grace, le skill et une icône. C’est l’agent qui lit le dépôt avec des commandes Git en lecture seule, en retirant tout identifiant de l’adresse du dépôt avant de la lire. Le plugin natif, sur la branche `main`, observe le dépôt lui-même avec un hook : il est plus fiable, mais n’est pas accepté par les annuaires publics.

Git et un client compatible sont nécessaires. Copilot dans VS Code et OMP ne sont pas pris en charge.

## Avant l’installation

- Disposer d’un compte Grace membre d’une organisation approuvée. Le dépôt est public, l’accès à Grace ne l’est pas.
- N’installez pas à la fois `hoppr-grace@grace-native` et `hoppr-grace@grace-lite` dans le même client : choisissez une variante.
- Le plugin fournit lui-même les skills Grace (`grace-review`, `grace-discover-codebase`, `grace-effort`). Si un projet contient encore les dossiers `grace-review`, `grace-discover-codebase` et `grace-effort` installés à la main pour ce client (`.claude/skills/`, `.agents/skills/` ou `.github/skills/`), supprimez-les pour éviter des doublons qui ne seraient plus mis à jour, sauf si un autre outil non couvert par le plugin (Cursor, Copilot dans VS Code…) les utilise dans le même dépôt. Gardez les profils d’agents `grace-review-*` : ce sont eux qui limitent les relecteurs à la lecture.
- Si Grace est déjà configuré à la main, sauvegardez vos réglages puis désactivez seulement cette ancienne entrée Grace avant d’activer le plugin.

## Installer

Choisissez **un seul** bloc, celui de votre client.

### Codex

```sh
codex plugin marketplace add HoppR-tech/grace-plugin --ref grace-lite
codex plugin add hoppr-grace@grace-lite
```

### Claude Code

```sh
claude plugin marketplace add "HoppR-tech/grace-plugin#grace-lite"
claude plugin install hoppr-grace@grace-lite --scope user
```

### Copilot CLI

```sh
copilot plugin marketplace add "HoppR-tech/grace-plugin#grace-lite"
copilot plugin install hoppr-grace@grace-lite
```

Fermez puis relancez le client et effectuez la connexion OAuth Grace qu’il propose ; ne copiez aucun token dans un fichier du plugin.

Dans un dépôt autorisé, demandez ensuite à l’agent de vérifier le contexte Grace avant de modifier du code. Il doit identifier le bon projet ou demander un choix explicite. Un projet incorrect, une absence de contexte ou un refus ne doit pas être contourné en inventant un identifiant de projet.

## Mettre à jour

```sh
codex plugin marketplace upgrade grace-lite && codex plugin add hoppr-grace@grace-lite
claude plugin marketplace update grace-lite && claude plugin update hoppr-grace@grace-lite --scope user
copilot plugin marketplace update grace-lite && copilot plugin update hoppr-grace@grace-lite
```

Une installation qui suit `grace-lite` reçoit la nouvelle préversion. Un tag `lite-v…` épinglé reste volontairement figé ; les tags existants ne sont jamais déplacés.

## Intégrité et traçabilité

Chaque version livre `SHA256SUMS`, `provenance.json` et `sbom.cdx.json`. La provenance identifie le commit source et l’exécution GitHub Actions. C’est une observation de build, **pas une attestation cryptographique**. Dans un clone de la référence annoncée :

```sh
shasum -a 256 -c SHA256SUMS
```

Tout échec interdit d’utiliser ce paquet.

## Désinstaller

```sh
codex plugin remove hoppr-grace@grace-lite
claude plugin uninstall hoppr-grace@grace-lite --scope user
copilot plugin uninstall hoppr-grace@grace-lite
```

Une désinstallation ne révoque pas votre connexion OAuth : déconnectez-vous séparément si nécessaire.

## Signaler un blocage

Écrivez à contact-grace@hoppr.tech avec le client et sa version, la version du plugin, l’étape et le message d’erreur. Excluez tokens, cookies, clés privées et contenu privé des projets.

## Licence

Le plugin est un logiciel propriétaire de HoppR : voir [LICENSE.md](LICENSE.md). Cette variante ne contient aucun composant tiers.

Ce dépôt reçoit uniquement des paquets fabriqués par l’intégration continue de HoppR.
