# Grace — plugin natif pour Codex, Claude Code et GitHub Copilot CLI

Ce dépôt distribue le plugin Grace. Le catalogue s’appelle `grace-native`, le plugin `hoppr-grace`. Il connecte votre outil à Grace avec votre compte et reconnaît le projet à partir du dépôt Git dans lequel vous travaillez.

**Préversion pour macOS (Apple Silicon et Intel), Linux (x64 et arm64) et Windows (x64).** Chaque plugin contient un petit exécutable par système ; le bon est choisi automatiquement. Sur macOS Apple Silicon, une signature ad hoc contrôle l’intégrité de l’exécutable ; les autres ne sont pas signés. Aucun ne prouve l’identité de l’éditeur : il n’y a ni Developer ID, ni notarisation, ni Authenticode. Copilot dans VS Code et OMP ne sont pas pris en charge.

Les exécutables ne nécessitent ni Node ni Python. Git (Git for Windows sous Windows) et un client compatible restent nécessaires. Sous Windows, Claude Code lance le plugin avec Git Bash, fourni par Git for Windows ; s’il ne le trouve pas, indiquez son chemin dans `CLAUDE_CODE_GIT_BASH_PATH`. Ne désactivez pas les protections de votre système ou de votre client pour l’installer.

Le téléchargement contient les variantes des trois outils, soit environ 50 Mo une fois extrait.

## Avant l’installation

- Disposer d’un compte Grace membre d’une organisation approuvée. Le dépôt est public, l’accès à Grace ne l’est pas.
- Utiliser un système pris en charge et une version récente de votre client. Le parcours complet a été exécuté le 25 novembre 2026 sur macOS arm64 avec Codex 0.172.0, Claude Code 2.1.331 et Copilot CLI 1.0.83, contre l’environnement de préproduction. Les essais de l’exécutable actuel sont détaillés dans le guide Grace. Une autre version peut se comporter autrement.
- Le plugin remplace le script d’installation par projet. Il fournit lui-même les quatre skills Grace (`grace-review`, `grace-discover-codebase`, `grace-update`, `grace-effort`) avec le client de revue, le bloc d’instructions Grace, ajouté au contexte à chaque démarrage de session au lieu d’être écrit dans `CLAUDE.md`, `AGENTS.md` ou `.github/copilot-instructions.md`, et, pour Claude Code, les profils de relecteurs `grace-review-low`, `grace-review-medium` et `grace-review-high`.
- Si un projet contient encore ces skills (`.claude/skills/`, `.agents/skills/` ou `.github/skills/`), les profils `.claude/agents/grace-review-*` ou le bloc Grace entre `<!-- grace:begin -->` et `<!-- grace:end -->`, supprimez-les : sinon ces copies passent avant celles du plugin ou les doublent, et ne sont plus mises à jour. Gardez-les seulement si un autre outil non couvert par le plugin (Cursor, Copilot dans VS Code…) les utilise dans le même dépôt.
- Codex ne charge pas d’agents depuis un plugin : `grace-update` installe toujours les profils `.codex/agents/grace-review-*.toml` dans le projet au premier échange. Gardez-les s’ils sont déjà installés.
- Claude Code ignore `permissionMode` pour les agents d’un plugin. Le plugin garde donc ses relecteurs en lecture seule par son hook : il leur refuse toute écriture, tout outil MCP et toute commande hors d’une liste de lecture (`git diff`, `git show`, `cat`, `grep`…), sans redirection ni enchaînement.
- Si Grace est déjà configuré à la main, sauvegardez vos réglages puis désactivez seulement cette ancienne entrée Grace avant d’activer le plugin. Sinon, elle peut masquer le plugin ou l’emporter sur lui. Ne supprimez pas vos autres MCP, instructions ou identifiants.

Vérifiez l’accès Git, sans placer de token dans l’URL :

```sh
git ls-remote https://github.com/HoppR-tech/grace-plugin.git HEAD
```

Une empreinte et `HEAD` doivent apparaître, sans compte GitHub ni clé SSH. N’installez pas une version que les notes annoncent non utilisable.

## Installer

Choisissez **un seul** bloc, celui de votre client. Les commandes ajoutent le catalogue puis installent sa variante Grace.

### Codex

```sh
codex plugin marketplace add HoppR-tech/grace-plugin --ref main
codex plugin add hoppr-grace@grace-native
```

### Claude Code

```sh
claude plugin marketplace add HoppR-tech/grace-plugin
claude plugin install hoppr-grace@grace-native --scope user
```

### Copilot CLI

```sh
copilot plugin marketplace add HoppR-tech/grace-plugin
copilot plugin install hoppr-grace@grace-native
```

Fermez puis relancez le client. Inspectez les permissions et approuvez les hooks uniquement si leur contenu et leur provenance correspondent au plugin choisi. Une installation réussie ne signifie pas que les hooks sont approuvés ou exécutés.

