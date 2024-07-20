# liste_epicerie

Présentation de l'application : (2x même vidéo)
* https://www.youtube.com/watch?v=CvIxjzk62bs

## Structure de la base de données

### Structure de la base de données locale

* une table d'article
  * Article

### Structure de la base de données Firestore

* articleGlobaux
  * Liste des articles globaux
    * Article
* epicerieGroupe
  * Liste des identifiants de groupe
    * Liste des listes d'épicerie
      * Liste des articles d'épicerie
        * Article
* groupes
  * Liste des identifiants de groupe
    * liste des membres du groupe
    * nombre de listes d'épicerie
* users
  * Liste des utilisateurs
    * email
    * identifiant du Groupe qu'il appartien
    * nom d'utilisateur

un Article:
*  _id;
*  product_name;
*  allergens;
*  brands;
*  categories;
*  creator;
*  image_url;
*  nutriscore_grade;
*  url;
*  status;
*  dateAchat;


## Règles de sécurité Firebase store

```Firebase Security Rules

rules_version = '2';
service cloud.firestore {
match /databases/{database}/documents {
match /{document=**} {
allow read : if	request.auth != null;
}

    // Règles pour la collection 'articlesGlobaux'
    match /articlesGlobaux/{document=**} {
      allow read, write, update: if true;
    }
    
    // Règles pour la collection 'epicerieGroupe'
    match /epicerieGroupe/{groupId}/{document=**} {
      allow read, write, update: if request.auth != null && isGroupMember(groupId, request.auth.uid);
    }
    
    // Règles pour la collection 'groupes'
    match /groupes/{groupId}/{document=**} {
      allow read, write, update: if request.auth != null && isGroupMember(groupId, request.auth.uid);
    }
    
    match /groupes/{documents=**} {
    	allow write: if request.auth != null;
    }
    
    // Règles pour la collection 'users'
    match /users/{userId} {
      allow write, update: if (request.auth != null && request.auth.uid == userId);
// Permettre à un utilisateur d'ajouter un autre utilisateur à son groupe en utilisant son courriel
allow update: if request.auth != null
}

    // Fonction pour vérifier si un utilisateur est membre du groupe
    function isGroupMember(groupId, userId) {
			return get(/databases/$(database)/documents/users/$(userId)).data.idGroupe == groupId;
    }

}
}

```