# Référentiel OrkestrAI

Le référentiel OrkestrAI est un cadre d'évaluation complet, conçu pour mesurer la qualité
du code produit en collaboration avec des outils d'intelligence artificielle. Il ne s'agit pas
de juger si l'IA a été utilisée — **mais de vérifier que son utilisation a été maîtrisée,
encadrée et guidée par un développeur compétent**.

Chaque critère a été pensé par des développeurs seniors confrontés quotidiennement aux réalités
du code généré par IA. Notre ambition : créer un langage commun pour évaluer la qualité du
code à l'ère de l'IA, qu'il s'agisse d'un audit de projet, d'une revue de code ou d'un
processus de certification.

Le référentiel est organisé en **5 axes** allant de l'architecture logicielle
à la sécurité, en passant par l'orchestration IA elle-même. Les exigences sont réparties
selon l'impact de chaque domaine sur la qualité globale du code. L'architecture pèse le plus
lourd car c'est la fondation sur laquelle tout le reste repose.

## Axe 1 — Architecture

L'humain a guidé l'IA vers une architecture propre, maintenable et évolutive.

**Pourquoi cet axe ?** L'architecture est la fondation de tout projet logiciel. Un code bien architecturé
est un code que l'on peut faire évoluer, tester et maintenir sur le long terme. Or, c'est précisément là que l'IA
a le plus besoin d'être guidée : les modèles de langage génèrent du code qui fonctionne dans l'immédiat,
mais rarement du code qui s'inscrit dans une vision architecturale cohérente. La séparation des responsabilités,
l'inversion de dépendances, la modélisation du domaine — ces choix fondamentaux ne peuvent être délégués à l'IA.
Ils sont le territoire du développeur senior, de l'architecte, de l'orchestrateur.

### 1.1 Séparation des responsabilités

Ce critère évalue si le code est organisé en couches distinctes avec des responsabilités claires.
Une bonne séparation des responsabilités est le premier signe qu'un humain a guidé l'architecture
plutôt que de laisser l'IA empiler du code dans un seul fichier. **C'est le critère le plus
lourd du référentiel** car il conditionne la maintenabilité de tout le projet.

**Exemple concret :** Dans un projet Symfony, le score maximum implique que le dossier `src/`
est organisé par bounded context, chaque contexte ayant ses sous-dossiers Domain/, Application/ et Infrastructure/.
Les entités du domaine ne contiennent aucune annotation framework. Les repositories sont définis comme interfaces
dans le domaine et implémentés dans l'infrastructure.

**Comment auditer :** Vérifier l'arborescence des fichiers. Analyser les imports : est-ce que le domaine importe de l'infrastructure ? Tracer le sens des dépendances avec un outil d'analyse statique. Vérifier que les controllers ne contiennent pas de logique métier.

### 1.2 Gestion des dépendances

Ce critère mesure le niveau de couplage entre les composants du système. L'injection de dépendances
et l'inversion de contrôle sont des piliers de la Clean Architecture. Un code fortement couplé est
un code qui ne peut pas être testé en isolation — et c'est souvent le résultat d'une génération IA
non supervisée, où chaque classe instancie directement ses dépendances.

**Exemple concret :** Le domaine définit `ProjectRepositoryInterface`. L'infrastructure fournit
`DoctrineProjectRepository`. Le binding est fait dans la configuration du conteneur de services.
Aucun `new` dans le domaine pour des services ou repositories.

**Comment auditer :** Chercher les `new` dans le domaine. Vérifier les constructeurs des services. S'assurer que toutes les dépendances externes (BDD, API, filesystem) sont derrière des interfaces.

### 1.3 Modélisation du domaine

La qualité de la modélisation du domaine révèle si le développeur a réfléchi au métier avant de coder.
L'IA a tendance à produire des modèles anémiques (de simples conteneurs de données avec des getters/setters)
car c'est le pattern le plus fréquent dans ses données d'entraînement. Un domaine riche, avec des entités
qui portent leur logique métier, des Value Objects immuables et des agrégats bien définis, est la marque
d'un développeur qui a orchestré l'IA avec une vision DDD.

**Exemple concret :** Une entité `Audit` expose des méthodes comme `score()`,
`complete()`, `certificationLevel()` — la logique métier vit dans le domaine. Les identifiants
sont des Value Objects typés (`AuditId`, `ProjectId`) et non de simples chaînes de caractères.
Le langage du code reflète le langage du métier.

**Comment auditer :** Lire les entités du domaine. Vérifier qu'elles portent de la logique métier et pas seulement des getters. Chercher des Value Objects. Vérifier que le nommage utilise le vocabulaire métier (ubiquitous language).

### 1.4 Testabilité

Un code bien architecturé est un code testable. Ce critère évalue non seulement la présence de tests,
mais surtout la capacité du code à être testé en isolation. L'IA génère souvent du code difficile à
tester car fortement couplé à l'infrastructure (base de données, APIs externes). Un bon orchestrateur
structure le code pour que le domaine puisse être testé sans aucune dépendance externe.

**Exemple concret :** Les tests du domaine ne nécessitent aucune base de données. Un `ExamSessionTest`
vérifie les règles métier (score, expiration, niveaux) en instanciant directement les entités.
Les tests d'intégration vérifient que les repositories Doctrine fonctionnent. Les tests e2e vérifient les parcours utilisateur.

