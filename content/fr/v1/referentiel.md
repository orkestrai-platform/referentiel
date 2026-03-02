# Référentiel OrkestrAI Standard v1

_Source: page référentiel historique OrkestrAI, convertie en Markdown pour publication open source._

# Référentiel OrkestrAI

100 points. 5 axes. 19 critères. Le standard de qualité pour l'orchestration organique du développement assisté par IA.

Standard v1

Le référentiel OrkestrAI est un cadre d'évaluation complet, conçu pour mesurer la qualité
du code produit en collaboration avec des outils d'intelligence artificielle. Il ne s'agit pas
de juger si l'IA a été utilisée — **mais de vérifier que son utilisation a été maîtrisée,
encadrée et guidée par un développeur compétent**.

Chaque critère a été pensé par des développeurs seniors confrontés quotidiennement aux réalités
du code généré par IA. Notre ambition : créer un langage commun pour évaluer la qualité du
code à l'ère de l'IA, qu'il s'agisse d'un audit de projet, d'une revue de code ou d'un
processus de certification.

Le référentiel est organisé en **5 axes** allant de l'architecture logicielle
à la sécurité, en passant par l'orchestration IA elle-même. Les 100 points sont répartis
selon l'impact de chaque domaine sur la qualité globale du code. L'architecture pèse le plus
lourd (30 points) car c'est la fondation sur laquelle tout le reste repose.

