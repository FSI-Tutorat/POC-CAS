# CAS dans un environnement de développement
## Présentation du POC
Ce POC a pour vocation de montrer comment accéder à un serveur d'authentification CAS à partir d'un poste de développement.

Le développement d'une application CAS se repose en grande partie sur la connexion à celui-ci. Mais elle n'est possible que depuis des serveurs autorisés. A Paul Sabatier le TLD du DNS doit absolument être ".univ-tlse3.fr" pour que le serveur autorise l'utilisation de leurs services.

Une fois que vous êtes connecté au CAS de votre université, la page vous redirige automatiquement vers une page basique.

Le repo contient un projet déjà préparé pour se connecter au serveur d'authentification CAS de l'université Paul Sabatier. De plus, le lien du test n'est valable que si vous essayez de vous connecter aux serveurs d'identification de Paul Sabatier.

### Changement du serveur d'authentification CAS
Si vous voulez changer le serveur de vérification CAS il vous faudra allez modifier le fichier __.env__ se trouvant à la racine du projet. La variable à modifier est celle nommée "CAS_HOST".
```
###> l3/cas-guard-bundle ###

CAS_HOST=preprod-cas.univ-tlse3.fr # Cas Server

CAS_PATH=cas                        # App path if not in root (eg. cas.test.com/cas)

CAS_PORT=443                         # Server port

CAS_HANDLE_LOGOUT_REQUEST=false       # Single sign out activation (default: false)

CAS_LOGIN_TARGET=https://127.0.0.1:8000 # Redirect path after login (when use anonymous mode)

CAS_LOGOUT_TARGET=https://127.0.0.1:8000  # Redirect path after logout

CAS_CA=false                         # SSL Certificate

CAS_FORCE=true                       # Allows cas check mode and not force, user : __NO_USER__ if not connected (If force false, Single sign out cant work).

CAS_GATEWAY=true             # Gateway mode (for use the mode gateway of the Cas Server) set to false if you use micro-services or apis rest.

###< l3/cas-guard-bundle ###
```

### From scratch
Si vous voulez partir d'un projet neuf vous pouvez en créer un avec la commande ci dessous puis suivre les étape d'installation du [CAS Guard Bundle](https://github.com/l3-team/CasGuardBundle), qui est le bundle nous permettant d'entreprendre la connexion avec ce type de serveur.
```
> symfony new --web-app NomDeVotreApp
```

> N'oubliez pas de vous __cd__ dans le dossier avant de faire d'autres commandes concernant le projet.

## Prérequis
- PHP 8.1.x : le moteur PHP
- Composer : un outil de gestion de dépendances pour PHP.
- Symfony cli : pour la commande Symfony en ligne de commande

## Mise en place du site web (environnement de développement)

Cette procédure est...longue (10 minutes ?)...les dépendances vont être récupérées depuis les dépôts.

1) Installer les dépendances vers des librairies tierces :  

```

composer install

  

> Loading composer repositories with package information

> Restricting packages listed in "symfony/symfony" to "6.0.*"

> ... fais des trucs ...

> Executing script cache:clear [OK]

> Executing script assets:install public [OK]

```

Seulement nécessaire lors de la mise en place de l'environnement de développement.

(Sauf si de nouveaux packages sont rajouter au fur et a mesure du développement)


## Installation et manipulations importantes
### Modification du fichier : hosts
La modification de ce fichier permet à l'ordinateur de rediriger lui-même un DNS vers une adresse IP sans utiliser de (serveur?) proxy.

> Nous avons essayer avec les proxy de Symfony, mais nous avons rencontré des difficultés ; dès lors que nous nous redirigeons vers un site ayant le même TLD que celui que nous avions assigner â notre application, Symfony chercher un projet qui aurait pu être raccorder à ce schéma de DNS : "*.univ-tlse3.fr"

Les serveurs d'identification CAS de notre université (Paul Sabatier) n'acceptent que les requêtes venant de serveurs possédant le TLD : ".univ-tlse3.fr". L'étape suivante est clé pour que nous puissions accéder à la page de connexion CAS.

Pour tous les systèmes il faut commenter la ligne qui redirige localhost par une ligne qui redirigera avec un TLD qui sera supporté par le serveur d'identification CAS.

##### Windows
Localisation : C:\Windows\System32\drivers\etc
```
# 127.0.0.1 localhost
# ::1 localhost

127.0.0.1 xxxx.univ-tlse3.fr
```

##### Linux / Mac
Localisation : /etc/hosts
```
# 127.0.0.1 localhost

127.0.0.1 xxxx.univ-tlse3.fr
```


### Création du projet et lancement de l'application
On crée le projet et on s'y déplace.
```
> git clone https://github.com/FSI-Tutorat/POC-CAS.git

> cd POC-CAS
```

On installe ensuite toutes les dépendances du projet.
```
> composer install

> composer update
```

On utilisera ensuite une commande nous permettant d'initier un authentificateur de certificat SSL nous permettant d'accéder à notre application web de manière "sécurisée". Cette étape est nécessaire pour que les serveurs d'authentification CAS nous laissent avoir accès à leurs services de connexion.
```
> symfony server:ca:install
```

Il est important de savoir que lorsque les services d'identification CAS ne peuvent renvoyer que sur les ports classiques HTTP. C'est pour cela que nous initions le port du serveur à 443.
```
> symfony server:start --port=443
```


### Test
> N'oubliez pas de modifier le _xxxx_ pour qu'il comporte la même chose que dans votre fichier hosts !

Le premier lien est pour le cas où vous partiez d'une nouvelle application, tandis que le second et si vous avez fait le git clone.

Allez sur l'un des liens suivant :

- https://preprod-cas.univ-tlse3.fr/cas/login?service=https%3A%2F%2Fxxxx.univ-tlse3.fr%2Flogin%2Findex.php1
Si après vous être connecté vous voyez la page de bases des nouvelles applications de Symfony, c'est tout bon!

- xxxx.univ-tlse3.fr
Si une fois connecté vous arrivez sur une page vous disant "Hello! ✅", vous avez réussi!