**Comment auditer :** Ratio de tests. Couverture du domaine. Présence de mocks/stubs pour l'infra. Temps d'exécution de la suite de tests. Vérifier que les tests du domaine passent sans base de données.

### 1.5 Modularité et couplage

Ce critère évalue si le projet est découpé en modules autonomes avec des frontières claires.
L'IA ne connaît pas les bounded contexts de votre projet — c'est au développeur de les définir
et de s'assurer que chaque module a une responsabilité claire, des contrats bien définis avec
les autres modules, et un minimum de dépendances croisées.

**Exemple concret :** Un projet organisé en bounded contexts (Audit, Certification, Exam, Security)
où chaque contexte a ses propres entités, repositories et controllers. Les communications entre contextes
passent par des interfaces partagées dans un Shared Kernel, jamais par des imports directs.

**Comment auditer :** Graphe de dépendances entre modules. Vérifier les imports croisés. S'assurer qu'aucun module n'accède directement aux détails internes d'un autre. Utiliser des outils comme deptrac ou phparkitect.

## Axe 2 — Orchestration IA

L'humain a piloté l'IA avec méthode, traçabilité et validation.

**Pourquoi cet axe ?** C'est l'axe qui distingue OrkestrAI de tout autre référentiel de qualité logicielle.
Il évalue non pas le code en lui-même, mais la manière dont l'humain et l'IA ont collaboré pour le produire.
La traçabilité des générations, la validation humaine systématique, la reproductibilité des résultats et la maîtrise
du périmètre confié à l'IA — ce sont les marqueurs d'une utilisation professionnelle et responsable de l'intelligence
artificielle dans le développement. Un projet peut avoir un code parfait ; s'il a été généré sans supervision,
c'est un château de cartes.

### 2.1 Traçabilité des générations

Peut-on identifier, pour chaque portion du code, si elle a été générée par IA ou écrite manuellement ?
La traçabilité est essentielle pour la maintenance : quand un bug survient dans du code généré, il faut
pouvoir retrouver le contexte de génération, le prompt utilisé, et les décisions prises. Sans traçabilité,
le code IA devient une boîte noire impossible à débugger efficacement.

**Exemple concret :** Les commits de code généré par IA incluent un tag `[AI]` dans le message
et un `Co-Authored-By`. Un dossier `.prompts/` à la racine du projet contient les prompts versionnés
utilisés pour chaque fonctionnalité majeure. Un fichier CLAUDE.md ou un ADR documente les sessions d'orchestration.

**Comment auditer :** Analyser le git log. Chercher des conventions de commit IA. Vérifier l'existence d'un dossier de prompts. Demander à l'équipe de retracer l'origine d'un fichier précis.

### 2.2 Validation humaine

Le code généré par IA a-t-il été systématiquement relu, compris et validé par un humain avant d'être intégré ?
La validation humaine est la clé de voûte de l'orchestration. Un développeur qui copie-colle du code IA sans
le comprendre n'orchestre pas — il délègue aveuglément. La validation implique la compréhension, la revue
critique et l'approbation explicite de chaque génération.

**Exemple concret :** Chaque PR contient une section "Code généré par IA" avec les fichiers concernés.
Une checklist de validation (tests passent, conventions respectées, pas de faille de sécurité, logique métier correcte)
est cochée avant le merge. Les PR sans validation sont bloquées par les règles du repository.

**Comment auditer :** Consulter les PRs et l'historique de review. Vérifier l'existence d'une checklist de validation. Demander à l'équipe de décrire leur processus de validation du code IA.

### 2.3 Reproductibilité

Les résultats de l'IA sont-ils reproductibles ? Si on relançait le même prompt dans le même contexte,
obtiendrait-on un résultat similaire ? La reproductibilité passe par la documentation des prompts,
du contexte fourni à l'IA, et des paramètres utilisés. C'est un principe fondamental de la démarche
scientifique appliqué à l'ingénierie logicielle assistée par IA.

**Exemple concret :** Un dossier `.prompts/` contient des templates de prompts organisés par domaine
(architecture, tests, refactoring). Chaque template inclut le contexte à fournir, les contraintes à préciser,
et un exemple de résultat attendu. Les prompts sont versionnés avec le code.

**Comment auditer :** Chercher un dossier de prompts. Vérifier si les templates sont versionnés. Demander à l'équipe de reproduire une génération passée. Évaluer la documentation du workflow IA.

### 2.4 Maîtrise du périmètre IA

Le développeur a-t-il clairement défini ce que l'IA peut et ne peut pas faire dans le projet ?
Un bon orchestrateur sait quand utiliser l'IA et quand s'en passer. Il définit les contraintes,
les conventions et les limites avant de lancer une génération. Les décisions architecturales,
la modélisation du domaine et les choix de sécurité ne doivent jamais être délégués à l'IA sans supervision.

