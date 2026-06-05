# 📒 NumberBook — Application Android connectée à un Backend distant

**Auteur :** DOSSAH Landry Yao
**Cours :** Programmation Mobile — ENSA Marrakech
**Date :** Juin 2026
**Stack :** Java · Android SDK · Retrofit 2 · PHP 8 · MySQL · PDO · RecyclerView

---

## 🚀 Objectif du projet

Ce projet consiste à développer une application Android complète nommée **NumberBook**, connectée à un backend distant PHP/MySQL, afin de comprendre le fonctionnement d'une architecture client/serveur mobile moderne.

L'application met en œuvre :

- 📖 Lecture des contacts système Android via `ContentResolver`
- 🔐 Gestion des permissions runtime (`READ_CONTACTS`)
- 📋 Affichage des contacts dans une liste moderne avec `RecyclerView`
- 🌐 Communication HTTP avec un backend distant via `Retrofit 2`
- 💾 Persistance des données dans une base MySQL distante
- 🔍 Recherche distante par nom ou numéro de téléphone
- 🔄 Sérialisation/désérialisation JSON automatique avec `Gson`

---

## 🧠 Concepts maîtrisés

- Accès aux données système Android via `ContentResolver` et `Cursor`
- Gestion des permissions runtime avec `ActivityResultLauncher`
- Architecture `RecyclerView` / `Adapter` / `ViewHolder`
- Appels réseau asynchrones avec `Retrofit 2` et `enqueue()`
- Conversion automatique JSON ↔ objets Java avec `Gson`
- Architecture backend PHP en couches (config / model / service / api)
- Requêtes préparées PDO pour la sécurité des données
- Communication client Android ↔ serveur PHP ↔ base MySQL

---

## 🏗️ Architecture

```
NumberBook/
│
├── 📱 Application Android (Java)
│   ├── Contact.java              → Modèle métier
│   ├── ApiResponse.java          → Modèle de réponse serveur
│   ├── ContactApi.java           → Interface Retrofit (endpoints)
│   ├── RetrofitClient.java       → Configuration HTTP client
│   ├── ContactAdapter.java       → Adapter RecyclerView
│   └── MainActivity.java         → Activité principale
│
└── 🖥️ Backend PHP (numberbook-api/)
    ├── config/
    │   └── Database.php          → Connexion PDO à MySQL
    ├── model/
    │   └── Contact.php           → Classe métier Contact
    ├── service/
    │   └── ContactService.php    → Logique d'accès aux données
    └── api/
        ├── insertContact.php     → POST — Insertion d'un contact
        ├── getAllContacts.php    → GET  — Récupération complète
        └── searchContact.php    → GET  — Recherche par mot-clé
```

### Schéma de communication

```
Téléphone Android
      │
      │  HTTP (Retrofit + Gson)
      ▼
Backend PHP (XAMPP / Apache)
      │
      │  PDO
      ▼
Base de données MySQL
```

---

## ⚙️ Fonctionnalités

| Fonctionnalité | Description |
|---|---|
| 📖 Charger les contacts | Lit les contacts du téléphone via `ContentResolver` |
| 🔐 Permission runtime | Demande `READ_CONTACTS` à l'utilisateur |
| 📋 Affichage liste | Contacts affichés dans un `RecyclerView` |
| 🔄 Synchronisation | Envoie tous les contacts au serveur via Retrofit |
| 💾 Persistance MySQL | Contacts stockés dans la base distante |
| 🔍 Recherche distante | Recherche par nom ou numéro dans la base |
| 📡 API REST | Backend PHP expose 3 endpoints JSON |

---

## 🗄️ Base de données

```sql
CREATE DATABASE IF NOT EXISTS numberbook
CHARACTER SET utf8mb4
COLLATE utf8mb4_unicode_ci;

USE numberbook;

CREATE TABLE contact (
    id         INT AUTO_INCREMENT PRIMARY KEY,
    name       VARCHAR(150) NOT NULL,
    phone      VARCHAR(50)  NOT NULL,
    source     VARCHAR(50)  DEFAULT 'mobile',
    created_at DATETIME     DEFAULT CURRENT_TIMESTAMP
);
```

| Colonne | Type | Description |
|---|---|---|
| `id` | INT AUTO_INCREMENT | Identifiant unique |
| `name` | VARCHAR(150) | Nom du contact |
| `phone` | VARCHAR(50) | Numéro (chaîne pour gérer +, espaces, tirets) |
| `source` | VARCHAR(50) | Origine des données (`mobile` par défaut) |
| `created_at` | DATETIME | Date d'insertion automatique |

