# Grace — plugins natifs, pilote privé

Trois variantes sont disponibles dans chaque paquet candidat : **Codex, Claude Code et GitHub Copilot CLI**. Le catalogue s’appelle `grace-native`, le plugin `grace`. Vérifiez la cible dans `provenance.json` et dans le nom de l’archive.

Les commandes d’installation et de mise à jour ci-dessous concernent le pilote **macOS Apple Silicon (arm64)**. Sa signature ad hoc contrôle l’intégrité du binaire. Il n’y a ni Developer ID ni notarisation.

Les archives **Linux x64** et **Windows x64** sont des candidats non signés réservés à la qualification. Vérifiez l’empreinte SHA-256 de l’archive avant extraction. Pour qualifier une installation, indiquez le dossier du catalogue extrait comme source au client. Le parcours via le dépôt distant et les cycles OAuth restent à valider sur ces deux OS.

Le binaire embarqué ne nécessite ni Node, ni Bun, ni Python sur le poste utilisateur. Git et un client compatible restent nécessaires. Ne désactivez pas globalement les protections de votre système ou de votre client pour l’installer.

## Avant l’installation

- Disposer d’un accès au dépôt privé `HoppR-tech/grace-plugin` et d’un compte Grace autorisé.
- Vérifier que votre client et macOS arm64 sont couverts par les notes de la version choisie. Les preuves historiques concernent Codex 0.164.0, Claude Code 2.1.303 et Copilot CLI 1.0.83 ; elles ne garantissent pas une autre version.
- Vérifier que le backend indiqué dans les notes de version prend en charge le parcours natif. Une connexion OAuth réussie ne prouve pas cette compatibilité.
- Si Grace est déjà configuré manuellement, conserver une sauvegarde des réglages puis désactiver seulement cette ancienne entrée Grace avant d’activer le plugin. Ne supprimez pas les autres MCP, vos instructions ou vos identifiants.
- OMP et Copilot dans VS Code ne sont **pas** qualifiés par ces trois variantes. OMP reconnaît une partie du format Claude, mais pas le hook de contexte livré ici.

Vérifiez l’accès Git, sans placer de token dans l’URL :

```sh
git ls-remote https://github.com/HoppR-tech/grace-plugin.git HEAD
```

Une empreinte et `HEAD` doivent apparaître. Un refus exige de corriger l’accès Git ; une réponse vide signifie qu’aucune branche n’est encore disponible. N’installez pas une version absente ou annoncée non utilisable.

## Installer

Choisissez **un seul** bloc, celui de votre client. Les commandes ajoutent le catalogue puis installent sa variante Grace.

### Codex

```sh
codex plugin marketplace add HoppR-tech/grace-plugin --ref main
codex plugin add grace@grace-native
```

### Claude Code

```sh
claude plugin marketplace add HoppR-tech/grace-plugin
claude plugin install grace@grace-native --scope user
```

### Copilot CLI

```sh
copilot plugin marketplace add HoppR-tech/grace-plugin
copilot plugin install grace@grace-native
```

Fermez puis relancez le client. Inspectez les permissions et approuvez les hooks uniquement si leur contenu et leur provenance correspondent au plugin choisi. Une installation réussie ne signifie pas que les hooks sont approuvés ou exécutés.

Effectuez la connexion OAuth Grace proposée par le client ; ne copiez aucun token dans un fichier du plugin. Les connexions restent indépendantes entre clients. Pour Copilot, la persistance OAuth après redémarrage n’est pas qualifiée : les essais locaux utilisaient un stockage en mémoire, qui n’est pas imposé par ce paquet. Ne présentez pas ce réglage de test comme une propriété de votre installation.

Dans un dépôt autorisé, demandez ensuite à l’agent de vérifier le contexte Grace avant de modifier du code. Il doit identifier le bon projet ou demander un choix explicite, puis accéder aux talents autorisés. Un projet incorrect, une absence de contexte ou un refus ne doit pas être contourné en inventant un identifiant de projet. Arrêtez l’essai et transmettez le message d’erreur sans credentials.

## Mettre à jour

Une nouvelle version publiée ne remplace pas immédiatement une session ouverte. Il faut actualiser le catalogue **puis** le plugin ; les commandes natives sont utilisées, sans updater Grace.

### Codex

```sh
codex plugin marketplace upgrade grace-native
codex plugin add grace@grace-native
```

### Claude Code

```sh
claude plugin marketplace update grace-native
claude plugin update grace@grace-native --scope user
```

### Copilot CLI

```sh
copilot plugin marketplace update grace-native
copilot plugin update grace@grace-native
```

Redémarrez le client, vérifiez la version affichée et refaites le contrôle du projet. Si le contenu des hooks change, leur approbation peut devoir être renouvelée. Une erreur de mise à jour ne doit pas être assimilée au maintien garanti d’un plugin actif ; vérifiez l’inventaire du client avant de reprendre.

Une installation qui suit `main` peut recevoir la nouvelle préversion. **Un tag ou commit épinglé reste volontairement figé** : il faut changer explicitement cette référence pour avancer. Les tags existants ne sont jamais déplacés.

## Intégrité et traçabilité

Chaque version livre `SHA256SUMS`, `provenance.json` et `sbom.cdx.json`. La provenance identifie le commit source et, pour une fabrication CI, l’exécution GitHub Actions. Ce document est une observation de build, **pas une attestation cryptographique**. Les sommes SHA-256 ne prouvent pas à elles seules l’identité de l’éditeur.

Après avoir cloné la référence annoncée et vérifié sa provenance par le canal de confiance de l’équipe, exécutez dans ce clone :

```sh
shasum -a 256 -c SHA256SUMS
```

Tout échec interdit d’utiliser ce paquet. Ne régénérez pas les sommes pour le faire accepter.

## Désinstaller ou revenir en arrière

Quittez le client avant le retrait. Choisissez le bloc correspondant :

```sh
codex plugin remove grace@grace-native
```

```sh
claude plugin uninstall grace@grace-native --scope user
```

```sh
copilot plugin uninstall grace@grace-native
```

Relancez le client et vérifiez que le plugin, ses hooks et son MCP ne sont plus actifs. Une désinstallation ne constitue pas une preuve de révocation OAuth : utilisez séparément les commandes de déconnexion natives si nécessaire.

Pour revenir à une version antérieure, utilisez uniquement un tag dont les notes confirment la compatibilité avec le backend courant. La première préversion n’a pas de version précédente garantie : désinstallez-la si l’essai échoue. Ne changez ni les fichiers générés ni le tag publié pour fabriquer un retour arrière.

## Signaler un blocage

Transmettez à l’équipe Grace le client et sa version, le système et son architecture, la version du plugin, l’étape et le message d’erreur. Excluez tokens, cookies, clés privées et contenu privé des projets.

Les changements se font dans `grace-platform`, puis la CI publie de nouveaux paquets dans `grace-plugin`. Une modification manuelle de ce dépôt de distribution n’est pas une nouvelle version vérifiée.
