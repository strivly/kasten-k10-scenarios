# kasten-k10-scenarios

# Scénarios pour le cours Kasten K10

## Maîtriser la sauvegarde et la restauration dans Kubernetes

- **Scenario 1:** Sauvegarder un déploiement stateless (ex: application web) et restaurer après suppression volontaire.
- **Scenario 2:** Sauvegarder un StatefulSet (ex: base de données PostgreSQL) avec PVC et restaurer après perte des données.
- **Scenario 3:** Restaurer un namespace complet incluant plusieurs workloads et services.

## Comprendre les concepts fondamentaux de la protection des données

- **Scenario 4:** Identifier les différences entre sauvegarde incrémentale, différentielle et complète.
- **Scenario 5:** Implémenter la rétention de sauvegardes selon les exigences de compliance (ex: 30 jours, 90 jours).
- **Scenario 6:** Simuler une corruption de données pour démontrer la nécessité d’une stratégie de sauvegarde fiable.

## Découvrir l’architecture et les composants de Kasten K10

- **Scenario 7:** Schématiser l’architecture Kasten K10 avec ses composants (Kanister, Aggregator, Executor).
- **Scenario 8:** Analyser les logs de Kasten K10 pour comprendre les interactions entre ses différents pods.
- **Scenario 9:** Inspecter la base de données interne de Kasten K10 pour comprendre le stockage des métadonnées.

## Déployer et configurer Kasten K10 dans un cluster Kubernetes via Helm

- **Scenario 10:** Installer Kasten K10 via Helm sur un cluster EKS avec des valeurs par défaut.
- **Scenario 11:** Personnaliser l’installation Helm pour utiliser un bucket S3 privé en tant que backend de sauvegarde.
- **Scenario 12:** Installer Kasten K10 avec l’intégration OIDC activée dès le départ.

## Mettre en place des politiques de sauvegarde automatisées

- **Scenario 13:** Créer une policy pour sauvegarder tous les StatefulSets toutes les 6 heures.
- **Scenario 14:** Définir des règles d’exclusion pour ne pas sauvegarder certains namespaces.
- **Scenario 15:** Associer une policy à des workloads critiques et configurer la rétention de 7 jours glissants.

## Sauvegarder et restaurer des workloads, notamment les StatefulSets

- **Scenario 16:** Sauvegarder un StatefulSet avec base MySQL et restaurer sur un namespace différent.
- **Scenario 17:** Restaurer un StatefulSet sans restaurer le PVC (migration sans données).
- **Scenario 18:** Restaurer une base de données sur un autre cluster en modifiant le nom du StatefulSet.

## Tester et valider les restaurations pour assurer la récupération des données en cas d'incident

- **Scenario 19:** Simuler la suppression accidentelle d’un namespace et valider la restauration complète.
- **Scenario 20:** Effectuer une restauration sélective d’un seul PVC d’une application multi-volumes.
- **Scenario 21:** Créer un script d’automatisation qui lance des restaurations régulières sur un cluster de test.

## Gérer la continuité des opérations et la reprise après sinistre (DR)

- **Scenario 22:** Planifier une procédure de basculement manuel vers un cluster secondaire.
- **Scenario 23:** Configurer la réplication automatique des sauvegardes vers une autre région AWS.
- **Scenario 24:** Simuler une panne de zone (AZ) et restaurer les workloads sur une AZ disponible.

## Comprendre les enjeux du Disaster Recovery et de la résilience des applications

- **Scenario 25:** Identifier les points de défaillance unique dans l’architecture Kubernetes actuelle.
- **Scenario 26:** Simuler une coupure réseau partielle et observer la capacité de résilience des workloads.
- **Scenario 27:** Calculer le RTO/RPO pour plusieurs types d’applications déployées.

## Automatiser le basculement et la restauration en cas de panne

- **Scenario 28:** Déployer un pipeline CI/CD qui déclenche automatiquement une restauration après détection de panne.
- **Scenario 29:** Utiliser les hooks Kanister pour automatiser la reconfiguration des applications après restauration.
- **Scenario 30:** Démontrer un basculement automatisé entre clusters via des scripts Kubernetes et Kasten APIs.

## Simuler un scénario de sinistre pour tester la robustesse des stratégies de protection