---

## 📡 API REST — Endpoints

| Méthode | Endpoint | Description | Paramètres |
|---|---|---|---|
| `GET` | `getAllContacts.php` | Retourne tous les contacts | — |
| `GET` | `searchContact.php` | Recherche par mot-clé | `?keyword=ali` |
| `POST` | `insertContact.php` | Insère un contact | `{name, phone}` en JSON |

### Exemple de réponse — `getAllContacts.php`
```json
[
  { "id": 1, "name": "Ali", "phone": "0612345678", "source": "mobile", "created_at": "2026-06-05 10:00:00" },
  { "id": 2, "name": "Landry", "phone": "0698765432", "source": "mobile", "created_at": "2026-06-05 10:00:01" }
]
```

### Exemple de réponse — `insertContact.php`
```json
{ "success": true, "message": "Contact inséré avec succès" }
```

---

## 📄 Permissions Android

```xml
<!-- Lecture des contacts système -->
<uses-permission android:name="android.permission.READ_CONTACTS" />

<!-- Communication réseau avec le backend -->
<uses-permission android:name="android.permission.INTERNET" />
```

> ⚠️ `READ_CONTACTS` est une permission **dangereuse** — elle doit être demandée à l'utilisateur au moment de l'exécution via `ActivityResultLauncher`.

---

## 📦 Dépendances Gradle

```groovy
implementation 'com.squareup.retrofit2:retrofit:2.11.0'
implementation 'com.squareup.retrofit2:converter-gson:2.11.0'
implementation 'androidx.recyclerview:recyclerview:1.3.2'
implementation 'com.google.android.material:material:1.11.0'
```

| Bibliothèque | Rôle |
|---|---|
| `Retrofit 2` | Simplifie les appels HTTP vers l'API REST |
| `converter-gson` | Conversion automatique JSON ↔ objets Java |
| `RecyclerView` | Affichage performant de listes de contacts |
| `Material` | Composants UI modernes Android |

---

## ⚙️ Installation & Configuration

### Prérequis
- Android Studio (Flamingo ou plus récent)
- XAMPP (Apache + MySQL)
- Émulateur Android API 24+ ou téléphone physique

### Backend
1. Copier le dossier `numberbook-api/` dans `C:\xampp\htdocs\`
2. Démarrer **Apache** et **MySQL** dans XAMPP
3. Ouvrir phpMyAdmin et exécuter le script SQL ci-dessus

### Application Android
1. Ouvrir le projet dans Android Studio
2. Dans `RetrofitClient.java`, adapter l'URL selon l'environnement :

```java
// Émulateur Android Studio
private static final String BASE_URL = "http://10.0.2.2/numberbook-api/api/";

// Téléphone physique (remplacer par l'IP de votre machine)
private static final String BASE_URL = "http://192.168.X.X/numberbook-api/api/";
```

3. Dans `AndroidManifest.xml`, vérifier la présence de :
```xml
android:usesCleartextTraffic="true"
```
> Nécessaire pour autoriser les connexions HTTP non chiffrées en local.

4. Lancer l'application avec ▶️

---

## 🎥 Démonstration

👉 Vidéo de la démonstration complète :



https://github.com/user-attachments/assets/8aae0204-7d77-4769-9313-68d65d732a26



---

## 🔐 Bonnes pratiques appliquées

- **Requêtes préparées PDO** — protection contre les injections SQL
- **Permission runtime** — demande explicite à l'utilisateur
- **Appels asynchrones** — `enqueue()` pour ne jamais bloquer l'UI
- **Architecture en couches** — séparation config / model / service / api
- **Singleton Retrofit** — une seule instance HTTP partagée
- **Fermeture du Cursor** — `cursor.close()` pour libérer les ressources

---

## 🧠 Conclusion

Ce projet m'a permis de comprendre :

- Comment Android expose les données système via `ContentResolver`
- La gestion des permissions dangereuses en runtime
- L'architecture `RecyclerView` / `Adapter` / `ViewHolder`
- Le fonctionnement de `Retrofit` pour consommer une API REST
- La construction d'un backend PHP structuré en couches
- La communication complète Android ↔ PHP ↔ MySQL

---

## 👨‍💻 Auteur

**DOSSAH Landry**
ENSA Marrakech — Génie Cyberdéfense et Systèmes de Télécommunications Embarquées
Programmation Mobile Java
