# Documentation Technique Shop Nest

Par Antoine Langevin

Présenté à Dini Ahamada

1<sup>er</sup> décembre 2024

## Introduction

ShopNest est une application web de type e-shop qui se veut d’être comme
un clone de **Amazon**. Le site web offre la possibilité aux
utilisateurs de devenir des marchants et de vendre des produits sur le
site.

## Spécification technique

### Version des logiciels

| **Nom** | **Version** |
|---------|-------------|
| PHP     | 8.2.11      |
| MySQL   | 5.7.11      |

### Version des librairies

| **Nom**           | **Version** |
|-------------------|-------------|
| font-awesome      | 5.15.3      |
| Jquery.dataTables | 1.11.3      |
| Jquery            | 3.5.1       |
| Charts            | \-          |
| Font-awesome      | 4.7.0 (min) |
| Bootstrap         | 4.5.3       |
| Popper            | 1.12.9      |

## Installation

Guide d’installation pour faire fonctionner **ShopNest**.

### Prérequis

-   Compte autorisé sur « GitLab ».

-   Une machine Windows.

-   Une clé SSH valide si vous utilisez le clonage **SSH**.

-   Un serveur web (optionnel).

### Étapes (UwAmp)

Cloner le dépôt « GitLab » avec l’une des commandes suivantes:

-   **<git@git.dti.crosemont.quebec:1909451/e-boutique-antoine-ali-annesophie.git>
    (SSH).**