- **Scenario 31:** Supprimer tous les objets d’un namespace (Deployments, PVCs, Services) et mesurer le temps de restauration.
- **Scenario 32:** Injecter des erreurs dans les sauvegardes (corruption simulée) pour tester les alertes et la reprise.
- **Scenario 33:** Simuler un ransomware et restaurer les workloads à partir des dernières sauvegardes propres.

## Assurer la mobilité des workloads entre clusters

- **Scenario 34:** Sauvegarder une application complète sur le cluster A et la restaurer sur le cluster B dans une autre région.
- **Scenario 35:** Modifier les configurations de services (ex: LoadBalancer → ClusterIP) lors de la restauration sur un autre cluster.
- **Scenario 36:** Utiliser des Policies avec des transformations JSON pour adapter les manifests lors de la migration.

## Sécuriser l'accès et la gestion des permissions

- **Scenario 37:** Créer des rôles RBAC personnalisés pour limiter l’accès aux sauvegardes d’un namespace spécifique.
- **Scenario 38:** Implémenter l’authentification via un fournisseur OIDC (Keycloak) et restreindre l’accès aux policies critiques.
- **Scenario 39:** Auditer les actions des utilisateurs sur Kasten K10 grâce aux logs Kubernetes et aux logs K10.

## Déployer Kasten K10 en environnement restreint (air-gapped)

- **Scenario 40:** Télécharger les images Docker et packages Helm nécessaires pour un déploiement air-gapped.
- **Scenario 41:** Configurer un registre privé pour héberger les images Kasten K10.
- **Scenario 42:** Installer Kasten K10 dans un cluster sans accès Internet et configurer un backend NFS pour les sauvegardes.

## Sauvegarder les applications Kubernetes sur un stockage NFS

- **Scenario 43:** Déployer un serveur NFS et créer une Location Profile NFS sur Kasten K10.
- **Scenario 44:** Sauvegarder et restaurer un StatefulSet sur NFS en validant la consistance des données restaurées.
- **Scenario 45:** Configurer des quotas et des alertes sur le stockage NFS pour anticiper le dépassement de capacité.

## Superviser et observer les opérations avec Kasten K10

- **Scenario 46:** Exposer les métriques Prometheus de Kasten K10 dans un cluster de monitoring.
- **Scenario 47:** Créer des requêtes PromQL pour suivre l’état des backups et restaurations.
- **Scenario 48:** Configurer Grafana pour afficher des dashboards dédiés à Kasten K10 (statut des jobs, durée des backups).
- **Scenario 49:** Configurer des alertes Prometheus pour surveiller l’échec des jobs de sauvegarde/restauration.
- **Scenario 50:** Tester l’envoi d’alertes critiques par e-mail via un SMTP sécurisé.


# Scénarios + Exercices pour le cours Kasten K10

## Maîtriser la sauvegarde et la restauration dans Kubernetes

### Scenario 1: Sauvegarder un déploiement stateless (ex: application web) et restaurer après suppression volontaire.
**Exercice:**
1. Déployer une application NGINX via `kubectl apply`.
2. Créer une policy de sauvegarde Kasten K10 ciblant le namespace de l’application.
3. Lancer manuellement la sauvegarde.
4. Supprimer le déploiement et le namespace.
5. Restaurer à partir de la sauvegarde et valider l’accessibilité de l’application.

### Scenario 2: Sauvegarder un StatefulSet (ex: base de données PostgreSQL) avec PVC et restaurer après perte des données.
**Exercice:**
1. Déployer un StatefulSet PostgreSQL avec un PVC de 1Go.
2. Remplir la base avec quelques lignes de données.
3. Créer une sauvegarde Kasten K10 complète.
4. Supprimer le PVC et vérifier la perte des données.
5. Restaurer le PVC et le StatefulSet, puis vérifier l’intégrité des données.

### Scenario 3: Restaurer un namespace complet incluant plusieurs workloads et services.
**Exercice:**
1. Déployer un namespace contenant deux applications (NGINX + Redis).
2. Créer une sauvegarde complète du namespace avec Kasten.
3. Supprimer tout le namespace.
4. Restaurer le namespace complet via l’interface K10.
5. Vérifier le bon fonctionnement des deux applications restaurées.

---

## Comprendre les concepts fondamentaux de la protection des données

### Scenario 4: Identifier les différences entre sauvegarde incrémentale, différentielle et complète.
**Exercice:**
1. Configurer trois policies distinctes pour sauvegarder une même application :
    - une en full backup,
    - une en incrémentale,
    - une en différentielle.
