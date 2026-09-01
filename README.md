# 🚀 E-Commerce Data Pipeline - Azure Databricks & Medallion Architecture

## 📋 Contexte et Objectif du Projet

Ce projet consiste à concevoir et mettre en œuvre un **pipeline de données complet et automatisé** basé sur l'architecture **Medallion (Bronze, Silver, Gold)** afin de valoriser les données du dataset public **Brazilian E-commerce (Olist)** disponible sur Kaggle.

L'objectif est de construire une plateforme analytique permettant d'**ingérer, nettoyer, transformer et structurer les données e-commerce** afin de produire des données fiables et exploitables pour l'analyse et la prise de décision.

### 🎯 Objectifs principaux

* **Data Engineering** : Mettre en place un pipeline permettant l'ingestion, le nettoyage, la transformation et la consolidation des différentes tables du dataset Olist.
* **Architecture Lakehouse** : Organiser les données selon les couches **Bronze, Silver et Gold** pour séparer les données brutes, nettoyées et prêtes pour l'analyse.
* **Data Analytics** : Construire des tables analytiques permettant d'étudier les **ventes, clients, produits, vendeurs, paiements, livraisons et avis clients**.
* **Business Intelligence** : Développer un **dashboard interactif** permettant de suivre les principaux KPI et d'analyser les performances commerciales et opérationnelles.
* **Aide à la décision** : Fournir des indicateurs et analyses permettant d'identifier les tendances, les performances et les opportunités d'amélioration.

### ☁️ Environnement Cloud et Technologies

Le projet est déployé dans l'environnement **Microsoft Azure** et s'appuie sur :

* **Azure Data Lake Storage Gen2 (ADLS Gen2)** pour le stockage des données.
* **Azure Databricks** pour l'ingestion et le traitement distribué avec **PySpark**.
* **Unity Catalog** pour le catalogage, la gouvernance et la gestion des accès.
* **Databricks SQL Warehouse** pour l'analyse et l'exécution des requêtes SQL.
* **Dashboard** pour la visualisation des KPI et des analyses business.


---

## 🛠️ Stack Technique & Outils Utilisés
* **Cloud Infrastructure** : Azure Resource Group, Azure Data Lake Storage Gen2
* **Sécurité & IAM** : Microsoft Entra ID (App Registration, Service Principal, Client Secret, SAS Token)
* **Orchestration & Calcul** : Azure Databricks (Compute Serverless), Apache Spark, Python (Pandas)
* **Gouvernance des Données** : Unity Catalog
* **Versionnage & CI/CD** : Git / GitHub (Databricks Repos)
* **Source des données** : Kaggle (`olistbr/brazilian-ecommerce`)

---

## 🏗️ Étapes Réalisées

### 1. Mise en place de l'Infrastructure Cloud (Azure)
* Création d'un **Resource Group** dédié, centralisant l'espace de travail Databricks et le stockage.
* Déploiement d'un compte de stockage **Azure Data Lake Gen2** configuré avec une réplication **LRS** (Locally Redundant Storage).
* Activation de l'option **Hierarchical Namespace** pour optimiser le stockage analytique.
* Création du conteneur racine (`bronze`) directement sur le serveur.

### 2. Configuration de la Sécurité et des Accès (Microsoft Entra ID & IAM)
* Création d'une **App Registration** (Service Principal) dans Microsoft Entra ID, configurée pour utiliser uniquement le tenant Azure actuel (mono-tenant).
* Récupération des identifiants critiques : **Application (client) ID** et **Tenant ID**.
* Génération d'un **Client Secret** sécurisé pour l'application.
* Configuration des droits d'accès via **IAM** (Identity and Access Management) sur le Data Lake.
* Attribution du rôle **Contributeur aux données en blob du stockage** (*Storage Blob Data Contributor*) au Service Principal.
* Vérification manuelle de l'attribution des rôles pour garantir l'accès en lecture/écriture au Data Lake.

### 3. Initialisation de l'Environnement de Développement (CI/CD)
* Création des notebooks de développement dans Azure Databricks.
* Liaison de l'espace de travail Databricks (`Databricks Repos`) avec le dépôt distant **GitHub**.
* Mise en place d'un fichier `.gitignore` strict pour exclure les variables d'environnement.
* Création et configuration d'un fichier `.env` local pour sécuriser les jetons SAS et les clés de connexion.

### 4. Ingestion des Données Brutes (Couche Bronze)
La **couche Bronze** correspond à la zone d'atterrissage des données. Son rôle est de **collecter et conserver les données sources dans leur état le plus brut possible**, avec un minimum de transformations, afin de garantir la traçabilité, la reproductibilité et la possibilité de rejouer les traitements en aval.