[Niveaux](#niveaux)
[Architecture (30 pts)](#axe-1)
[Orchestration IA (25 pts)](#axe-2)
[Qualité du code (20 pts)](#axe-3)
[Performance (15 pts)](#axe-4)
[Sécurité (10 pts)](#axe-5)
[Répartition](#repartition)
[Processus d'audit](#processus)
[FAQ](#faq)

100

points au total

5

axes d'évaluation

19

critères de notation

## Niveaux de certification

Chaque niveau représente un palier de maîtrise dans l'orchestration du code IA. Le score est calculé sur 100 points, chaque point correspondant à une pratique concrète et vérifiable.

### Non certifié

< 60

Score insuffisant. Des améliorations significatives sont nécessaires avant de pouvoir prétendre à la certification.

### Certified

60 — 74

Maîtrise des fondamentaux. Le code est structuré, l'IA est guidée, les bases de la qualité sont respectées.

### Certified Pro

75 — 89

Expertise avancée. Architecture solide, orchestration méthodique, qualité et sécurité au rendez-vous.

### Certified Expert

90 — 100

Excellence sur tous les axes. Référence en matière d'orchestration IA. Un modèle pour la profession.

## Axe 1 — Architecture

30 points

L'humain a guidé l'IA vers une architecture propre, maintenable et évolutive.

**Pourquoi 30 points ?** L'architecture est la fondation de tout projet logiciel. Un code bien architecturé
est un code que l'on peut faire évoluer, tester et maintenir sur le long terme. Or, c'est précisément là que l'IA
a le plus besoin d'être guidée : les modèles de langage génèrent du code qui fonctionne dans l'immédiat,
mais rarement du code qui s'inscrit dans une vision architecturale cohérente. La séparation des responsabilités,
l'inversion de dépendances, la modélisation du domaine — ces choix fondamentaux ne peuvent être délégués à l'IA.
Ils sont le territoire du développeur senior, de l'architecte, de l'orchestrateur.

### 1.1 Séparation des responsabilités

0 — 8 pts

Ce critère évalue si le code est organisé en couches distinctes avec des responsabilités claires.
Une bonne séparation des responsabilités est le premier signe qu'un humain a guidé l'architecture
plutôt que de laisser l'IA empiler du code dans un seul fichier. **C'est le critère le plus
lourd du référentiel** car il conditionne la maintenabilité de tout le projet.

| Score | Critère |
| --- | --- |
| 0 | Tout dans un fichier / God class / pas de structure. L'IA a généré sans aucun guidage architectural. |
| 2 | Séparation basique (controllers vs le reste). Une intention de structurer existe, mais elle reste superficielle. |
| 5 | Couches identifiables (présentation, application, domaine, infrastructure). Le développeur a imposé une structure en couches à l'IA. |
| 8 | Architecture hexagonale ou clean architecture appliquée — les dépendances pointent vers le domaine. L'inversion de contrôle est respectée systématiquement. |

**Exemple concret :** Dans un projet Symfony, le score maximum implique que le dossier `src/`
est organisé par bounded context, chaque contexte ayant ses sous-dossiers Domain/, Application/ et Infrastructure/.
Les entités du domaine ne contiennent aucune annotation framework. Les repositories sont définis comme interfaces
dans le domaine et implémentés dans l'infrastructure.

**Comment auditer :** Vérifier l'arborescence des fichiers. Analyser les imports : est-ce que le domaine importe de l'infrastructure ? Tracer le sens des dépendances avec un outil d'analyse statique. Vérifier que les controllers ne contiennent pas de logique métier.

### 1.2 Gestion des dépendances

0 — 6 pts

Ce critère mesure le niveau de couplage entre les composants du système. L'injection de dépendances
et l'inversion de contrôle sont des piliers de la Clean Architecture. Un code fortement couplé est
un code qui ne peut pas être testé en isolation — et c'est souvent le résultat d'une génération IA
non supervisée, où chaque classe instancie directement ses dépendances.

| Score | Critère |
| --- | --- |
| 0 | Instanciations directes partout, couplage fort entre toutes les couches. Modifier un composant oblige à modifier tous les autres. |
| 3 | Injection de dépendances utilisée mais incohérente. Certains services sont injectés, d'autres instanciés à la main. |
| 6 | DI systématique, interfaces pour les dépendances externes, inversion de contrôle respectée. Le domaine ne dépend de rien d'extérieur. |

**Exemple concret :** Le domaine définit `ProjectRepositoryInterface`. L'infrastructure fournit
`DoctrineProjectRepository`. Le binding est fait dans la configuration du conteneur de services.
Aucun `new` dans le domaine pour des services ou repositories.

**Comment auditer :** Chercher les `new` dans le domaine. Vérifier les constructeurs des services. S'assurer que toutes les dépendances externes (BDD, API, filesystem) sont derrière des interfaces.

### 1.3 Modélisation du domaine

0 — 6 pts

La qualité de la modélisation du domaine révèle si le développeur a réfléchi au métier avant de coder.
L'IA a tendance à produire des modèles anémiques (de simples conteneurs de données avec des getters/setters)
car c'est le pattern le plus fréquent dans ses données d'entraînement. Un domaine riche, avec des entités
qui portent leur logique métier, des Value Objects immuables et des agrégats bien définis, est la marque
d'un développeur qui a orchestré l'IA avec une vision DDD.

| Score | Critère |
| --- | --- |
| 0 | Modèles anémiques, logique dans les controllers. Les entités ne sont que des structures de données sans comportement. |
| 3 | Entités avec de la logique mais pas de Value Objects, pas d'agrégats clairs. Le domaine existe mais manque de rigueur. |
| 6 | DDD appliqué : entités riches, Value Objects immuables, agrégats avec racines claires, ubiquitous language cohérent dans tout le code. |

**Exemple concret :** Une entité `Audit` expose des méthodes comme `score()`,
`complete()`, `certificationLevel()` — la logique métier vit dans le domaine. Les identifiants
sont des Value Objects typés (`AuditId`, `ProjectId`) et non de simples chaînes de caractères.
Le langage du code reflète le langage du métier.

**Comment auditer :** Lire les entités du domaine. Vérifier qu'elles portent de la logique métier et pas seulement des getters. Chercher des Value Objects. Vérifier que le nommage utilise le vocabulaire métier (ubiquitous language).

### 1.4 Testabilité

0 — 5 pts

Un code bien architecturé est un code testable. Ce critère évalue non seulement la présence de tests,
mais surtout la capacité du code à être testé en isolation. L'IA génère souvent du code difficile à
tester car fortement couplé à l'infrastructure (base de données, APIs externes). Un bon orchestrateur
structure le code pour que le domaine puisse être testé sans aucune dépendance externe.

| Score | Critère |
| --- | --- |
| 0 | Aucun test. Le projet ne peut pas prouver que son code fonctionne correctement. |
| 2 | Tests présents mais couplés à l'infra (DB, API). Les tests sont lents, fragiles et difficiles à maintenir. |
| 4 | Tests unitaires du domaine isolés. Le domaine est testé indépendamment de toute infrastructure. |
| 5 | Pyramide de tests respectée (unit > integration > e2e), domaine testé en isolation complète, couverture significative des cas métier. |

**Exemple concret :** Les tests du domaine ne nécessitent aucune base de données. Un `ExamSessionTest`
vérifie les règles métier (score, expiration, niveaux) en instanciant directement les entités.
Les tests d'intégration vérifient que les repositories Doctrine fonctionnent. Les tests e2e vérifient les parcours utilisateur.

**Comment auditer :** Ratio de tests. Couverture du domaine. Présence de mocks/stubs pour l'infra. Temps d'exécution de la suite de tests. Vérifier que les tests du domaine passent sans base de données.

### 1.5 Modularité et couplage

0 — 5 pts

Ce critère évalue si le projet est découpé en modules autonomes avec des frontières claires.
L'IA ne connaît pas les bounded contexts de votre projet — c'est au développeur de les définir
et de s'assurer que chaque module a une responsabilité claire, des contrats bien définis avec
les autres modules, et un minimum de dépendances croisées.

| Score | Critère |
| --- | --- |
| 0 | Tout est interconnecté. Modifier un fichier risque de casser le reste. Pas de frontières entre les concepts. |
| 3 | Modules identifiables mais des dépendances croisées subsistent. La séparation est intentionnelle mais incomplète. |
| 5 | Modules autonomes, contrats clairs entre bounded contexts, faible couplage. Chaque module pourrait être extrait sans impacter les autres. |

**Exemple concret :** Un projet organisé en bounded contexts (Audit, Certification, Exam, Security)
où chaque contexte a ses propres entités, repositories et controllers. Les communications entre contextes
passent par des interfaces partagées dans un Shared Kernel, jamais par des imports directs.

**Comment auditer :** Graphe de dépendances entre modules. Vérifier les imports croisés. S'assurer qu'aucun module n'accède directement aux détails internes d'un autre. Utiliser des outils comme deptrac ou phparkitect.

## Axe 2 — Orchestration IA

25 points

L'humain a piloté l'IA avec méthode, traçabilité et validation.

**Pourquoi 25 points ?** C'est l'axe qui distingue OrkestrAI de tout autre référentiel de qualité logicielle.
Il évalue non pas le code en lui-même, mais la manière dont l'humain et l'IA ont collaboré pour le produire.
La traçabilité des générations, la validation humaine systématique, la reproductibilité des résultats et la maîtrise
du périmètre confié à l'IA — ce sont les marqueurs d'une utilisation professionnelle et responsable de l'intelligence
artificielle dans le développement. Un projet peut avoir un code parfait ; s'il a été généré sans supervision,
c'est un château de cartes.

### 2.1 Traçabilité des générations

0 — 7 pts

Peut-on identifier, pour chaque portion du code, si elle a été générée par IA ou écrite manuellement ?
La traçabilité est essentielle pour la maintenance : quand un bug survient dans du code généré, il faut
pouvoir retrouver le contexte de génération, le prompt utilisé, et les décisions prises. Sans traçabilité,
le code IA devient une boîte noire impossible à débugger efficacement.

| Score | Critère |
| --- | --- |
| 0 | Impossible de savoir ce qui a été généré par IA vs écrit à la main. Aucune convention, aucune trace. |
| 3 | Mentions dans les commits ou la documentation, mais pas systématique. L'effort de traçabilité est partiel. |
| 5 | Convention de commits identifiant les générations IA (tag, préfixe). On peut filtrer l'historique pour isoler le code généré. |
| 7 | Historique complet : prompts documentés, fichiers générés identifiés, versioning des prompts. Chaque génération est reproductible et auditable. |

**Exemple concret :** Les commits de code généré par IA incluent un tag `[AI]` dans le message
et un `Co-Authored-By`. Un dossier `.prompts/` à la racine du projet contient les prompts versionnés
utilisés pour chaque fonctionnalité majeure. Un fichier CLAUDE.md ou un ADR documente les sessions d'orchestration.

**Comment auditer :** Analyser le git log. Chercher des conventions de commit IA. Vérifier l'existence d'un dossier de prompts. Demander à l'équipe de retracer l'origine d'un fichier précis.

### 2.2 Validation humaine

0 — 7 pts

Le code généré par IA a-t-il été systématiquement relu, compris et validé par un humain avant d'être intégré ?
La validation humaine est la clé de voûte de l'orchestration. Un développeur qui copie-colle du code IA sans
le comprendre n'orchestre pas — il délègue aveuglément. La validation implique la compréhension, la revue
critique et l'approbation explicite de chaque génération.

| Score | Critère |
| --- | --- |
| 0 | Code IA copié-collé sans relecture. Aucune preuve de compréhension humaine du code intégré. |
| 3 | Relecture partielle, pas de processus défini. Le développeur relit parfois mais sans méthode. |
| 5 | Code review systématique du code généré, checklist de validation appliquée à chaque intégration. |
| 7 | Processus documenté : chaque génération IA passe par une review senior + des tests avant merge. Le processus est vérifiable dans l'historique. |

**Exemple concret :** Chaque PR contient une section "Code généré par IA" avec les fichiers concernés.
Une checklist de validation (tests passent, conventions respectées, pas de faille de sécurité, logique métier correcte)
est cochée avant le merge. Les PR sans validation sont bloquées par les règles du repository.

**Comment auditer :** Consulter les PRs et l'historique de review. Vérifier l'existence d'une checklist de validation. Demander à l'équipe de décrire leur processus de validation du code IA.

### 2.3 Reproductibilité

0 — 6 pts

Les résultats de l'IA sont-ils reproductibles ? Si on relançait le même prompt dans le même contexte,
obtiendrait-on un résultat similaire ? La reproductibilité passe par la documentation des prompts,
du contexte fourni à l'IA, et des paramètres utilisés. C'est un principe fondamental de la démarche
scientifique appliqué à l'ingénierie logicielle assistée par IA.

| Score | Critère |
| --- | --- |
| 0 | Prompts jetés après usage, aucune mémoire. Impossible de comprendre comment le code a été généré. |
| 3 | Prompts sauvegardés mais pas organisés. On retrouve des traces mais sans méthode ni structure. |
| 6 | Bibliothèque de prompts versionnée, contexte documenté, résultat reproductible. Un nouveau développeur peut reprendre le workflow IA du projet. |

**Exemple concret :** Un dossier `.prompts/` contient des templates de prompts organisés par domaine
(architecture, tests, refactoring). Chaque template inclut le contexte à fournir, les contraintes à préciser,
et un exemple de résultat attendu. Les prompts sont versionnés avec le code.

**Comment auditer :** Chercher un dossier de prompts. Vérifier si les templates sont versionnés. Demander à l'équipe de reproduire une génération passée. Évaluer la documentation du workflow IA.

### 2.4 Maîtrise du périmètre IA

0 — 5 pts

Le développeur a-t-il clairement défini ce que l'IA peut et ne peut pas faire dans le projet ?
Un bon orchestrateur sait quand utiliser l'IA et quand s'en passer. Il définit les contraintes,
les conventions et les limites avant de lancer une génération. Les décisions architecturales,
la modélisation du domaine et les choix de sécurité ne doivent jamais être délégués à l'IA sans supervision.

| Score | Critère |
| --- | --- |
| 0 | L'IA a généré l'intégralité sans cadrage. Aucune contrainte n'a été posée, aucune limite définie. |
| 2 | L'humain a défini les grandes lignes mais l'IA a eu carte blanche sur l'implémentation détaillée. |
| 5 | Périmètre IA clairement défini : l'humain décide de l'architecture, l'IA implémente sous contraintes explicites documentées (CLAUDE.md, ADR, conventions). |

**Exemple concret :** Un fichier `CLAUDE.md` à la racine du projet définit les conventions architecturales,
les patterns à utiliser, les règles de nommage et les interdictions (pas d'annotations dans le domaine,
pas d'instanciation directe, tests obligatoires). L'IA est guidée par ces contraintes à chaque génération.

**Comment auditer :** Vérifier l'existence de documents de cadrage (ADR, CLAUDE.md, conventions). Demander à l'équipe quel était le périmètre confié à l'IA. Comparer la documentation des décisions avec le code livré.

## Axe 3 — Qualité du code

20 points

Le code livré est lisible, cohérent et maintenable — quel que soit qui l'a écrit.

**Pourquoi 20 points ?** La qualité du code est ce que l'on voit quand on ouvre un fichier source.
C'est le critère le plus tangible et le plus immédiat. Un code de qualité se lit comme un texte bien écrit :
chaque variable a un nom intentionnel, chaque fonction fait une seule chose, chaque fichier respecte les mêmes
conventions. L'IA peut générer du code de qualité variable — c'est au développeur de s'assurer que le résultat
final est homogène, lisible et maintenable. La qualité du code est aussi le meilleur indicateur de la santé
à long terme d'un projet : un code propre aujourd'hui, c'est un code qui sera encore compréhensible dans 6 mois.

### 3.1 Lisibilité et nommage

0 — 6 pts

Le code est-il auto-documenté ? Les noms de variables, fonctions et classes expriment-ils clairement
leur intention ? L'IA génère souvent des noms génériques (`data`, `result`, `temp`)
ou reproduit des conventions d'exemples de documentation. Un bon orchestrateur impose un nommage intentionnel
et cohérent qui reflète le langage du domaine métier.

| Score | Critère |
| --- | --- |
| 0 | Nommage incohérent, variables obscures, code spaghetti. On ne comprend pas le code sans effort significatif. |
| 3 | Nommage correct dans l'ensemble, quelques incohérences. Le code est lisible mais pas toujours expressif. |
| 6 | Nommage intentionnel et uniforme, code auto-documenté, ubiquitous language respecté dans tout le projet. |

**Exemple concret :** Au lieu de `$data = $repo->get($id)`, le code dit
`$project = $this->projectRepository->findById(ProjectId::fromString($id))`.
Chaque nom révèle l'intention. Les méthodes sont nommées selon les actions métier : `complete()`,
`certificationLevel()`, pas `process()` ou `handle()`.

**Comment auditer :** Lecture du code source. Vérifier la cohérence des conventions de nommage. Rechercher des patterns de nommage générique. Vérifier que le vocabulaire du code correspond au vocabulaire métier.

### 3.2 Complexité

0 — 5 pts

Les fonctions sont-elles courtes et focalisées ? La complexité cyclomatique est-elle maîtrisée ?
L'IA a tendance à générer des fonctions longues avec de nombreux branchements conditionnels, car elle
optimise pour la complétude plutôt que pour la simplicité. Un orchestrateur veille à ce que chaque
fonction opère à un seul niveau d'abstraction et reste compréhensible d'un seul regard.

| Score | Critère |
| --- | --- |
| 0 | Fonctions de 200+ lignes, complexité cyclomatique > 20. Le code est un labyrinthe de conditions imbriquées. |
| 3 | Complexité maîtrisée dans l'ensemble, quelques points noirs identifiés mais limités. |
| 5 | Complexité cyclomatique < 10 partout, fonctions courtes, un seul niveau d'abstraction par fonction. Le code respire. |

**Exemple concret :** Aucune méthode ne dépasse 20 lignes. Les conditions complexes sont extraites
dans des méthodes nommées (`isEligibleForCertification()` plutôt qu'une longue chaîne de if/else).
PHPStan/Psalm au niveau maximum ne remonte aucune alerte de complexité.

**Comment auditer :** Utiliser des outils d'analyse statique (phpstan, phpmd, sonar). Mesurer la complexité cyclomatique. Identifier les fonctions les plus longues. Vérifier les niveaux d'imbrication.

### 3.3 Cohérence des patterns

0 — 5 pts

Le même problème est-il résolu de la même manière partout dans le projet ? L'un des défauts les plus
fréquents du code généré par IA est l'incohérence stylistique : chaque génération peut proposer un
pattern différent pour le même type de problème. Un bon orchestrateur impose des conventions et vérifie
que l'IA les respecte systématiquement.

| Score | Critère |
| --- | --- |
| 0 | Chaque fichier utilise un style différent. Mix de patterns, conventions contradictoires, impression de code écrit par 10 personnes différentes. |
| 3 | Style globalement cohérent avec des écarts ponctuels. L'intention de cohérence est visible. |
| 5 | Un seul style architectural, conventions appliquées uniformément, linter/formatter configuré et appliqué. Tous les fichiers similaires se ressemblent. |

**Exemple concret :** Tous les controllers suivent le même pattern (injection de dépendances, validation, délégation au domaine, réponse).
Tous les repositories implémentent la même interface. Tous les Value Objects suivent le même template (constructeur privé, factory method, equals).
Un fichier `.php-cs-fixer.php` ou `phpcs.xml` normalise le formatage.

**Comment auditer :** Comparer des fichiers similaires (deux controllers, deux repositories). Vérifier la config du linter/formatter. S'assurer que le CI/CD vérifie le style.

### 3.4 Dette technique maîtrisée

0 — 4 pts

Le projet contient-il du code mort, des TODO orphelins, des dépendances inutilisées ou des hacks
non documentés ? L'IA génère parfois du code "au cas où" — des classes jamais utilisées, des méthodes
redondantes, des imports superflus. Un projet bien orchestré est un projet propre où chaque ligne
de code a une raison d'exister et où chaque compromis technique est documenté.

| Score | Critère |
| --- | --- |
| 0 | TODO partout, code mort, dépendances inutilisées, hacks non documentés. Le projet accumule de la dette silencieuse. |
| 2 | Quelques résidus mais identifiés et documentés. La dette est connue et priorisée. |
| 4 | Pas de code mort, pas de TODO orphelins, dépendances propres, chaque compromis est documenté dans un ADR ou un commentaire explicite. |

**Exemple concret :** `grep -r "TODO\|FIXME\|HACK" src/` ne retourne aucun résultat.
`composer why` justifie chaque dépendance. Les rares compromis techniques sont documentés
dans des ADR (Architecture Decision Records) avec la raison du choix et les alternatives envisagées.

**Comment auditer :** Rechercher TODO/FIXME/HACK dans le code. Analyser les dépendances inutilisées. Identifier le code mort avec des outils d'analyse statique. Vérifier la présence d'ADR pour les compromis.

## Axe 4 — Performance et sobriété

15 points

Le code est efficace et ne gaspille pas de ressources.

**Pourquoi 15 points ?** La performance et la sobriété sont des enjeux de plus en plus critiques
dans le développement logiciel. L'IA ne se soucie pas des performances — elle génère le code le plus "probable",
pas le plus efficace. Les problèmes classiques du code généré par IA incluent les requêtes N+1, les dépendances
superflues, le chargement excessif de données et l'absence totale de considérations Green IT. Un orchestrateur
responsable veille à ce que le code soit non seulement fonctionnel et bien structuré, mais aussi sobre et
respectueux des ressources. L'éco-conception n'est pas un luxe — c'est une responsabilité.

### 4.1 Efficacité des requêtes

0 — 5 pts

Les accès aux données sont-ils optimisés ? L'un des pièges les plus fréquents du code IA est le
problème N+1 : une boucle qui exécute une requête par itération au lieu d'un seul chargement.
L'IA ne "voit" pas les implications de performance de ses choix — c'est au développeur de vérifier
que les requêtes sont efficaces, indexées et paginées.

| Score | Critère |
| --- | --- |
| 0 | N+1 queries, requêtes en boucle, pas d'index. Chaque page charge des centaines de requêtes inutiles. |
| 3 | Requêtes correctes dans l'ensemble, quelques optimisations manquantes mais pas de problème critique. |
| 5 | Requêtes optimisées, eager loading quand nécessaire, index pertinents, pagination systématique, profiling effectué. |

**Exemple concret :** Le Symfony Profiler montre un nombre de requêtes raisonnable par page (< 10).
Les collections sont chargées avec des JOIN quand nécessaire. Les listes sont paginées.
Les colonnes fréquemment requêtées sont indexées. Pas de `findAll()` suivi d'un filtre PHP.

**Comment auditer :** Utiliser le profiler Symfony (debug toolbar). Compter les requêtes par page. Vérifier les index en base. Tester avec un volume de données significatif.

### 4.2 Sobriété des dépendances

0 — 4 pts

Chaque dépendance du projet est-elle justifiée ? L'IA suggère fréquemment des bibliothèques tierces
pour des tâches simples, créant un bloat de dépendances qui alourdit le projet, augmente la surface
d'attaque et complique les mises à jour. Un bon orchestrateur challenge chaque `composer require`
et préfère le code natif quand c'est raisonnable.

| Score | Critère |
| --- | --- |
| 0 | 50 packages pour un CRUD, dépendances dupliquées, libs inutilisées. Le `vendor/` pèse plus que le code métier. |
| 2 | Dépendances raisonnables mais quelques superflues identifiées. |
| 4 | Chaque dépendance est justifiée, pas de bloat, versions maintenues, audit de sécurité des dépendances effectué. |

**Exemple concret :** Le `composer.json` ne contient que les dépendances strictement nécessaires.
Pas de bibliothèque de 10 000 lignes pour une seule fonction utilitaire. `composer audit` ne remonte
aucune vulnérabilité. Les versions sont contraintes avec soin (pas de `*`).

**Comment auditer :** Analyser le composer.json / package.json. Exécuter `composer audit`. Vérifier que chaque dépendance est effectivement utilisée. Comparer le ratio code métier / dépendances.

### 4.3 Gestion des ressources

0 — 3 pts

Le code gère-t-il correctement les ressources système ? Fichiers, connexions, mémoire — chaque ressource
ouverte doit être fermée, chaque allocation doit être libérée. L'IA oublie souvent ces détails car ils
ne sont pas nécessaires pour que le code "fonctionne" en apparence, mais ils causent des problèmes
insidieux en production.

| Score | Critère |
| --- | --- |
| 0 | Fuites mémoire, fichiers non fermés, connexions non libérées. Le code fonctionne en dev mais crash en prod. |
| 2 | Gestion correcte dans l'ensemble, pas de fuite évidente détectée. |
| 3 | Gestion explicite des ressources, lazy loading, caching pertinent. Le code est pensé pour tourner en production avec un volume réel. |

**Exemple concret :** Les streams sont fermés dans des blocs finally. Le lazy loading est utilisé
pour les collections volumineuses. Un système de cache (HTTP, applicatif) est en place pour les données
rarement modifiées. La mémoire est monitorée dans le profiler.

**Comment auditer :** Profiling mémoire. Revue des streams et connexions. Vérifier les stratégies de cache. Tester avec un volume de données réaliste.

### 4.4 Éco-conception

0 — 3 pts

Le projet intègre-t-il des considérations de sobriété numérique ? À l'heure où le numérique représente
4 % des émissions mondiales de gaz à effet de serre, l'éco-conception est un devoir professionnel.
Un orchestrateur responsable minimise l'empreinte de son code : payloads légers, compression, cache HTTP,
images optimisées, pas de requêtes superflues.

| Score | Critère |
| --- | --- |
| 0 | Aucune considération Green IT. Pages de 5 Mo, centaines de requêtes, aucun cache. |
| 2 | Pas de gaspillage flagrant. Le site est raisonnablement léger sans effort délibéré. |
| 3 | Choix techniques documentés favorisant la sobriété : payloads compressés, cache HTTP configuré, lazy loading des images, CSS/JS minifiés, pas de tracking superflu. |

**Exemple concret :** Le CSS est inline et minifié (pas de framework CSS de 300 Ko pour un simple site).
Les images sont au format WebP avec lazy loading. Les headers de cache sont configurés.
Lighthouse affiche un score performance > 90. Le poids total d'une page est inférieur à 500 Ko.

**Comment auditer :** Mesurer le poids des pages avec les DevTools. Compter le nombre de requêtes. Vérifier les headers de cache. Lancer un audit Lighthouse. Évaluer la stratégie globale de sobriété.

## Axe 5 — Sécurité

10 points

Le code ne présente pas de vulnérabilités connues et protège les données des utilisateurs.

**Pourquoi 10 points ?** La sécurité est un sujet transversal qui irrigue tous les autres axes.
Si elle ne représente "que" 10 points dédiés, c'est parce que de nombreux critères des autres axes contribuent
indirectement à la sécurité (architecture propre, tests, validation humaine). Néanmoins, ces 10 points
sont critiques : une seule faille de sécurité peut compromettre tout un projet. L'IA est particulièrement
dangereuse dans ce domaine car elle reproduit les patterns vulnérables avec une confiance apparente qui
peut tromper un développeur non vigilant. Injections SQL, XSS, secrets en clair, absence de validation
— ce sont des erreurs que l'IA commet régulièrement et que l'orchestrateur doit systématiquement vérifier.

### 5.1 OWASP Top 10

0 — 4 pts

Le code est-il protégé contre les 10 vulnérabilités web les plus courantes ? Le référentiel OWASP
est le standard mondial en matière de sécurité applicative. Les failles les plus fréquentes dans
le code généré par IA sont les injections (SQL, XSS, commandes), l'absence de CSRF, la mauvaise
gestion de l'authentification et l'exposition de données sensibles dans les réponses API.

| Score | Critère |
| --- | --- |
| 0 | Injections SQL, XSS, CSRF non protégé. Le code est vulnérable aux attaques les plus basiques. |
| 2 | Protections basiques en place (ORM avec parameterized queries, échappement Twig). Les fondamentaux sont couverts. |
| 4 | OWASP Top 10 couvert : parameterized queries, CSP headers, CSRF tokens, validation des entrées côté serveur, rate limiting, headers de sécurité configurés. |

**Exemple concret :** Toutes les requêtes passent par l'ORM (pas de SQL brut). Les templates Twig
échappent automatiquement les variables. Les formulaires incluent un token CSRF. Les headers de sécurité
(X-Content-Type-Options, X-Frame-Options, CSP) sont configurés. Les entrées utilisateur sont validées
côté serveur, jamais seulement côté client.

**Comment auditer :** Lancer un scan de sécurité (OWASP ZAP, Snyk). Revue manuelle des entrées utilisateur. Vérifier les headers HTTP de sécurité. Tester les formulaires sans JavaScript pour valider la protection côté serveur.

### 5.2 Gestion des secrets

0 — 3 pts

Les secrets (mots de passe, clés API, tokens) sont-ils correctement externalisés et protégés ?
C'est l'une des erreurs les plus graves et les plus fréquentes dans le code généré par IA : des
credentials en dur dans le code source, un fichier `.env` commité par mégarde, des clés API
visibles dans l'historique git. Un seul secret fuité peut compromettre tout un système.

| Score | Critère |
| --- | --- |
| 0 | Credentials en dur dans le code, .env commité dans git. Les secrets sont exposés publiquement. |
| 2 | Variables d'environnement utilisées mais .env.example incomplet ou secrets dans l'historique git. |
| 3 | Secrets externalisés, .env.example complet, .gitignore rigoureux, pas de secret dans l'historique git. Rotation des secrets documentée. |

**Exemple concret :** Le fichier `.env` est dans le `.gitignore`. Un `.env.example`
documente toutes les variables nécessaires avec des valeurs placeholder. `git log -S "password"` ne
retourne aucun résultat suspect. Les secrets de production sont gérés par un vault ou des variables
d'environnement du serveur, jamais dans le code.

**Comment auditer :** Rechercher des patterns de secrets dans le code (grep sur password, secret, api\_key, token). Vérifier le .gitignore. Scanner l'historique git avec un outil comme truffleHog ou gitleaks. Vérifier le .env.example.

### 5.3 Protection des données

0 — 3 pts

Les données personnelles des utilisateurs sont-elles protégées conformément au RGPD et aux bonnes
pratiques de sécurité ? L'IA n'a aucune notion de conformité réglementaire. Elle peut logger des données
personnelles, stocker des mots de passe en clair, ou exposer des informations sensibles dans les réponses
API. C'est au développeur de s'assurer que chaque donnée personnelle est traitée avec le respect qu'elle mérite.

| Score | Critère |
| --- | --- |
| 0 | Données personnelles exposées, pas de validation, logs contenant des PII (données personnelles identifiables). |
| 2 | Validation des entrées, données sensibles non loguées. Les bases du RGPD sont respectées. |
| 3 | RGPD respecté : consentement implémenté, droit à l'oubli fonctionnel, données chiffrées au repos, logs nettoyés de toute PII, politique de rétention définie. |

**Exemple concret :** Les mots de passe sont hashés avec bcrypt/argon2 (jamais en clair ni en MD5).
Les logs ne contiennent ni emails, ni noms, ni adresses IP identifiantes. Un endpoint de suppression de compte
existe et anonymise toutes les données de l'utilisateur. La politique de cookies est conforme au RGPD.

**Comment auditer :** Audit RGPD complet. Revue des logs en production (chercher des PII). Vérifier le hashing des mots de passe. Tester la suppression de compte. Vérifier la politique de cookies et le consentement.

## Répartition des points

Vue d'ensemble des 100 points répartis sur les 5 axes d'évaluation. La pondération reflète l'impact de chaque domaine sur la qualité globale d'un projet où l'IA est orchestrée par le développeur.

pts

## Processus d'audit

Un audit OrkestrAI se déroule en 5 étapes rigoureuses, de la prise de contact à la délivrance du badge. Chaque étape est conçue pour garantir une évaluation objective, complète et actionnable.

1

### Cadrage

L'auditeur définit le périmètre de l'évaluation avec l'équipe projet. Cette étape permet de comprendre le contexte technique et organisationnel.

* Périmètre du projet et objectifs
* Stack technique et outils IA utilisés
* Taille de l'équipe et rôles
* Part estimée de code généré par IA
* Contraintes spécifiques (réglementaires, performance, etc.)

2

### Analyse automatisée

Des outils d'analyse statique et de sécurité fournissent une première évaluation objective et quantifiable du code.

* Analyse statique (PHPStan, Psalm, SonarQube)
* Scan de sécurité (Snyk, OWASP ZAP, gitleaks)
* Métriques de complexité cyclomatique
* Couverture de tests et qualité des assertions
* Audit des dépendances (vulnérabilités, licences)

3

### Revue humaine

Un auditeur senior lit le code, évalue chaque critère et dialogue avec l'équipe pour comprendre les choix techniques.

* Lecture approfondie du code source
* Évaluation de chaque critère du référentiel
* Entretien avec l'équipe sur le processus IA
* Vérification de la traçabilité des générations
* Analyse de l'historique git et des PRs

4

### Rapport détaillé

L'auditeur rédige un rapport complet avec le score de chaque critère, les points forts, les axes d'amélioration et des recommandations priorisées.

* Score détaillé par axe et par critère
* Points forts identifiés et valorisés
* Points faibles avec recommandations concrètes
* Plan d'action priorisé pour progresser
* Niveau de certification attribué

5

### Certification

Badge OrkestrAI délivré si score ≥ 60 — Certificat PDF avec code de vérification unique —
Widget intégrable sur votre site — Validité 12 mois — Re-certification annuelle recommandée

## Questions fréquentes

Tout ce que vous devez savoir sur le référentiel OrkestrAI et le processus de certification.

### Est-ce que l'utilisation de l'IA est obligatoire pour être certifié ?

Oui, le référentiel OrkestrAI évalue spécifiquement la qualité du code produit en collaboration avec des outils d'IA.
L'axe 2 (Orchestration IA, 25 points) mesure la manière dont l'IA a été utilisée, tracée et validée.
Un projet entièrement écrit à la main ne pourrait pas obtenir ces 25 points et aurait donc un score maximum de 75/100.

### Quels outils d'IA sont acceptés ?

Tous les outils d'IA générative pour le code sont acceptés : ChatGPT, Claude, GitHub Copilot, Cursor,
Codeium, Amazon CodeWhisperer, et tout autre outil similaire. Ce qui compte n'est pas l'outil utilisé
mais la manière dont le développeur orchestre organiquement le développement.

### Quelle est la différence entre l'examen individuel et l'audit de projet ?

L'examen individuel (QCM de 30 questions en 60 minutes) certifie les **connaissances théoriques**
d'un développeur sur les bonnes pratiques d'orchestration IA. L'audit de projet certifie la **mise en pratique**
de ces connaissances sur un projet réel. Les deux certifications sont complémentaires et indépendantes.

### Comment est calculé le score de l'audit ?

L'auditeur évalue chaque critère individuellement selon la grille de notation détaillée ci-dessus.
Chaque critère a un score minimum de 0 et un score maximum défini. Le score total est la somme
des 19 critères, sur 100 points. L'auditeur justifie chaque note avec des observations concrètes
tirées du code source.

### Que se passe-t-il si je n'atteins pas 60 points ?

Vous recevez quand même le rapport détaillé avec le score de chaque critère et les recommandations
d'amélioration. Vous pouvez soumettre votre projet à un nouvel audit après avoir implémenté les
améliorations. Il n'y a pas de limite au nombre de tentatives — l'objectif est la progression, pas la sanction.

### Le référentiel va-t-il évoluer ?

Oui. Le domaine de l'IA évolue rapidement et notre référentiel doit refléter les meilleures pratiques
du moment. Nous prévoyons des mises à jour semestrielles intégrant les retours des auditeurs, les
nouvelles menaces de sécurité et les évolutions des pratiques d'orchestration IA. Chaque version
est numérotée et les changements sont documentés.

## Prêt à être audité ?

Faites évaluer votre code selon ce référentiel ou passez l'examen individuel pour valider vos compétences.

Créer mon compte
Découvrir OrkestrAI
