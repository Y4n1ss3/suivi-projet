# Gestionnaire de tâches — notes de passation

Fichier unique `gestionnaire-taches.html` (~3,9 Mo, ~5000 lignes), UI en français, minimaliste, **100 % hors-ligne**. Objectif de l'utilisateur : rester minimaliste, utilisable au quotidien, sans internet. **Ne pas ajouter de fonctionnalités non demandées.**

## Architecture du fichier
- CSS : `<style>` ligne ~7 ; `<style id="dynStyle">` ~605 (couleurs dynamiques statuts/priorités).
- Libs embarquées (lignes ~893–1262, très longues, ne pas toucher) : JSZip 3.10.1, SheetJS 0.18.5, Mammoth 1.6.0, pdf.js.
- Code applicatif : DERNIER `<script>` inline (~ligne 1263), IIFE en JS vanilla style ES5.
- Sections (commentaires dans le script, numéros approximatifs) : Constantes 1267 · État 1296 · Utilitaires 1313 · Configuration 1366 · Stockage 1524 · Filtrage/tri 1609 · Rendu commun 1704 · Chemin critique 1890 · Gantt 1935 · Tableau de bord 2071 · Activité du jour 2386 · Bilatérale 2500 · Édition d'une tâche 2780 · Interactions 2970 · Filtres/tri/vues 3088 · Thème 3131 · Export 3140 · Import 3216 · Multi-projets 3493 · Personnalisation 3626 · Pièces jointes (IndexedDB) 3770 · Lecture Word/Excel/ODF 4092/4563 · Sélection multiple 4852 · Alerte démarrage 4976 · Croix des fenêtres 4995 · Démarrage 5008.

## Stockage
- localStorage `suivi-projet-v2` : `{version:2, projects:[{id,name,created,tasks,fields}], bilat, currentId, author, config:{statuses,prios,fields(legacy)}, ui:{view,sort,reverse,theme,zoom,cascade,critical,scope,group,page,daySort,dayRev,tabOrder,prio,who,cf,...}}`. Ancienne clé `suivi-projet-v1`.
- Pièces jointes : IndexedDB `suivi-projet-fichiers`. Préférence alerte de démarrage : `suivi-projet-alerte`.
- Tâche : `id,title,desc,status,prio,assignee,start,due,tags,deps,comments[{id,author,date,text}],milestone,custom{},files[],created,lastAct,bilat,bnote`.
- Statuts : kinds `todo/doing/blocked/done` (ids par défaut todo, doing, review, blocked, done). Priorités : `PRIO_LIST` (haute en premier).
- Colonnes personnalisées **par projet** (`p.fields`, `FIELDS` chargé par `setActive`, copié par `syncCurrent`). Vue « Tous les projets » : colonnes perso masquées (`viewFields()` renvoie []).

## Fonctions clés
`taskById, taskIn(pid,id), isDone, statusInfo, prioInfo/prioN, firstOfKind, commit() (save+render), save(), syncCurrent(), setActive(), switchProject(id,keepFilters), allEntries(), scopeTasks(), matches(), valOk(), rowHTML(t,ctx), render()`, `renderDay`, `renderBilat`, `bilatMarkdown`, `exportBilatXlsx`, `saveFile(name,data,mime)`, `touch(t)`, `lastActOf`, `actToday`, `collectTask/autoSave`, `cleanFields/cloneFields/viewFields`, `importText/mergeConfig/mergeFieldsIntoCurrent`, `toAllJSONText`, `cleanBilat`.
État UI : `ui.page` (tasks|dash|day|bilat), `ui.scope` (one|all), `ui.who` (NOWHO="\u0001" = non assigné), `ui.cf`, `ui.prio` (filtres partagés entre onglets), `ui.tabOrder` (pageTasks,pageDash,pageDay,pageBilat).

## Fonctionnalités (résumé)
Liste/tableau avec tri, filtres (statut, priorité, responsable, valeur de colonne), recherche, groupement, pagination ; Gantt avec dépendances, jalons, chemin critique ; commentaires ; multi-projets ; statuts/priorités/colonnes personnalisables ; tableau de bord KPI ; pièces jointes avec visionneuse (Word/Excel/ODF/PDF) ; sélection multiple ; duplication ; thèmes (dont Marseille) ; import/export JSON/Excel, « Sauvegarde complète » ; alerte de démarrage sur les données stockées dans le navigateur.
Ajouts récents :
- **Activité du jour** : tâches par priorité ; une tâche avec action/commentaire aujourd'hui passe en « Traitées aujourd'hui » et revient le lendemain ; tri par colonnes (dont échéance).
- Onglets réordonnables par glisser-déposer (`ui.tabOrder`).
- Édition d'une tâche : **sauvegarde automatique** (350 ms de debounce sur les textes) ; bouton Enregistrer uniquement à la création ; la croix ferme directement.
- **Bilatérale** : seules les tâches cochées « bilat » apparaissent (terminées ou non) ; par projet : en-tête avec % terminées/bloquées/en retard + « Ouvrir le projet », briques « Terminées » (au-dessus) puis « À faire » en liste à puces ; notes façon commentaires Word (icône au survol de la ligne) ; points manuels ; export Excel mis en page (`.xlsx`) et Markdown (l'export PDF a été retiré à la demande).
- Colonnes personnalisées par projet.

## Limites connues
- Dans l'artefact publié le navigateur peut bloquer les téléchargements : utiliser le fichier hors-ligne.
- Import multi-projets : seulement « Ajouter les projets » ou « Tout remplacer » (remplacer efface points bilatérale, drapeaux et notes).
- Les notes comptent comme activité du jour.
- Nextcloud ne peut pas exécuter de HTML : télécharger le fichier en local, ou dossier statique hébergé par un admin. Sync WebDAV non construite.

## Idées non demandées (ne pas implémenter sans accord)
Ajout auto des tâches en retard/dormantes à la bilatérale, champ « décision prise », résumé copiable, date de dernière bilatérale, fusion par projet à l'import, sync WebDAV, colonnes perso en vue « Tous les projets ».

## Méthode de travail / tests
- Modifier via scripts de patch Python avec `rep(a,b)` qui vérifie **exactement une** occurrence.
- Extraire le dernier `<script>` et lancer `node --check`.
- Dans les chaînes JS, échapper les apostrophes (`d\'une`).
- Aucun U+FFFD dans le fichier (la publication le refuse).
- Tests Playwright (Chromium préinstallé) sur `python3 -m http.server` ; pré-remplir `localStorage['suivi-projet-alerte']='1'` pour éviter l'alerte.
- Vérifier visuellement les xlsx : `soffice --headless --convert-to pdf` + `pdftoppm`.
- Publication Claude (optionnelle) : `Artifact action:read` sur l'URL avant de republier ; URL : https://claude.ai/artifact/JDACmfj7D7fyFVJsiFxbN2 (accessible seulement depuis claude.ai).

## Données de test
`taches-test.json` (« Projet test », 14 tâches), `vdm-projets-2026-10-12.json` (8 projets / 100 tâches, import VDM), `bilaterale-exemple.xlsx`.