* Téléchargement automatisé du dataset Olist depuis Kaggle vers l'environnement local Databricks.
* Structuration des données et téléversement des fichiers CSV bruts (`customers`, `orders`, `products`, etc.) directement dans le conteneur cloud Azure Data Lake Gen2 (`bronze/raw_csv/`).
* Conservation des fichiers dans un format proche de la source pour préserver l'intégrité des données d'origine.
* Centralisation des données brutes dans une zone unique servant de point d'entrée pour les traitements analytiques ultérieurs.
* Utilisation du SDK Python `azure-storage-blob` pour garantir l'écriture via le jeton SAS, assurant une compatibilité totale avec les clusters Databricks Serverless.

### 5. Préparation et Fiabilisation des Données (Couche Silver)
La **couche Silver** représente l'étape de **nettoyage, standardisation et enrichissement technique** des données issues de la Bronze. L'objectif est d'obtenir des tables fiables, cohérentes et exploitables, prêtes à être utilisées pour les analyses métier et la construction de la couche Gold.

* Nettoyage des données : gestion des valeurs manquantes, suppression des doublons et correction des incohérences.
* Standardisation des schémas et typage rigoureux des colonnes pour homogénéiser les différentes tables du dataset.
* Contrôle qualité sur les données clés (identifiants, dates, montants, statuts de commande, informations clients et produits).
* Préparation des jointures entre tables métier (`orders`, `customers`, `order_items`, `payments`, `reviews`, etc.) pour faciliter les usages analytiques.
* Production de jeux de données consolidés et fiables servant de base à la modélisation de la couche Gold.

### 6. Modélisation Analytique et Valorisation Métier (Couche Gold)
La **couche Gold** correspond au niveau de **modélisation analytique final**, conçu pour rendre les données directement exploitables par les outils de reporting, de visualisation et d'aide à la décision. Dans ce projet, elle s'appuie sur la création d'un **schéma en étoile** dans le schéma `dbw_lab.gold`, afin d'organiser les données autour d'indicateurs métier clairs et performants.

* Création de dimensions métier dédiées aux **clients**, **vendeurs**, **produits** et **dates** pour structurer l'analyse.
* Construction de la table de faits `fact_order_items`, centrée sur les lignes de commande et enrichie avec les informations de commande, de client, de prix et de frais de livraison.
* Traduction de la catégorie produit via la table `product_category_name_translation` afin de rendre les analyses plus lisibles.
* Génération d'une dimension date à partir des dates d'achat pour faciliter les analyses temporelles par année, mois, jour et trimestre.
* Organisation des tables Gold selon une logique orientée **Business Intelligence**, prête pour l'exploration dans Databricks SQL ou dans un dashboard.
* Export des tables finales du modèle en étoile (`dim_customers`, `dim_sellers`, `dim_products`, `dim_date`, `fact_order_items`) vers le conteneur Azure `gold`, dans le dossier `star_schema/`, pour mise à disposition des consommations aval.

---

## ⚙️ Mise en place du Pipeline End-to-End

Afin d'automatiser l'enchaînement des traitements, une définition de pipeline a été ajoutée dans le fichier `pipline.yml`. Cette configuration permet d'orchestrer les différentes étapes du projet sous forme de **Lakeflow Jobs**, en respectant la logique de dépendance entre les couches Bronze, Silver et Gold.

Le pipeline `Olist_End_to_End_Pipeline` repose sur trois tâches principales exécutées de manière séquentielle :

* **Ingestion_Bronze** : exécute le notebook `01_bronze` pour télécharger les données sources, les structurer et les déposer dans la couche Bronze.
* **Transformation_Silver** : exécute le notebook `02_silver` après la réussite de la couche Bronze afin de nettoyer, standardiser et fiabiliser les données.
* **Modelisation_Gold** : exécute le notebook `03_gold` après la couche Silver pour construire le schéma en étoile analytique et préparer les tables finales destinées au reporting.

Cette orchestration présente plusieurs avantages :

* Elle garantit le **respect de l'ordre logique des traitements** grâce aux dépendances entre les tâches.
* Elle facilite l'**exécution automatisée de bout en bout** du projet.
* Elle améliore la **maintenabilité** et la **reproductibilité** des traitements.
* Elle permet une intégration cohérente avec le dépôt GitHub du projet via la section `git_source`.

La configuration active également une file d'attente (`queue.enabled: true`) et un mode `PERFORMANCE_OPTIMIZED`, afin d'améliorer la gestion de l'exécution et les performances du job dans l'environnement Databricks.

## 📊 Création du Dashboard