**Exemple concret :** Un fichier `CLAUDE.md` à la racine du projet définit les conventions architecturales,
les patterns à utiliser, les règles de nommage et les interdictions (pas d'annotations dans le domaine,
pas d'instanciation directe, tests obligatoires). L'IA est guidée par ces contraintes à chaque génération.

**Comment auditer :** Vérifier l'existence de documents de cadrage (ADR, CLAUDE.md, conventions). Demander à l'équipe quel était le périmètre confié à l'IA. Comparer la documentation des décisions avec le code livré.

## Axe 3 — Qualité du code

Le code livré est lisible, cohérent et maintenable — quel que soit qui l'a écrit.

**Pourquoi cet axe ?** La qualité du code est ce que l'on voit quand on ouvre un fichier source.
C'est le critère le plus tangible et le plus immédiat. Un code de qualité se lit comme un texte bien écrit :
chaque variable a un nom intentionnel, chaque fonction fait une seule chose, chaque fichier respecte les mêmes
conventions. L'IA peut générer du code de qualité variable — c'est au développeur de s'assurer que le résultat
final est homogène, lisible et maintenable. La qualité du code est aussi le meilleur indicateur de la santé
à long terme d'un projet : un code propre aujourd'hui, c'est un code qui sera encore compréhensible dans 6 mois.

### 3.1 Lisibilité et nommage

Le code est-il auto-documenté ? Les noms de variables, fonctions et classes expriment-ils clairement
leur intention ? L'IA génère souvent des noms génériques (`data`, `result`, `temp`)
ou reproduit des conventions d'exemples de documentation. Un bon orchestrateur impose un nommage intentionnel
et cohérent qui reflète le langage du domaine métier.

**Exemple concret :** Au lieu de `$data = $repo->get($id)`, le code dit
`$project = $this->projectRepository->findById(ProjectId::fromString($id))`.
Chaque nom révèle l'intention. Les méthodes sont nommées selon les actions métier : `complete()`,
`certificationLevel()`, pas `process()` ou `handle()`.

**Comment auditer :** Lecture du code source. Vérifier la cohérence des conventions de nommage. Rechercher des patterns de nommage générique. Vérifier que le vocabulaire du code correspond au vocabulaire métier.

### 3.2 Complexité

Les fonctions sont-elles courtes et focalisées ? La complexité cyclomatique est-elle maîtrisée ?
L'IA a tendance à générer des fonctions longues avec de nombreux branchements conditionnels, car elle
optimise pour la complétude plutôt que pour la simplicité. Un orchestrateur veille à ce que chaque
fonction opère à un seul niveau d'abstraction et reste compréhensible d'un seul regard.

**Exemple concret :** Aucune méthode ne dépasse 20 lignes. Les conditions complexes sont extraites
dans des méthodes nommées (`isEligibleForCertification()` plutôt qu'une longue chaîne de if/else).
PHPStan/Psalm au niveau maximum ne remonte aucune alerte de complexité.

**Comment auditer :** Utiliser des outils d'analyse statique (phpstan, phpmd, sonar). Mesurer la complexité cyclomatique. Identifier les fonctions les plus longues. Vérifier les niveaux d'imbrication.

### 3.3 Cohérence des patterns

Le même problème est-il résolu de la même manière partout dans le projet ? L'un des défauts les plus
fréquents du code généré par IA est l'incohérence stylistique : chaque génération peut proposer un
pattern différent pour le même type de problème. Un bon orchestrateur impose des conventions et vérifie
que l'IA les respecte systématiquement.

**Exemple concret :** Tous les controllers suivent le même pattern (injection de dépendances, validation, délégation au domaine, réponse).
Tous les repositories implémentent la même interface. Tous les Value Objects suivent le même template (constructeur privé, factory method, equals).
Un fichier `.php-cs-fixer.php` ou `phpcs.xml` normalise le formatage.

**Comment auditer :** Comparer des fichiers similaires (deux controllers, deux repositories). Vérifier la config du linter/formatter. S'assurer que le CI/CD vérifie le style.

### 3.4 Dette technique maîtrisée

Le projet contient-il du code mort, des TODO orphelins, des dépendances inutilisées ou des hacks
non documentés ? L'IA génère parfois du code "au cas où" — des classes jamais utilisées, des méthodes
redondantes, des imports superflus. Un projet bien orchestré est un projet propre où chaque ligne
de code a une raison d'exister et où chaque compromis technique est documenté.

**Exemple concret :** `grep -r "TODO\|FIXME\|HACK" src/` ne retourne aucun résultat.
`composer why` justifie chaque dépendance. Les rares compromis techniques sont documentés
dans des ADR (Architecture Decision Records) avec la raison du choix et les alternatives envisagées.

**Comment auditer :** Rechercher TODO/FIXME/HACK dans le code. Analyser les dépendances inutilisées. Identifier le code mort avec des outils d'analyse statique. Vérifier la présence d'ADR pour les compromis.

## Axe 4 — Performance et sobriété

Le code est efficace et ne gaspille pas de ressources.

**Pourquoi cet axe ?** La performance et la sobriété sont des enjeux de plus en plus critiques
dans le développement logiciel. L'IA ne se soucie pas des performances — elle génère le code le plus "probable",
pas le plus efficace. Les problèmes classiques du code généré par IA incluent les requêtes N+1, les dépendances
superflues, le chargement excessif de données et l'absence totale de considérations Green IT. Un orchestrateur
responsable veille à ce que le code soit non seulement fonctionnel et bien structuré, mais aussi sobre et
respectueux des ressources. L'éco-conception n'est pas un luxe — c'est une responsabilité.

### 4.1 Efficacité des requêtes

Les accès aux données sont-ils optimisés ? L'un des pièges les plus fréquents du code IA est le
problème N+1 : une boucle qui exécute une requête par itération au lieu d'un seul chargement.
L'IA ne "voit" pas les implications de performance de ses choix — c'est au développeur de vérifier
que les requêtes sont efficaces, indexées et paginées.

**Exemple concret :** Le Symfony Profiler montre un nombre de requêtes raisonnable par page (< 10).
Les collections sont chargées avec des JOIN quand nécessaire. Les listes sont paginées.
Les colonnes fréquemment requêtées sont indexées. Pas de `findAll()` suivi d'un filtre PHP.

**Comment auditer :** Utiliser le profiler Symfony (debug toolbar). Compter les requêtes par page. Vérifier les index en base. Tester avec un volume de données significatif.

### 4.2 Sobriété des dépendances

Chaque dépendance du projet est-elle justifiée ? L'IA suggère fréquemment des bibliothèques tierces
pour des tâches simples, créant un bloat de dépendances qui alourdit le projet, augmente la surface
d'attaque et complique les mises à jour. Un bon orchestrateur challenge chaque `composer require`
et préfère le code natif quand c'est raisonnable.

**Exemple concret :** Le `composer.json` ne contient que les dépendances strictement nécessaires.
Pas de bibliothèque de 10 000 lignes pour une seule fonction utilitaire. `composer audit` ne remonte
aucune vulnérabilité. Les versions sont contraintes avec soin (pas de `*`).

**Comment auditer :** Analyser le composer.json / package.json. Exécuter `composer audit`. Vérifier que chaque dépendance est effectivement utilisée. Comparer le ratio code métier / dépendances.

### 4.3 Gestion des ressources

Le code gère-t-il correctement les ressources système ? Fichiers, connexions, mémoire — chaque ressource
ouverte doit être fermée, chaque allocation doit être libérée. L'IA oublie souvent ces détails car ils
ne sont pas nécessaires pour que le code "fonctionne" en apparence, mais ils causent des problèmes
insidieux en production.

**Exemple concret :** Les streams sont fermés dans des blocs finally. Le lazy loading est utilisé
pour les collections volumineuses. Un système de cache (HTTP, applicatif) est en place pour les données
rarement modifiées. La mémoire est monitorée dans le profiler.

**Comment auditer :** Profiling mémoire. Revue des streams et connexions. Vérifier les stratégies de cache. Tester avec un volume de données réaliste.

### 4.4 Éco-conception

Le projet intègre-t-il des considérations de sobriété numérique ? À l'heure où le numérique représente
4 % des émissions mondiales de gaz à effet de serre, l'éco-conception est un devoir professionnel.
Un orchestrateur responsable minimise l'empreinte de son code : payloads légers, compression, cache HTTP,
images optimisées, pas de requêtes superflues.

**Exemple concret :** Le CSS est inline et minifié (pas de framework CSS de 300 Ko pour un simple site).
Les images sont au format WebP avec lazy loading. Les headers de cache sont configurés.
Lighthouse affiche un score performance > 90. Le poids total d'une page est inférieur à 500 Ko.

**Comment auditer :** Mesurer le poids des pages avec les DevTools. Compter le nombre de requêtes. Vérifier les headers de cache. Lancer un audit Lighthouse. Évaluer la stratégie globale de sobriété.

## Axe 5 — Sécurité

Le code ne présente pas de vulnérabilités connues et protège les données des utilisateurs.

**Pourquoi cet axe ?** La sécurité est un sujet transversal qui irrigue tous les autres axes.
Si elle dispose d'une section dédiée, c'est parce que de nombreux critères des autres axes contribuent
indirectement à la sécurité (architecture propre, tests, validation humaine). Néanmoins, ces exigences
sont critiques : une seule faille de sécurité peut compromettre tout un projet. L'IA est particulièrement
dangereuse dans ce domaine car elle reproduit les patterns vulnérables avec une confiance apparente qui
peut tromper un développeur non vigilant. Injections SQL, XSS, secrets en clair, absence de validation
— ce sont des erreurs que l'IA commet régulièrement et que l'orchestrateur doit systématiquement vérifier.

### 5.1 OWASP Top 10

Le code est-il protégé contre les 10 vulnérabilités web les plus courantes ? Le référentiel OWASP
est le standard mondial en matière de sécurité applicative. Les failles les plus fréquentes dans
le code généré par IA sont les injections (SQL, XSS, commandes), l'absence de CSRF, la mauvaise
gestion de l'authentification et l'exposition de données sensibles dans les réponses API.

**Exemple concret :** Toutes les requêtes passent par l'ORM (pas de SQL brut). Les templates Twig
échappent automatiquement les variables. Les formulaires incluent un token CSRF. Les headers de sécurité
(X-Content-Type-Options, X-Frame-Options, CSP) sont configurés. Les entrées utilisateur sont validées
côté serveur, jamais seulement côté client.

**Comment auditer :** Lancer un scan de sécurité (OWASP ZAP, Snyk). Revue manuelle des entrées utilisateur. Vérifier les headers HTTP de sécurité. Tester les formulaires sans JavaScript pour valider la protection côté serveur.

### 5.2 Gestion des secrets

Les secrets (mots de passe, clés API, tokens) sont-ils correctement externalisés et protégés ?
C'est l'une des erreurs les plus graves et les plus fréquentes dans le code généré par IA : des
credentials en dur dans le code source, un fichier `.env` commité par mégarde, des clés API
visibles dans l'historique git. Un seul secret fuité peut compromettre tout un système.

**Exemple concret :** Le fichier `.env` est dans le `.gitignore`. Un `.env.example`
documente toutes les variables nécessaires avec des valeurs placeholder. `git log -S "password"` ne
retourne aucun résultat suspect. Les secrets de production sont gérés par un vault ou des variables
d'environnement du serveur, jamais dans le code.

**Comment auditer :** Rechercher des patterns de secrets dans le code (grep sur password, secret, api\_key, token). Vérifier le .gitignore. Scanner l'historique git avec un outil comme truffleHog ou gitleaks. Vérifier le .env.example.

### 5.3 Protection des données

Les données personnelles des utilisateurs sont-elles protégées conformément au RGPD et aux bonnes
pratiques de sécurité ? L'IA n'a aucune notion de conformité réglementaire. Elle peut logger des données
personnelles, stocker des mots de passe en clair, ou exposer des informations sensibles dans les réponses
API. C'est au développeur de s'assurer que chaque donnée personnelle est traitée avec le respect qu'elle mérite.

**Exemple concret :** Les mots de passe sont hashés avec bcrypt/argon2 (jamais en clair ni en MD5).
Les logs ne contiennent ni emails, ni noms, ni adresses IP identifiantes. Un endpoint de suppression de compte
existe et anonymise toutes les données de l'utilisateur. La politique de cookies est conforme au RGPD.

**Comment auditer :** Audit RGPD complet. Revue des logs en production (chercher des PII). Vérifier le hashing des mots de passe. Tester la suppression de compte. Vérifier la politique de cookies et le consentement.

## 6. Orchestration opérationnelle avancée

Cette section complète les axes 1 à 5 avec des pratiques d'exécution terrain.
Elle vise à rendre le référentiel directement utilisable pour préparer l'examen,
structurer un projet réel et standardiser les habitudes d'équipe.

### 6.1 Git, branches et worktrees

L'usage de l'IA impose une isolation stricte des travaux. Un même développeur peut
avoir plusieurs tâches en parallèle: feature, correctif urgent, revue de sécurité.
Sans isolation, le contexte se pollue et les diffs deviennent non auditables.

**Pratique recommandée:**

- une branche par objectif métier;
- un worktree par tâche active (`feature/*`, `fix/*`, `hotfix/*`);
- aucun mélange de sujets dans un même diff;
- suppression du worktree quand la tâche est terminée.

**Exemple concret:**

```bash
git fetch origin
git worktree add ../wt-score-endpoint -b feature/score-endpoint origin/main
cd ../wt-score-endpoint
```

Chaque session IA se fait dans le worktree dédié, avec un périmètre de fichiers
explicitement borné.

**Comment auditer:**

- vérifier la granularité des branches et commits;
- confirmer qu'un patch = un objectif;
- contrôler la lisibilité des PR (pas de bruit hors périmètre);
- vérifier qu'aucune feature non terminée n'est restée cachée dans un worktree local.

### 6.2 Hygiène de commit et traçabilité de décision

Le code généré par IA devient inmaintenable sans historique de décision.
Un commit doit expliquer "pourquoi", pas seulement "quoi".

**Pratique recommandée:**

- commits atomiques;
- message conventionnel (type/scope);
- référence du contexte de génération (prompt, ticket, ADR);
- mention explicite des hypothèses prises.

**Comment auditer:**

- lire les 20 derniers commits d'une zone active;
- mesurer la proportion de commits "WIP"/opaques;
- vérifier la présence de liens vers docs, tickets ou décisions.

### 6.3 Gestion du contexte IA

Une bonne réponse IA dépend d'un bon contexte. Le contexte doit être stable,
versionné, et aligné avec l'architecture cible.

**Fichiers de cadrage minimum:**

- `AGENTS.md` (règles d'interaction et interdits);
- `docs/architecture.md` (frontières techniques);
- `docs/decisions.md` (historique d'arbitrages);
- `docs/backlog.md` (tâches courtes et vérifiables);
- `docs/prompts/` (templates réutilisables).

**Signaux de maturité:**

- les prompts sont orientés objectifs et critères d'acceptation;
- l'IA est contrainte sur les fichiers modifiables;
- les sorties incluent systématiquement risques et tests.

### 6.4 Catalogue de prompts et reproductibilité

Le référentiel n'impose pas un outil, mais impose une méthode reproductible.
Un catalogue de prompts permet de rejouer une génération et d'en comparer la qualité.

**Template recommandé (par prompt):**

1. Contexte
2. Objectif
3. Contraintes
4. Fichiers autorisés
5. Critères d'acceptation
6. Risques connus
7. Commandes de vérification

**Comment auditer:**

- vérifier que des templates existent et sont versionnés;
- exécuter un replay sur une tâche similaire;
- comparer lisibilité, robustesse et conformité aux conventions.

### 6.5 Revue de diff orientée risques

La revue d'un patch IA doit d'abord traiter les risques, avant le style.

**Ordre de revue recommandé:**

1. Bugs fonctionnels
2. Régressions potentielles
3. Sécurité
4. Couplage architecture
5. Dette technique
6. Lisibilité/style

**Comment auditer:**

- vérifier la qualité des commentaires de review;
- confirmer que les blocages critiques sont traités avant merge;
- vérifier que les décisions de compromis sont documentées.

## 7. Delivery, CI/CD et exploitation

### 7.1 Quality gates CI/CD

Un projet IA-assisted ne doit jamais reposer uniquement sur une validation locale.
Les contrôles essentiels doivent être automatisés.

**Gates minimales:**

- lint/format;
- tests unitaires et intégration;
- analyse statique;
- scan sécurité dépendances;
- build reproductible.

**Comment auditer:**

- ouvrir la configuration CI;
- vérifier qu'un échec bloque le merge;
- confirmer l'absence de contournement permanent des checks.

### 7.2 Migrations et sécurité des données

Les migrations générées avec aide IA sont un risque majeur si elles ne sont pas
testées sur données réalistes.

**Pratique recommandée:**

- migration idempotente ou strictement contrôlée;
- rollback planifié;
- sauvegarde avant opération destructive;
- script de vérification post-migration.

**Comment auditer:**

- relire les migrations récentes;
- vérifier la présence de tests d'intégration DB;
- vérifier la stratégie de rollback et de reprise.

### 7.3 Observabilité et diagnostic

Un système bien orchestré est observable. Sans logs structurés, métriques et traces,
les défauts IA-induced deviennent difficiles à expliquer.

**Minimum attendu:**

- logs structurés avec corrélation;
- métriques techniques (latence, erreurs, saturation);
- alertes sur seuils critiques;
- runbooks d'incident.

**Comment auditer:**

- contrôler le contenu des logs;
- simuler une erreur et vérifier le diagnostic;
- vérifier la présence d'un runbook actionnable.

### 7.4 Budgets de performance et sobriété

Les exigences de performance doivent être explicites. "Ça fonctionne" n'est pas
un critère suffisant pour un projet certifiable.

**Exemples de budgets:**

- temps de réponse p95;
- poids maximal d'une page;
- nombre maximal de requêtes DB par écran;
- budget mémoire sur flux critiques.

## 8. Sécurité avancée et conformité

### 8.1 Supply chain logicielle

Le risque ne vient pas seulement du code produit, mais des dépendances.

**Attendus:**

- dépendances minimales et justifiées;
- versions verrouillées;
- surveillance des CVE;
- procédure de mise à jour sécurisée.

### 8.2 Gouvernance des secrets

Au-delà du stockage, il faut traiter rotation, révocation et exposition historique.

**Attendus:**

- rotation périodique;
- procédure de révocation d'urgence;
- scan historique Git;
- séparation nette des environnements.

### 8.3 Protection des données par conception

La conformité RGPD doit être pensée dès la conception:

- minimisation des données collectées;
- durée de conservation explicite;
- droit à l'effacement effectif;
- journalisation sans données identifiantes.

### 8.4 Gouvernance des données envoyées aux IA

Les prompts peuvent devenir un vecteur de fuite de données sensibles.

**Attendus:**

- politique claire sur les données autorisées en prompt;
- anonymisation/pseudonymisation si nécessaire;
- interdiction d'envoyer secrets/PII sans base légale;
- revue régulière des pratiques.

## 9. Guide de préparation à l'examen

### 9.1 Dossier de preuves recommandé

Préparer un dossier de preuves augmente fortement la réussite:

- architecture documentée;
- extrait de backlog et décisions;
- exemples de prompts et validations humaines;
- résultats de tests et quality gates;
- exemples de revues de code et corrections.

### 9.2 Plan d'entraînement (4 semaines)

### Semaine 1

- setup du cadre projet;
- standards de commits/PR;
- micro-feature avec tests.

### Semaine 2

- travail sur séparation des responsabilités;
- revue de code guidée risques;
- amélioration de la testabilité.

### Semaine 3

- performance/sobriété;
- sécurité OWASP + secrets;
- simulation incident + diagnostic.

### Semaine 4

- audit blanc complet;
- correction des écarts;
- préparation de l'argumentaire technique.

### 9.3 Erreurs fréquentes en situation d'examen

- réponses théoriques non reliées au code réel;
- confusion entre "code qui marche" et "code maintenable";
- absence de traçabilité des usages IA;
- sous-estimation de la sécurité et des cas limites;
- manque de preuves concrètes (tests, logs, décisions).

### 9.4 Checklist finale candidat

- je peux expliquer mes choix d'architecture;
- je peux justifier comment j'ai cadré l'IA;
- je peux démontrer la stratégie de test;
- je peux prouver la gestion des risques sécurité;
- je peux montrer des preuves de reproductibilité.

## 10. Annexes pratiques

### 10.1 Checklist PR IA-assisted

- objectif métier explicite;
- périmètre de fichiers respecté;
- tests adaptés ajoutés/mis à jour;
- risques identifiés et documentés;
- validation humaine explicite avant merge.

### 10.2 Checklist "revue sécurité rapide"

- validation entrées/sorties;
- auth/authz cohérentes;
- pas de secret en clair;
- erreurs non verbeuses pour l'utilisateur final;
- dépendances sans vulnérabilités critiques connues.

### 10.3 Checklist "go/no-go release"

- quality gates verts;
- migration validée;
- rollback prêt;
- observabilité opérationnelle;
- incident runbook à jour.

## 11. Modèle opératoire d'orchestration agentique

Cette section formalise le mode de fonctionnement cible d'une équipe qui
utilise des agents de codage IA en environnement professionnel.

### 11.1 Chaîne de valeur de l'orchestration

Cycle recommandé sur chaque changement:

1. **Intent**: clarifier le besoin métier et les non-objectifs.
2. **Plan**: découper en micro-lots testables et ordonnés.
3. **Execute**: produire un patch minimal par lot.
4. **Verify**: exécuter quality gates et revue orientée risques.
5. **Decide**: accepter, rejeter ou re-travailler le lot.
6. **Trace**: archiver contexte, prompts et décisions.

Un lot n'est terminé que si les preuves sont disponibles:

- diff lisible;
- tests adaptés;
- risques explicités;
- décision humaine signée dans la PR.

### 11.2 Rôles minimaux et responsabilités

Rôles recommandés pour un niveau "architecte-compatible":

- **Architecte**: cadre les invariants, frontières, dépendances autorisées.
- **Lead dev**: pilote le séquencement, arbitre les compromis.
- **Développeur**: orchestre l'agent sur un périmètre strict.
- **Reviewer**: valide robustesse, lisibilité, non-régression.
- **Security champion**: vérifie menaces et conformité.

Matrice RACI simplifiée:

| Activité | Architecte | Lead | Dev | Reviewer | Sec |
| --- | --- | --- | --- | --- | --- |
| Définir architecture cible | R | A | C | I | C |
| Cadrer prompts et contexte | C | A | R | I | C |
| Implémenter lot | I | C | R | I | I |
| Revue de risque | C | A | C | R | R |
| Décision de merge | C | A | C | R | C |

### 11.3 Contrat d'entrée/sortie d'une tâche agentique

**Entrée minimale**:

- intention métier (1 paragraphe);
- critères d'acceptation vérifiables;
- fichiers autorisés;
- contraintes architecture/sécurité;
- commandes de validation.

**Sortie minimale**:

- plan d'implémentation;
- patch;
- tests et résultats;
- risques résiduels;
- proposition de message de commit.

### 11.4 Critères de maturité équipe

Niveau "bronze":

- usage ponctuel de l'IA avec validation manuelle;
- conventions partielles et hétérogènes.

Niveau "argent":

- protocole stable par type de tâche;
- qualité mesurée (tests, lint, static analysis);
- traçabilité des décisions majeure.

Niveau "or":

- orchestration industrialisée;
- gouvernance outillée des prompts et contextes;
- amélioration continue par postmortem et métriques.

## 12. Protocoles de prompts et exemples concrets

### 12.1 Prompt de diagnostic architecture

```text
Objectif: identifier les zones de couplage fort dans [module].
Contexte: architecture visée [hexagonale / clean / layered].
Contraintes: ne pas proposer de refonte globale.
Sortie: top 5 risques, impacts, ordre de traitement.
```

Usage attendu:

- phase de découverte;
- création d'un backlog de remédiation;
- priorisation avant implémentation.

### 12.2 Prompt d'implémentation contrôlée

```text
Objectif: implémenter [feature] dans les fichiers autorisés.
Contraintes:
- aucun changement hors périmètre;
- pas de nouvelle dépendance;
- tests requis.
Sortie:
1) plan court
2) patch
3) tests
4) risques
```

Contrôles humains obligatoires:

- cohérence avec le modèle métier;
- conformité aux conventions de nommage;
- vérification explicite des cas limites.

### 12.3 Prompt de refactor sans régression

```text
Objectif: réduire la complexité de [fichier/méthode].
Interdits: modifier le comportement fonctionnel.
Sortie: proposition en 3 étapes + tests de non-régression.
```

Exemple concret de preuve attendue:

- complexité cyclomatique avant/après;
- taille de méthode avant/après;
- couverture tests inchangée ou améliorée.

### 12.4 Prompt de revue sécurité ciblée

```text
Contexte: endpoint [X] avec entrée utilisateur.
Mission: analyser risques OWASP et proposer correctifs.
Sortie: vulnérabilités probables + patchs minimaux + tests.
```

Attendu opérationnel:

- validation côté serveur;
- messages d'erreur non verbeux;
- vérification auth/authz;
- absence de fuite de secrets.

### 12.5 Règles interdites pour prompts en production

- envoyer des secrets en clair;
- envoyer des PII sans base légale;
- demander des contournements sécurité;
- demander un "quick fix" sans tests.

## 13. Modèle de menace spécifique aux agents IA

### 13.1 Menaces principales

- **Prompt injection** via contenus non fiables.
- **Exfiltration** de données sensibles dans le contexte.
- **Tool abuse** (exécution d'actions hors périmètre).
- **Dependency poisoning** via suggestions non vérifiées.
- **Hallucination critique** en zone de sécurité.

### 13.2 Contrôles de réduction du risque

- séparation stricte des permissions d'outils;
- allowlist de commandes et chemins modifiables;
- sandbox pour exécution; élévation explicitement approuvée;
- redaction/anonymisation des données de prompt;
- double validation humaine pour sécurité et conformité.

### 13.3 Scénario d'attaque et réponse

Scénario:

1. un ticket inclut un extrait malveillant;
2. l'agent interprète cet extrait comme instruction;
3. le patch proposé ajoute une fuite de données.

Réponse attendue:

- détection en revue de diff orientée risques;
- rejet du patch;
- nettoyage du contexte;
- ajout d'un test de non-régression sécurité;
- note d'incident et mise à jour du guide de prompts.

### 13.4 Journal de contrôle recommandé

Pour chaque session agentique:

- identifiant de session;
- périmètre de fichiers;
- outils utilisés;
- risques signalés;
- décision finale et motif.

## 14. Pack de preuves pour audit avancé

### 14.1 Arborescence recommandée

```text
audit-evidence/
  01-architecture/
  02-orchestration-ia/
  03-quality-gates/
  04-security/
  05-performance/
  06-runbooks/
  07-postmortems/
```

### 14.2 Preuves minimales par axe

Architecture:

- schéma de dépendances;
- règles d'architecture codées (deptrac, etc.);
- ADR majeures.

Orchestration IA:

- templates de prompts;
- échantillon de sessions tracées;
- exemples de rejets justifiés.

Qualité:

- rapports tests;
- rapports static analysis;
- rapport dette technique.

Sécurité:

- scan dépendances;
- scan secret;
- preuve de correction d'au moins une faille.

Performance:

- baseline;
- budgets;
- mesures avant/après optimisation.

### 14.3 Défense orale orientée architecte

Questions attendues:

1. Quels invariants d'architecture sont non négociables?
2. Quelles tâches sont explicitement interdites à l'agent?
3. Où est la preuve de non-régression?
4. Quel est votre plan de rollback documenté?

## 15. Runbooks et procédures opérationnelles

### 15.1 Runbook "incident patch IA"

Déclencheur:

- régression en production liée à un patch agentique.

Procédure:

1. geler les merges;
2. isoler le commit fautif;
3. rollback selon procédure;
4. ouvrir postmortem;
5. renforcer règle de prompt ou contrôle manquant.

### 15.2 Runbook "secret exposé"

1. révoquer immédiatement la clé;
2. invalider sessions/tokens associés;
3. purger historique si applicable;
4. notifier les parties concernées;
5. documenter cause racine et action préventive.

### 15.3 Runbook "migration défaillante"

1. stop write;
2. diagnostic rapide;
3. rollback ou restauration validée;
4. vérification d'intégrité des données;
5. reprise progressive avec monitoring renforcé.

### 15.4 Postmortem standard

Sections minimales:

- timeline factuelle;
- impact;
- causes racines;
- barrières qui ont échoué;
- actions correctives;
- date de vérification d'efficacité.

## 16. Bibliographie normative et sources fiables

Les sources ci-dessous sont recommandées comme base commune d'exigence.
Elles doivent être citées dans les ADR, guides internes et formations.

### 16.1 Sécurité applicative et SDLC

- NIST Secure Software Development Framework (SP 800-218):
  [NIST SSDF](https://csrc.nist.gov/pubs/sp/800/218/final)
- OWASP Top 10 (2021):
  [OWASP Top 10](https://owasp.org/Top10/)
- OWASP ASVS:
  [OWASP ASVS](https://owasp.org/www-project-application-security-verification-standard/)
- OWASP Cheat Sheet Series:
  [OWASP Cheat Sheets](https://cheatsheetseries.owasp.org/)
- OWASP Top 10 for LLM Applications:
  [OWASP LLM Top 10](https://owasp.org/www-project-top-10-for-large-language-model-applications/)

### 16.2 Supply chain, provenance et intégrité

- SLSA Framework:
  [SLSA](https://slsa.dev/)
- in-toto framework:
  [in-toto](https://in-toto.io/)
- OpenSSF Scorecard:
  [OpenSSF Scorecard](https://securityscorecards.dev/)
- SPDX specification:
  [SPDX](https://spdx.dev/specifications/)
- CycloneDX SBOM:
  [CycloneDX](https://cyclonedx.org/specification/overview/)

### 16.3 Opérations, incident et observabilité

- NIST Computer Security Incident Handling Guide (SP 800-61r2):
  [NIST 800-61r2](https://csrc.nist.gov/pubs/sp/800/61/r2/final)
- OpenTelemetry specification:
  [OpenTelemetry Spec](https://opentelemetry.io/docs/specs/)

### 16.4 Git, traçabilité et standards de contribution

- Git worktree documentation:
  [git-worktree](https://git-scm.com/docs/git-worktree)
- Conventional Commits 1.0.0:
  [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/)
- Keep a Changelog:
  [Keep a Changelog](https://keepachangelog.com/en/1.1.0/)

### 16.5 Gouvernance IA et fiabilité

- NIST AI Risk Management Framework 1.0:
  [NIST AI RMF](https://www.nist.gov/itl/ai-risk-management-framework)
- ISO/IEC 42001 overview (AI management systems):
  [ISO 42001 Overview](https://www.iso.org/standard/81230.html)
- Model Spec (OpenAI):
  [OpenAI Model Spec](https://model-spec.openai.com/)

### 16.6 Références de méthode produit et test

- Twelve-Factor App:
  [12factor](https://12factor.net/)
- Martin Fowler, Test Pyramid:
  [Test Pyramid](https://martinfowler.com/articles/practical-test-pyramid.html)
- CISA Secure by Design:
  [Secure by Design](https://www.cisa.gov/securebydesign)