2. Modifier l’application entre chaque sauvegarde (ex: ajouter des fichiers).
3. Comparer la taille et la durée des sauvegardes.
4. Documenter les avantages/inconvénients de chaque méthode.

### Scenario 5: Implémenter la rétention de sauvegardes selon les exigences de compliance.
**Exercice:**
1. Créer une policy de sauvegarde qui conserve les données pendant 30 jours.
2. Configurer une autre policy avec rétention de 7 jours.
3. Simuler l’expiration automatique des snapshots.
4. Vérifier que les snapshots expirés sont bien supprimés par Kasten.

### Scenario 6: Simuler une corruption de données pour démontrer la nécessité d’une stratégie de sauvegarde fiable.
**Exercice:**
1. Déployer une base MariaDB avec PVC.
2. Remplir la base avec des données critiques.
3. Sauvegarder la base.
4. Simuler une corruption (modifier ou supprimer des fichiers directement dans le PVC).
5. Restaurer depuis la sauvegarde et valider la récupération intégrale.

---

## Découvrir l’architecture et les composants de Kasten K10

### Scenario 7: Schématiser l’architecture Kasten K10 avec ses composants.
**Exercice:**
1. Lire la documentation officielle sur les composants Kasten.
2. Réaliser un diagramme sur Lucidchart ou Draw.io montrant :
    - Aggregator,
    - Executor,
    - Kanister,
    - Storage Profile,
    - Policies,
    - Backup Actions.

### Scenario 8: Analyser les logs de Kasten K10 pour comprendre les interactions entre ses pods.
**Exercice:**
1. Exécuter `kubectl logs` sur les pods `kanister` et `executor`.
2. Déclencher une sauvegarde manuelle d’un namespace.
3. Suivre les logs pour comprendre la communication entre les composants.
4. Rédiger un rapport d'observation.

### Scenario 9: Inspecter la base de données interne de Kasten K10 pour comprendre le stockage des métadonnées.
**Exercice:**
1. Exposer temporairement le pod `aggregator` en interne.
2. Explorer les données internes stockées via l’API REST de Kasten.
3. Lister les objets "backups", "policies" et "profiles" présents.

---

## Déployer et configurer Kasten K10 dans un cluster Kubernetes via Helm

### Scenario 10: Installer Kasten K10 via Helm sur un cluster EKS avec des valeurs par défaut.
**Exercice:**
1. Provisionner un cluster EKS.
2. Ajouter le repo Helm de Kasten.
3. Lancer `helm install k10 kasten/k10` avec les valeurs par défaut.
4. Vérifier le déploiement via `kubectl get pods -n kasten-io`.

### Scenario 11: Personnaliser l’installation Helm pour utiliser un bucket S3 privé.
**Exercice:**
1. Créer un bucket S3 et un user IAM avec accès restreint.
2. Configurer `values.yaml` pour y intégrer le backend S3.
3. Installer Kasten via Helm avec ces valeurs.
4. Effectuer une sauvegarde et vérifier l’écriture dans S3.

### Scenario 12: Installer Kasten K10 avec l’intégration OIDC activée.
**Exercice:**
1. Configurer un fournisseur OIDC (ex: Auth0 ou Keycloak).
2. Paramétrer `values.yaml` de Helm pour intégrer l’authentification OIDC.
3. Installer Kasten et valider l’accès via un utilisateur OIDC.

---

## Mettre en place des politiques de sauvegarde automatisées

### Scenario 13: Créer une policy pour sauvegarder tous les StatefulSets toutes les 6 heures.
**Exercice:**
1. Déployer un StatefulSet MySQL.
2. Créer une policy Kasten configurée pour déclencher un backup automatique toutes les 6 heures.
3. Valider la planification via l’interface ou via `kubectl get`.
4. Laisser tourner et vérifier que plusieurs sauvegardes sont créées automatiquement.

### Scenario 14: Définir des règles d’exclusion pour ne pas sauvegarder certains namespaces.
**Exercice:**
1. Déployer deux namespaces : `production` et `test`.
2. Créer une policy qui cible uniquement le namespace `production` et exclut `test`.
3. Lancer des sauvegardes automatiques.
4. Vérifier que les workloads dans `test` ne sont pas sauvegardés.