Effectuez la connexion OAuth Grace proposée par le client ; ne copiez aucun token dans un fichier du plugin. Les connexions restent indépendantes entre clients. Si le client redemande une connexion après un redémarrage, reconnectez-vous depuis l’outil.

Dans un dépôt autorisé, demandez ensuite à l’agent de vérifier le contexte Grace avant de modifier du code. Il doit identifier le bon projet ou demander un choix explicite, puis accéder aux talents autorisés. Un projet incorrect, une absence de contexte ou un refus ne doit pas être contourné en inventant un identifiant de projet. Arrêtez l’essai et transmettez le message d’erreur sans credentials.

## Mettre à jour

Le plugin s’appelait `grace` avant d’être renommé `hoppr-grace`. Si `grace@grace-native` est encore installé, désinstallez-le (voir « Désinstaller » avec l’ancien nom), actualisez le catalogue puis installez `hoppr-grace@grace-native`.

Une nouvelle version publiée ne remplace pas immédiatement une session ouverte. Il faut actualiser le catalogue **puis** le plugin ; les commandes natives sont utilisées, sans updater Grace.

### Codex

```sh
codex plugin marketplace upgrade grace-native
codex plugin add hoppr-grace@grace-native
```

### Claude Code

```sh
claude plugin marketplace update grace-native
claude plugin update hoppr-grace@grace-native --scope user
```

### Copilot CLI

```sh
copilot plugin marketplace update grace-native
copilot plugin update hoppr-grace@grace-native
```

Redémarrez le client, vérifiez la version affichée et refaites le contrôle du projet. Si le contenu des hooks change, leur approbation doit être renouvelée. Codex le demande en session interactive ; en mode non interactif (`codex exec`), il ignore sans le signaler un hook non approuvé, et le plugin ne déclare alors plus le dépôt. Une erreur de mise à jour ne doit pas être assimilée au maintien garanti d’un plugin actif ; vérifiez l’inventaire du client avant de reprendre.

Une installation qui suit `main` peut recevoir la nouvelle préversion. **Un tag ou commit épinglé reste volontairement figé** : il faut changer explicitement cette référence pour avancer. Les tags existants ne sont jamais déplacés.

## Intégrité et traçabilité

Chaque version livre `SHA256SUMS`, `provenance.json` et `sbom.cdx.json`. La provenance identifie le commit source et, pour une fabrication CI, l’exécution GitHub Actions. Ce document est une observation de build, **pas une attestation cryptographique**. Les sommes SHA-256 ne prouvent pas à elles seules l’identité de l’éditeur.

Après avoir cloné la référence annoncée, exécutez dans ce clone :

```sh
shasum -a 256 -c SHA256SUMS
```

Tout échec interdit d’utiliser ce paquet. Ne régénérez pas les sommes pour le faire accepter.

## Désinstaller ou revenir en arrière

Quittez le client avant le retrait. Choisissez le bloc correspondant :

```sh
codex plugin remove hoppr-grace@grace-native
```

```sh
claude plugin uninstall hoppr-grace@grace-native --scope user
```

```sh
copilot plugin uninstall hoppr-grace@grace-native
```

Relancez le client et vérifiez que le plugin, ses hooks et son MCP ne sont plus actifs. Une désinstallation ne constitue pas une preuve de révocation OAuth : utilisez séparément les commandes de déconnexion natives si nécessaire.

Le client peut conserver le catalogue ou d’anciennes versions sur le disque. Retirez le catalogue avec `marketplace remove` si vous n’en avez plus besoin.

Pour revenir à une version antérieure, utilisez uniquement un tag dont les notes confirment la compatibilité avec le service courant. Désinstallez le plugin, retirez le catalogue, rajoutez-le épinglé au tag (`TAG` ci-dessous), puis réinstallez :

```sh
codex plugin marketplace remove grace-native
codex plugin marketplace add HoppR-tech/grace-plugin --ref TAG
codex plugin add hoppr-grace@grace-native
```

```sh
claude plugin marketplace remove grace-native
claude plugin marketplace add "HoppR-tech/grace-plugin#TAG"
claude plugin install hoppr-grace@grace-native --scope user
```

```sh
copilot plugin marketplace remove grace-native
copilot plugin marketplace add "HoppR-tech/grace-plugin#TAG"
copilot plugin install hoppr-grace@grace-native
```

Ne changez ni les fichiers générés ni le tag publié pour fabriquer un retour arrière.

## Signaler un blocage

Transmettez à votre contact Grace le client et sa version, le système et son architecture, la version du plugin, l’étape et le message d’erreur. Excluez tokens, cookies, clés privées et contenu privé des projets.

N’ouvrez pas d’Issue publique avec des informations privées.

## Licence

Le plugin est un logiciel propriétaire de HoppR : voir [LICENSE.md](LICENSE.md). Les composants tiers sont listés dans [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md).

Ce dépôt reçoit uniquement des paquets fabriqués par l’intégration continue de HoppR. Une modification manuelle n’est pas une nouvelle version vérifiée.
