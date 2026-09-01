# 🚀 E-Commerce Data Pipeline - Azure Databricks & Medallion Architecture

## 📋 Contexte et Objectif du Projet
Ce projet s'inscrit dans le cadre de la construction d'un pipeline de données complet et automatisé (Data Engineering) basé sur l'architecture **Medallion (Bronze, Silver, Gold)**. 
* **Objectif principal** : Ingérer, nettoyer, structurer et transformer le dataset public **Brazilian E-commerce (Olist)** provenant de Kaggle afin de le rendre exploitable pour des analyses avancées et des tableaux de bord décisionnels.
* **Environnement Cloud** : Microsoft Azure (Azure Data Lake Gen2, Microsoft Entra ID) orchestré via Azure Databricks et structuré à l'aide d'Unity Catalog.

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
* Téléchargement automatisé du dataset Olist depuis Kaggle vers l'environnement local Databricks.
* Structuration des données et téléversement des fichiers CSV bruts (`customers`, `orders`, `products`, etc.) directement dans le conteneur cloud Azure Data Lake Gen2 (`bronze/raw_csv/`).
* Utilisation du SDK Python `azure-storage-blob` pour garantir l'écriture via le jeton SAS, assurant une compatibilité totale avec les clusters Databricks Serverless.

---

## 🔜 Prochaines Étapes
* **Couche Silver** : Nettoyage des données, gestion des valeurs manquantes, suppression des doublons et typage rigoureux des schémas.
* **Couche Gold** : Modélisation dimensionnelle (tables de faits et de dimensions) et création d'agrégations prêtes pour la Business Intelligence (Power BI / Apache Superset).