### Scenario 15: Associer une policy à des workloads critiques et configurer la rétention de 7 jours.
**Exercice:**
1. Déployer une application critique dans un namespace `critical-apps`.
2. Configurer une policy avec une rétention stricte de 7 jours.
3. Simuler le passage de plusieurs jours (ou manipuler les dates) et vérifier l’expiration automatique des anciennes sauvegardes.

---

## Sauvegarder et restaurer des workloads, notamment les StatefulSets

### Scenario 16: Sauvegarder un StatefulSet MySQL et restaurer sur un namespace différent.
**Exercice:**
1. Déployer un StatefulSet MySQL dans `namespace-a`.
2. Sauvegarder ce StatefulSet avec Kasten.
3. Restaurer le StatefulSet dans `namespace-b` et modifier le PVC si nécessaire.
4. Vérifier l’intégrité des données après restauration.

### Scenario 17: Restaurer un StatefulSet sans restaurer le PVC.
**Exercice:**
1. Créer un StatefulSet avec PVC de 1Go.
2. Sauvegarder le StatefulSet.
3. Restaurer uniquement les ressources Kubernetes (sans le PVC) dans un namespace de test.
4. Déployer manuellement un nouveau PVC vierge et associer-le au StatefulSet restauré.

### Scenario 18: Restaurer une base sur un autre cluster en modifiant le nom du StatefulSet.
**Exercice:**
1. Sauvegarder un StatefulSet `mysql-db` sur le cluster A.
2. Restaurer sur cluster B en renommant la ressource StatefulSet (ex: `mysql-db-dr`).
3. Adapter les services et secrets associés.
4. Tester la connexion à la base restaurée.

---

## Tester et valider les restaurations pour assurer la récupération des données

### Scenario 19: Simuler la suppression d’un namespace et valider la restauration.
**Exercice:**
1. Déployer une app avec backend et frontend dans un namespace `app-test`.
2. Sauvegarder l’ensemble du namespace.
3. Supprimer entièrement `app-test`.
4. Restaurer via Kasten et vérifier que les deux apps fonctionnent.

### Scenario 20: Restaurer uniquement un PVC.
**Exercice:**
1. Déployer une base PostgreSQL avec deux PVC (données + logs).
2. Supprimer uniquement le PVC des logs.
3. Restaurer uniquement ce PVC depuis Kasten.
4. Vérifier que la base fonctionne correctement avec les logs restaurés.

### Scenario 21: Automatiser des tests de restauration avec un script.
**Exercice:**
1. Écrire un script bash ou Python qui utilise l’API de Kasten pour lancer une restauration.
2. Automatiser la validation en exécutant un test de santé sur l’application restaurée (ex: `curl` ou `pg_isready`).
3. Générer un rapport automatique du résultat.

---

## Gérer la continuité des opérations et la reprise après sinistre (DR)

### Scenario 22: Planifier une procédure de basculement manuel.
**Exercice:**
1. Déployer une app critique sur un cluster principal.
2. Sauvegarder cette app avec Kasten et exporter la sauvegarde vers un bucket S3 multi-régions.
3. Restaurer manuellement la sauvegarde sur un cluster secondaire (DR cluster).
4. Documenter la procédure étape par étape.

### Scenario 23: Réplication automatique vers une autre région.
**Exercice:**
1. Créer deux buckets S3 dans deux régions distinctes.
2. Configurer la replication automatique de sauvegardes via les Location Profiles de Kasten.
3. Effectuer plusieurs backups et vérifier leur présence dans les deux régions.

### Scenario 24: Simuler une panne de zone et restaurer dans une autre AZ.
**Exercice:**
1. Déployer une application sur une AZ (Availability Zone) spécifique.
2. Simuler la perte de cette AZ (en supprimant le nœud ou via un test théorique).
3. Restaurer l’application sur une autre AZ en modifiant la `nodeAffinity` dans les manifests Kubernetes.

---

## Automatiser le basculement et la restauration

### Scenario 28: Déployer un pipeline CI/CD de restauration automatisée.
**Exercice:**
1. Créer un pipeline GitLab CI ou Jenkins qui déclenche une restauration Kasten via l’API.
2. Inclure une étape de vérification automatisée post-restauration (healthcheck ou tests applicatifs).
3. Exécuter le pipeline après un incident simulé.