La phase de visualisation a consisté à concevoir un **dashboard décisionnel** dans Databricks SQL afin de transformer le modèle Gold en support d'analyse métier. L'objectif du dashboard est de proposer une lecture synthétique et interactive de la performance commerciale, du catalogue produit, de la répartition géographique des clients et du suivi logistique.

Le dashboard a été construit à partir des tables du schéma `dbw_lab.gold`, en particulier la table de faits `fact_order_items` et les dimensions `dim_date`, `dim_products`, `dim_customers` et `dim_sellers`. Chaque visualisation a été pensée pour répondre à une question métier précise.

### 8. Vue d'ensemble
**Type de graphique :** Carte à indicateurs (*Counter*)

Cette section affiche les KPI globaux les plus importants du projet :
* **Chiffre d'Affaires Total** : somme de `total_item_value` dans `fact_order_items`.
* **Nombre de commandes** : comptage distinct des `order_id`.

**Objectif métier :** fournir, dès l'ouverture du dashboard, une vision immédiate du niveau d'activité global de la plateforme e-commerce.

### 9. Tendances des ventes
**Type de graphique :** Graphique en courbe (*Line*)

Cette visualisation montre l'**évolution mensuelle des revenus** à partir de la jointure entre `fact_order_items` et `dim_date`.

* La table `fact_order_items` fournit les montants de vente.
* La table `dim_date` permet d'agréger les résultats par mois, trimestre ou année.

**Objectif métier :** identifier les périodes de croissance, les ralentissements, ainsi que la saisonnalité des ventes.

### 10. Performance du catalogue
**Type de graphique :** Barres horizontales (*Bar*)

Cette section présente le **Top 10 des catégories de produits les plus rentables** en s'appuyant sur la jointure entre `fact_order_items` et `dim_products`.

* `fact_order_items` contient les montants générés par ligne de commande.
* `dim_products` apporte la catégorie produit traduite pour une lecture plus claire.

**Objectif métier :** repérer les catégories les plus contributrices au chiffre d'affaires et orienter les décisions commerciales ou marketing.

### 11. Analyse géographique
**Type de graphique :** Carte choroplèthe (*Map*)

Cette visualisation permet de représenter la **densité des clients et le volume de ventes par État** grâce à la jointure entre `fact_order_items` et `dim_customers`.

* `dim_customers` fournit l'information géographique, notamment `customer_state`.
* `fact_order_items` permet de mesurer le niveau d'activité associé à chaque zone.

**Objectif métier :** localiser les régions les plus actives, détecter les disparités territoriales et mieux cibler les actions commerciales.

### 12. Suivi logistique
**Type de graphique :** Anneau (*Donut*) ou jauge

Cette partie mesure le **taux de commandes livrées vs annulées** à partir de la colonne `order_status` de `fact_order_items`.

**Objectif métier :** suivre la qualité opérationnelle du processus de commande et identifier les signaux de friction logistique.

### 13. Top vendeurs
**Type de graphique :** Barres horizontales (*Bar*)

Cette visualisation met en avant les **vendeurs générant le plus de chiffre d'affaires** à partir de `fact_order_items`, avec enrichissement via `dim_sellers`.

* Le calcul repose sur la somme de `total_item_value` par `seller_id`.
* La jointure avec `dim_sellers` permet d'ajouter des informations de localisation comme la ville et l'État.
* Un filtre sur `order_status = 'delivered'` permet de ne retenir que les ventes réellement abouties.

**Objectif métier :** identifier les vendeurs les plus performants et mieux comprendre leur contribution à la performance globale de la marketplace.

---

## 📈 Interprétation des Résultats

Les résultats du dashboard permettent de dégager plusieurs enseignements métier importants :

* Le **chiffre d'affaires total** et le **volume de commandes** offrent une mesure directe de la performance globale de l'activité e-commerce.
* L'**évolution mensuelle des revenus** permet de mettre en évidence une dynamique temporelle, utile pour détecter les périodes fortes, les creux d'activité et la saisonnalité.
* Le **top des catégories de produits** met en avant les segments les plus rentables, ce qui peut orienter les investissements marketing, la gestion de stock ou la stratégie produit.
* La **répartition géographique des clients et des ventes** aide à comprendre les marchés régionaux les plus actifs et à cibler les zones à fort potentiel.
* Le **suivi des commandes livrées vs annulées** constitue un indicateur clé de la qualité opérationnelle et de la satisfaction potentielle des clients.
* Le **classement des vendeurs** permet d'identifier les partenaires les plus performants et de mieux piloter l'écosystème marketplace.

Dans l'ensemble, ce dashboard transforme les données préparées dans la couche Gold en **indicateurs lisibles, actionnables et orientés décision**, facilitant à la fois le suivi de la performance commerciale et l'analyse des enjeux opérationnels.

---
