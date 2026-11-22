# Gestionnaire de tâches

Un gestionnaire de tâches et de suivi de projets en **un seul fichier HTML**, en français, minimaliste et **100 % hors-ligne** : pas de serveur, pas de compte, pas d'internet.

## Utilisation

1. Télécharger [`gestionnaire-taches.htm`](gestionnaire-taches.htm).
2. L'ouvrir dans un navigateur (Chrome, Edge ou Firefox) : double-clic suffit.

Les données restent **dans le navigateur** (localStorage et IndexedDB pour les pièces jointes). Si vous videz les données du navigateur, tout est effacé : pensez à sauvegarder (voir plus bas).

## Fonctionnalités

- **Tâches** : liste ou tableau, tri, filtres (statut, priorité, responsable, colonnes personnalisées), recherche, groupement, pagination, duplication, sélection multiple, commentaires.
- **Gantt** avec dépendances, jalons et chemin critique.
- **Multi-projets**, statuts, priorités et colonnes personnalisables par projet.
- **Tableau de bord** (indicateurs clés).
- **Activité du jour** : tâches par priorité, traitées aujourd'hui, glisser-déposer.
- **Bilatérale** : synthèse par projet des tâches cochées, notes, export Excel (`.xlsx`) et Markdown.
- **Pièces jointes** avec visionneuse (Word, Excel, ODF, PDF, images…).
- Thèmes, onglets réordonnables.

## Sauvegarde

- **Exporter → Sauvegarde complète** : JSON de tous les projets, avec le contenu des pièces jointes. Se réimporte avec **Importer**.
- **Exporter → Sauvegarde automatique** :
  - *Chrome / Edge* : choisissez le dossier qui contient la page. Toutes les 5 minutes (si quelque chose a changé), une sauvegarde complète `suivi-projet-auto-AAAA-MM-JJ-HHMM.json` y est écrite. **Les 5 plus récentes sont conservées**, les plus anciennes sont supprimées. À chaque nouvelle session, Chrome redemande l'autorisation : un clic dans la page suffit.
  - *Firefox* : pas d'accès aux dossiers, un téléchargement automatique a lieu au plus une fois par jour dans Téléchargements (pas de nettoyage automatique).

## Fichiers du dépôt

| Fichier | Rôle |
|---|---|
| `gestionnaire-taches.htm` | L'application complète (HTML, CSS, JS et bibliothèques embarquées) |
| `CLAUDE.md` | Notes techniques de passation : architecture, stockage, méthode de test |

## Développement

Tout est dans `gestionnaire-taches.htm` ; le code applicatif est le dernier `<script>`, en JavaScript vanilla de style ES5. Les bibliothèques embarquées (JSZip, SheetJS, Mammoth, pdf.js) ne se modifient pas. Voir [CLAUDE.md](CLAUDE.md) pour le détail.

Le principe : rester simple et utilisable au quotidien, sans ajouter de fonctionnalités superflues.