### Scenario 29: Utiliser les hooks Kanister pour automatiser la reconfiguration d’applications.
**Exercice:**
1. Définir un blueprint Kanister pour automatiser la ré-application de configurations (ex: changer les variables d’environnement).
2. Associer ce blueprint à une policy Kasten.
3. Exécuter la sauvegarde + restauration et vérifier que la configuration est bien adaptée automatiquement.

### Scenario 30: Basculement automatisé entre clusters via scripts.
**Exercice:**
1. Écrire un script Terraform ou bash qui restaure les sauvegardes Kasten sur un cluster distant.
2. Automatiser la mise à jour des DNS pour basculer le trafic vers le cluster secondaire après restauration.
3. Simuler la panne et déclencher le basculement automatiquement.

---

## Assurer la mobilité des workloads entre clusters

### Scenario 34: Sauvegarder une app sur le cluster A et restaurer sur cluster B.
**Exercice:**
1. Déployer une app dans un cluster source.
2. Sauvegarder l’application et ses PVCs.
3. Exporter la sauvegarde dans un bucket partagé.
4. Restaurer sur un cluster cible en une nouvelle instance.

### Scenario 35: Modifier les configurations de services lors de la restauration.
**Exercice:**
1. Sauvegarder une application exposée en LoadBalancer sur le cluster source.
2. Restaurer sur un cluster cible avec transformation pour exposer l’application uniquement en ClusterIP.
3. Valider l’accessibilité via un Ingress ou une autre méthode.

### Scenario 36: Utiliser des JSON Patch pour transformer des manifests.
**Exercice:**
1. Créer un fichier JSON Patch qui modifie l’image container et la ressource CPU lors de la restauration.
2. Appliquer ce patch via Kasten lors d’une restauration inter-cluster.
3. Vérifier que les ressources déployées incluent bien les modifications.

---

## Sécuriser l'accès et la gestion des permissions

### Scenario 37: Créer des rôles RBAC pour restreindre l’accès à certains namespaces.
**Exercice:**
1. Définir un rôle RBAC Kubernetes autorisant uniquement la gestion des sauvegardes pour `namespace-a`.
2. Associer ce rôle à un utilisateur Kasten via l’OIDC ou un ServiceAccount.
3. Tester les restrictions via l’interface Kasten K10.

### Scenario 38: Intégrer un fournisseur OIDC.
**Exercice:**
1. Configurer un fournisseur Keycloak avec deux groupes d’utilisateurs.
2. Associer des rôles Kasten différents selon le groupe (ex: lecteur, admin).
3. Tester l’accès à Kasten avec différents comptes OIDC.

### Scenario 39: Auditer les actions utilisateurs sur Kasten K10.
**Exercice:**
1. Activer l’audit log sur le cluster Kubernetes.
2. Capturer et analyser les logs liés aux actions de sauvegarde/restauration sur Kasten.
3. Filtrer par utilisateur et par type d’action (create, delete, restore).

---

## Déployer Kasten K10 en environnement restreint (air-gapped)

### Scenario 40: Préparer les images pour un déploiement air-gapped.
**Exercice:**
1. Télécharger toutes les images nécessaires de Kasten K10.
2. Pousser ces images sur un registre privé interne.
3. Vérifier la disponibilité des images depuis le cluster air-gapped.

### Scenario 41: Installer Kasten K10 en mode air-gapped.
**Exercice:**
1. Déployer Kasten en utilisant uniquement le registre privé.
2. Configurer un stockage NFS local comme backend de sauvegarde.
3. Effectuer une sauvegarde et restauration complète sans accès Internet.

### Scenario 42: Sauvegarder vers NFS en mode air-gapped.
**Exercice:**
1. Déployer un serveur NFS local.
2. Configurer un Location Profile NFS dans Kasten.
3. Sauvegarder un workload et restaurer à partir de ce stockage NFS.

---

## Superviser et observer les opérations avec Kasten K10

### Scenario 46: Exposer les métriques Prometheus.
**Exercice:**
1. Installer Prometheus et Grafana dans le cluster.
2. Ajouter le ServiceMonitor Kasten.
3. Vérifier l’apparition des métriques dans Prometheus.

### Scenario 47: Créer des requêtes PromQL pour Kasten.
**Exercice:**
1. Écrire des requêtes PromQL pour suivre :
    - le nombre de jobs de sauvegarde en échec,
    - la durée moyenne des sauvegardes,
    - la latence des restaurations.
