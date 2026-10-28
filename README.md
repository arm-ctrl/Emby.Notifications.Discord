# Notifications Emby vers Discord

Ce dépôt contient le code du plugin Emby qui envoie des notifications vers un webhook Discord.

Cette copie est une reprise communautaire du projet [MediaBrowser/Emby.Notifications.Discord](https://github.com/MediaBrowser/Emby.Notifications.Discord). Elle est indépendante du projet officiel Emby. Voir [LICENSE.md](LICENSE.md) pour les conditions de licence.

## État de cette copie

- La version de départ est la **1.2.3.0**.
- La version de travail est la **1.2.4.0**.
- La modification 1.2.4.0 filtre les notifications identiques envoyées au même webhook dans une fenêtre de cinq secondes. La déduplication tient compte du type et de la date de l’événement ainsi que du contenu du message. Elle est en mémoire et est réinitialisée au redémarrage d’Emby.
- Le problème a été observé dans les journaux : un seul événement Emby pouvait provoquer trois ou quatre requêtes distinctes vers Discord.
- La compilation de la version 1.2.4.0 a réussi. Le résultat vient d’être installé pour essai sur un Synology DS220+ sous DSM 7.4.1-90080, avec Emby Server 4.11.0.9-724110009. **La vérification avec un vrai événement est encore à faire.**

Le bouton « Envoyer une notification de test » ne valide pas cette correction : il suit un chemin différent. Il faut observer une notification déclenchée par un événement réel d’Emby.

## Reprendre le travail plus tard

1. Vérifier le prochain événement réel : une notification Emby doit apparaître une seule fois sur Discord.
2. Si elle apparaît encore plusieurs fois, noter l’heure, le type d’événement et le nombre de copies. Comparer avec les journaux Emby de cette période. Ne pas publier les journaux bruts : ils peuvent contenir des adresses IP et des URL de webhook.
3. Si le comportement est corrigé, mettre à jour cette section et `CHANGELOG.md` avec le résultat de l’essai.
4. Recompiler, vérifier la version, puis préparer une publication dans un dépôt GitHub personnel.

### Attention avant une publication GitHub

Avant tout push, vérifier `git remote -v` et pousser uniquement vers un dépôt sous notre contrôle. Le dépôt `MediaBrowser/Emby.Notifications.Discord` est le projet source amont.

Les journaux Emby et le dossier `artifacts/` sont exclus de Git par `.gitignore`. Vérifier `git status` avant chaque commit pour s’assurer qu’aucun journal, webhook ou autre donnée privée n’est inclus.

## Compilation

Le projet cible `netstandard2.0`. Une compilation Release a été vérifiée avec le SDK .NET 8 :

```sh
dotnet build Emby.Notifications.Discord.sln --configuration Release
```

Le DLL compilé se trouve sous `Emby.Notifications.Discord/bin/Release/netstandard2.0/`. Le ZIP d’essai local, s’il existe, est dans `artifacts/` et n’est pas suivi par Git.

## Installation manuelle sur Synology

Pour remplacer le DLL d’un plugin déjà installé :

1. Arrêter Emby depuis le Centre de paquets DSM.
2. Conserver une copie du DLL actuellement installé avant de le remplacer. Sur cette installation, le fichier est `/var/packages/EmbyServer/var/plugins/Emby.Notifications.Discord.dll`.
3. Envoyer le nouveau DLL dans un dossier où le compte SSH peut écrire, par exemple son dossier personnel, puis le copier vers le dossier du plugin avec `sudo`.
4. Redémarrer Emby depuis le Centre de paquets et vérifier la version affichée dans le tableau de bord.

La sauvegarde créée lors du premier essai est `Emby.Notifications.Discord.dll.bak` dans le dossier du plugin. Pour revenir à cette copie, arrêter Emby, restaurer ce fichier vers `Emby.Notifications.Discord.dll`, puis redémarrer Emby.

Ne pas désinstaller le plugin pour effectuer une mise à jour du DLL : le remplacement conserve la configuration existante.

## Configuration du plugin

### URL du webhook Discord (obligatoire)

URL du webhook entrant créé dans les paramètres du salon ou du serveur Discord.

### URL de l’avatar (facultatif)

Remplace l’avatar du webhook. Laisser le champ vide pour utiliser l’avatar configuré dans Discord.

### Nom d’utilisateur du webhook (facultatif)

Remplace le nom associé au webhook. Laisser le champ vide pour utiliser celui configuré dans Discord.

### Remplacement du nom du serveur

Permet de remplacer les occurrences de « Emby Server » par le nom du serveur dans les notifications et leur pied de page.

### Notifications d’ajout de médias

Remplace la notification par défaut par un embed contenant des informations plus riches sur le média ajouté. Les types de médias à notifier peuvent être choisis dans la configuration.

### Masquer les liens externes du serveur

Exclut des notifications les liens vers le serveur Emby, par exemple les liens de titre ou d’images. À activer si l’URL du serveur ne doit pas être exposée.

### Type de mention

Choisir `@here`, `@everyone` ou aucune mention. `@everyone` mentionne tous les membres; `@here` cible les membres actuellement en ligne.

### Couleur de l’embed

Définit la couleur d’accent de l’embed, avec le sélecteur de couleur ou une valeur hexadécimale. La valeur par défaut est le vert.

## Liens du projet d’origine

- [Dépôt source](https://github.com/MediaBrowser/Emby.Notifications.Discord)
- [Discussion sur le forum Emby](https://emby.media/community/index.php?/topic/82370-new-plugin-discord-notifications/)
