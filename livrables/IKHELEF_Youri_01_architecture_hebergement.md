# Architecture cible et choix de l’hébergement

## Contexte

L’application étudiée est une plateforme web composée de plusieurs composants techniques :

* Backend principal développé avec **Laravel (PHP 8.4)**
* Microservice de supervision développé avec **Next.js**
* Base de données relationnelle **MySQL**
* Base NoSQL **MongoDB**
* Système de cache et de sessions **Redis**
* Exposition de endpoints publics :

  * API REST
  * moteur de recherche
  * webhook externe
* Interface d’administration (phpMyAdmin)
* Environnements distincts : **qualification et production**

L’infrastructure actuelle repose sur des machines virtuelles configurées manuellement, ce qui entraîne :

* difficulté de maintenance
* absence de reproductibilité
* faible industrialisation
* risque opérationnel élevé
* supervision limitée

L’objectif est donc de définir une **architecture cible cloud industrialisée, sécurisée et scalable**.



## Choix du fournisseur Cloud

Le fournisseur retenu est **AWS**, pour les raisons suivantes :

* large catalogue de services managés
* intégration native réseau / sécurité / supervision
* automatisation du déploiement
* haute disponibilité multi-AZ
* gestion avancée des secrets et des logs
* capacité de montée en charge rapide
* facturation à l’usage

---

##  Architecture cible proposée

###  Réseau

* 1 **VPC dédié par environnement** (qualification / production)
* Sous-réseaux publics :

  * Load Balancer
  * Bastion éventuel
* Sous-réseaux privés :

  * services applicatifs
  * bases de données
  * cache

Sécurité assurée via :

* Security Groups
* Network ACL
* absence d’exposition directe des bases

---

### Exposition web

* **Route 53** : gestion DNS
* **CloudFront** : CDN et optimisation des performances
* **AWS WAF** : protection contre attaques applicatives
* **Application Load Balancer (ALB)** :

  * terminaison TLS
  * routage vers services backend

Certificats HTTPS fournis via **AWS Certificate Manager (ACM)**.

---

### Hébergement applicatif

Les applications seront conteneurisées et déployées sur :

**Amazon ECS Fargate**

Services déployés :

* Service Laravel (backend API)
* Service Next.js (dashboard supervision)

Avantages :

* pas de gestion de VM
* déploiement reproductible
* autoscaling
* isolation des workloads
* intégration CI/CD facilitée

---

### Gestion des données

* **Amazon RDS MySQL**

  * sauvegardes automatiques
  * réplication Multi-AZ
  * maintenance automatisée

* **Amazon ElastiCache Redis**

  * gestion des sessions
  * cache applicatif

* **MongoDB**

  * hébergement envisagé :

    * MongoDB Atlas
    * ou Amazon DocumentDB après validation de compatibilité
    * ou instance EC2 privée dédiée si contraintes techniques

---

### Stockage

* **Amazon S3**

  * stockage des fichiers applicatifs
  * exports
  * logs archivés
  * sauvegardes applicatives

---

### Gestion des secrets

* **AWS Secrets Manager** ou **SSM Parameter Store**

  * credentials base de données
  * tokens webhook
  * clés API externes
  * variables d’environnement sensibles

---

### Supervision et logs

* **Amazon CloudWatch**

  * métriques infrastructure
  * logs applicatifs centralisés
  * alertes (CPU, mémoire, erreurs HTTP, latence)

* dashboards de monitoring

* alarmes automatiques

---

### Administration

* accès via **AWS Systems Manager Session Manager**
* suppression du SSH public direct
* traçabilité des connexions

---

## Séparation des environnements

* VPC distinct qualification / production
* bases séparées
* secrets séparés
* pipelines CI/CD indépendants
* quotas et scaling différents

---

## Justification des choix

Cette architecture permet :

* industrialisation du déploiement
* réduction du risque de configuration manuelle
* amélioration de la sécurité
* meilleure résilience
* montée en charge automatique
* centralisation de la supervision
* réduction des coûts liés à la maintenance
* facilitation des mises en production

Elle constitue une cible cohérente pour moderniser l’infrastructure existante et assurer le maintien en conditions opérationnelles de l’application.

---