2. Visualiser les résultats dans Grafana.

### Scenario 48: Configurer Grafana pour Kasten.
**Exercice:**
1. Importer ou créer un dashboard Grafana dédié à Kasten.
2. Ajouter des panels pour la volumétrie, les échecs et la durée des jobs.
3. Personnaliser l’alerte visuelle en cas de job échoué.

### Scenario 49: Configurer des alertes Prometheus.
**Exercice:**
1. Configurer une alerte Prometheus sur `kastenk10_action_failure_total`.
2. Simuler un échec de sauvegarde.
3. Vérifier la réception de l’alerte.

### Scenario 50: Tester l’envoi d’alertes email.
**Exercice:**
1. Configurer Alertmanager pour envoyer des e-mails via SMTP.
2. Simuler plusieurs types d’alertes critiques.
3. Vérifier que les alertes sont bien reçues dans la boîte mail.

---

# QCM - Validation des acquis sur Kasten K10

### Question 1:
**Quel composant de Kasten K10 est responsable de l’exécution des sauvegardes sur les workloads Kubernetes ?**

- A) Aggregator
- B) Executor
- C) Kanister
- D) K10 UI

> **Réponse correcte:** B

---

### Question 2:
**Quel type de workload nécessite généralement l’inclusion de PVC lors de la sauvegarde ?**

- A) Deployment stateless
- B) Job
- C) StatefulSet
- D) ConfigMap

> **Réponse correcte:** C

---

### Question 3:
**Quelle commande permet d’installer Kasten K10 via Helm ?**

- A) `helm install k10 kasten/k10`
- B) `kubectl apply -f kasten.yaml`
- C) `helm deploy kasten --namespace kasten-io`
- D) `kastctl install`

> **Réponse correcte:** A

---

### Question 4:
**Lors d’un déploiement air-gapped, que devez-vous impérativement préparer ?**

- A) Un cluster OpenShift
- B) Des ingress controllers publics
- C) Un registre privé pour les images Docker
- D) Un bucket S3 public

> **Réponse correcte:** C

---

### Question 5:
**Quelle est la bonne définition d’une sauvegarde incrémentale ?**

- A) Sauvegarde de l’ensemble des données à chaque exécution
- B) Sauvegarde uniquement des nouvelles données ou changements depuis la dernière sauvegarde
- C) Sauvegarde uniquement de la configuration Kubernetes
- D) Sauvegarde complète mais compressée

> **Réponse correcte:** B

---

### Question 6:
**Dans Kasten K10, que permet un Location Profile ?**

- A) Gérer l’accès OIDC des utilisateurs
- B) Configurer la localisation des snapshots Kubernetes
- C) Définir la destination de stockage des sauvegardes (S3, NFS, etc.)
- D) Appliquer une transformation aux manifests Kubernetes

> **Réponse correcte:** C

---

### Question 7:
**Qu’est-ce que Kanister dans l’architecture Kasten ?**

- A) L’interface utilisateur web
- B) Un framework pour créer des blueprints et automatiser les opérations applicatives
- C) Le moteur de planification des sauvegardes
- D) Le démon responsable de restaurer les volumes PVC

> **Réponse correcte:** B

---

### Question 8:
**Pour assurer la résilience face à une perte totale d’un cluster Kubernetes, il faut :**

- A) Avoir uniquement des sauvegardes locales sur PVC
- B) Avoir une sauvegarde de l’ETCD
- C) Disposer de backups multi-sites ou inter-clusters via Kasten
- D) Sauvegarder uniquement les secrets Kubernetes

> **Réponse correcte:** C

---

### Question 9:
**Quelle est l’une des métriques critiques pour superviser Kasten via Prometheus ?**

- A) `kubernetes_pod_uptime_seconds`
- B) `kastenk10_actions_duration_seconds`
- C) `k10_executor_restart_count`
- D) `pod_network_throughput`

> **Réponse correcte:** B

---

### Question 10:
**Que permet l’intégration OIDC dans Kasten ?**

- A) Gérer le cycle de vie des pods Kanister
- B) Authentifier les utilisateurs via un fournisseur externe (Keycloak, Auth0…)
- C) Activer la réplication automatique des sauvegardes
- D) Restreindre les ressources CPU pour Kasten K10

> **Réponse correcte:** B