-   [**https://git.dti.crosemont.quebec/1909451/e-boutique-antoine-ali-annesophie.git**](https://git.dti.crosemont.quebec/1909451/e-boutique-antoine-ali-annesophie.git)
    **(HTTPS).**

Couper le dossier « **UwAmp** » vers votre « **C:\\** ».

Créer un dossier « **www** » sous « **C:\\UwAmp\\** ».

Déplacer le dossier « **e-boutique-antoine-ali-annesophie **» dans le
dossier www sous « **C:\\UwAmp\\www** ».

***Vous pouvez maintenant démarrer UwAmp et cliquer sur le bouton
« Browser www »***

### Étapes (non UwAmp)

Cloner le dépôt « GitLab » avec l’une des commandes suivantes dans votre
serveur web:

-   **<git@git.dti.crosemont.quebec:1909451/e-boutique-antoine-ali-annesophie.git>
    (SSH).**

-   [**https://git.dti.crosemont.quebec/1909451/e-boutique-antoine-ali-annesophie.git**](https://git.dti.crosemont.quebec/1909451/e-boutique-antoine-ali-annesophie.git)
    **(HTTPS).**

Supprimer le dossier « **UwAmp »**

Mettre comme « Document Root » :
« {DOCUMENTPATH}/e-boutique-antoine-ali-annesophie/app/public/ »

## Sécurité du site web

### Authentification et autorisation

Les utilisateurs doivent s’authentifier dans le site-web pour effectuer
des actions tels qu’acheter des produits, mettre des commentaires ou
encore accéder à certaines pages effectuant des tâches dans le serveur.
Les mots de passe sont encryptés avec l’algorithme BCRYPT et la fonction
PHP « password_hash » lors de la création du compte utilisateur dans le
contrôleur « RegisterController » et sont sauvegardés dans la base de
données.

Les utilisateurs ayant oubliés leur mot de passe peuvent faire la
demande de réinitialisation du mot de passe via la page d’oublie du mot
de passe. Un courriel est envoyé au courriel lié au compte avec un code
à 9 chiffres

*Pour l’instant, tous les types d’utilisateur doivent se connecter avec
**uniquement** un mot de passe mais dans le future proche, une méthode
de double identification avec le numéro de téléphone et/ou le courriel
va être ajouté.*

### Injections SQL & Pratiques

Pour des mesures de sécurités, toutes les requêtes SQL sont préparées
avec les « PDOStatement » du langage PHP dans les « DAOs ». Dans la
plupart des cas, les contrôleurs **n’interagissent pas** avec la base de
données, cette pratique est pour éviter de mettre des requêtes SQL
partout dans le code et les centralisées dans un type de fichier.

Dans quelques contrôleurs, nous trouvons des requêtes SQL puisqu’elles
sont utilisées pour générer des données qui ne sont pas dans modèles
mais plus des statistiques.

Avant d’effectuer quelconque requête SQL, la fonction ou le script doit
préparer la requête avec la méthode « *prepare() »* puis utiliser la
méthode « *bindParam() »* pour inclure les valeurs fournies par
l’utilisateur. Cette manière sécurise la requête SQL et les données
utilisateur.

*Dans un future proche, toutes ses fonctions vont être centralisé dans
un ou des fichiers sous le dossier « database » ou « statistiques » pour
mieux appliquer la méthode de centralisation des requêtes SQL.*

### Attaques XSS

#### CSP (Content-Security-Policy)

Le site web implémente la sécurité CSP pour éviter des attaques XSS et
de surfaces grâce à la restriction des sources des différentes
ressources utilisées par les pages. Les pages doivent initialiser un
objet de type « CSP » et rajouter les différentes sources des ressources
et exécuter le CSP pour construire le « *Content-Security-Policy *« tag
et le rajouter dans le « header ». L’objet gère les styles et les
scripts.

*Pour l’instant, cette sécurité est incluse **UNIQUEMENT** dans le
module utilisateur normal. Il va être déployé dans le centre
administratif et marchand bientôt.*

#### Validation et sanitisation des entrées

Pour renforcer la protection contre les attaques XSS, toutes les entrées
utilisateur sont traitées pour s’assurer qu’elles sont valides. Une
validation est effectuée au niveau de la surface avec du JavaScript et
les tags HTML puis lors de la réception de la requête au niveau du
serveur avec les différentes méthodes PHP. Plusieurs méthodes de
sanitisation et de vérification des données sont utilisées, voici la
liste : *« filter_input », « date_parse », « preg_replace »,
« checkdate » et « preg_match ».*

Prendre note que le mot de passe entrée est vérifié pour s’assurer que
les normes de sécurités sont bien respectées mais n’est pas traité par
la fonction « filter\_*input* » ni « *preg_replace* » pour éviter dans
faire la modification.

### Gestion des erreurs et journalisation

Pour l’instant, la gestion des erreurs se fait avec la renvoie de code
erreur utilisant une messagerie server/session. Les différentes pages
pouvant générer des erreurs à cause d’une mauvaise entrée utilisateur ou
le système vont être rendue par les pages sources **ou** la page
« errorForm.php ». Dans un future proche, certaines erreurs vont être
sauvegardées dans la base de données pour que les administrateurs
puissent régler certains problèmes liés au code.

### Mesures contre le CSRF (Cross-Site Request Forgery)

Lors de l’authentification de l’utilisateur, un jeton CSRF est généré
dans la session sous la variable « csrf ». Cette valeur est transmise
dans chaque formulaire manipulant des données au niveau de la base de
données. Cela évite les attaques CSRF puis sécurise les formulaires
modifiant des données sensibles. Pour une meilleure sécurité, le jeton
csrf est regénéré après chaque requête et est stocké dans la session.

Un fichier « **CSRF.php** » fournit les méthodes
« ***session_generate_token()** »* et *« **session_match_tokens(string
$token)*** » pour faire la gestion des jetons et globaliser le code.

Prendre note que cette mesure de sécurité a été déployé avec succès dans
tous les formulaires du site-web et est maintenant rendu **une mesure à
rajouter systémiquement dans les formulaires**.

### hCaptcha

Le site web implémente hCaptacha pour vérifier l’identité des
utilisateurs et valider qu’ils sont bien humains, cela créer aussi un
type de frein au appels API pour la création de nouveaux comptes
utilisateurs. Ce formulaire va probablement être aussi utilisé pour les
authentifications.

## Fonctionnalités

## Problèmes

-   Plusieurs versions d’une même librairie utilisées dans le site, à
    normaliser.

-   Les tags \<html> ne sont pas bien distribués dans les pages.

-   Faire la révision des déclarations de classes dans les divs
    (col-md-2 sans class= «»).

-   Les pages n’implémentent pas la sécurisation des données
    (htmlspecialchar)

-   Les « card » pour les produits ne sont pas uniformes.

-   Les pages de connexion, inscription et inscription marchande doivent
    être révisé.

## Diagrammes

### Modélisation de la base de données

<img src="https://github.com/CupidonSauce173/shopnest/blob/main/diagrammes/database.png" style="width:7.5in;height:8.38958in" />

### Cas d’utilisation (utilisateur)

<img src="https://github.com/CupidonSauce173/shopnest/blob/main/diagrammes/cas_utilisateurs.png" style="width:7.5in;height:5.93681in" />

### Cas d’utilisation (administrateur)

<img src="https://github.com/CupidonSauce173/shopnest/blob/main/diagrammes/cas_administrateur.png" style="width:7.5in;height:5.00764in" />

### Cas d’utilisation (marchand)

<img src="https://github.com/CupidonSauce173/shopnest/blob/main/diagrammes/cas_marchand.png" style="width:7.5in;height:4.55347in" />

## Sources

[Content-Security-Policy (CSP) Header Quick
Reference](https://content-security-policy.com/)

[Cross Site Request Forgery (CSRF) \| OWASP
Foundation](https://owasp.org/www-community/attacks/csrf)

[Cross-Site Request Forgery Prevention - OWASP Cheat Sheet
Series](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html)

[PHP CSRF
(phptutorial.net)](https://www.phptutorial.net/php-tutorial/php-csrf/)

[Cross Site Scripting Prevention - OWASP Cheat Sheet
Series](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html)
