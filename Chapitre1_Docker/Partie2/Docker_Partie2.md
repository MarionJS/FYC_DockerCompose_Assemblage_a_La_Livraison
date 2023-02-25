# Partie 2 - Dockerfile

## Introduction aux Dockerfile

&nbsp;

Docker permet de construire une image Docker d�une application � l�aide de la commande build et d�un Dockerfile. L�image ainsi produite peut �tre utilis�e pour cr�er un conteneur dans un docker host, ce conteneur �tant charg� d�ex�cuter l�application (ou service) contenue dans l�image.

&nbsp;

*Qu�est-ce qu�un Dockerfile ?*

Un dockerfile est un fichier texte qui contient toutes les instructions n�cessaires � la construction d�une image Docker.

&nbsp;

*Pourquoi cr�er une image Docker ?*

Une image Docker de votre application est n�cessaire pour cr�er un conteneur qui ex�cutera la dite application dans un environnement Docker.

&nbsp;

*Comment cr�er un Dockerfile ?*

Un Dockerfile peut �tre cr�� avec n�importe quel �diteur de texte. Pour ce faire, il suffit de cr�er un nouveau fichier nomm� � Dockerfile � sans ajouter d�extension (il peut �tre n�cessaire de retirer l�extension � .txt �).

&nbsp;

Il est � noter qu�il n�est pas possible de renommer un fichier Dockerfile : tous les fichiers Dockerfile sans exception doivent imp�rativement se nommer : � Dockerfile �. Ce qui signifie qu�il n�est pas possible d�avoir plus d�un Dockerfile par dossier !

Nous explorerons dans la prochaine section la structure et les instructions d�un Dockerfile.

&nbsp;

&nbsp;

## Le fichier Dockerfile

&nbsp;

Comme expliqu� dans la section pr�c�dente, un Dockerfile est un fichier texte comportant une s�rie d�instructions. Ces instructions ressemble � s'y m�prendre � des "commandes".

N�anmoins, les instructions utilis�es dans les Dockerfile sont sp�cifiques � ces derniers et ne doivent pas �tre confondus avec les commandes de la CLI (command line interface) de Docker.

&nbsp;

### 1) Syntaxe

&nbsp;

La syntaxe d�un fichier Dockerfile se veut � la fois simple et accessible, elle ob�it n�anmoins � des r�gles qui lui sont propre :

- Il ne peut pas y avoir 2 instructions Dockerfile sur une m�me ligne.

- Chaque instruction s�ex�cute de fa�on ind�pendante.

&nbsp;

La syntaxe des instructions au sein d�un Dockerfile ressemble � ce que l�on peut trouver dans un script de commande classique :

``INSTRUCTION <argument1> <argument2> �``
 
&nbsp;

Un exemple avec l'instruction COPY :

``COPY aspnetapp/*.csproj ./aspnetapp/ ``
 
&nbsp;

Nous reviendrons sur les d�tails du fonctionnement de cette instruction dans la prochaine section.

&nbsp;

Bien que cela ne soit pas obligatoire, la convention veut que le nom des instructions d�un Dockerfile soit �crit en majuscule.

&nbsp;

Pour �crire un commentaire dans un Dockerfile, il faut commencer une ligne avec le caract�re � # �.

&nbsp; 

Exemple :

``# Commentaire dans un dockerfile``
 
Il n�est pas possible de mettre un commentaire et une instruction sur une m�me ligne.

&nbsp;

Certaines instructions peuvent �tre �crites selon deux formes diff�rentes, c�est le cas pour CMD, ENTRYPOINT et RUN. Ces deux formes sont les suivantes :

&nbsp;

la forme � shell �. elle permet d�ex�cuter une instruction avec le shell du container.

Syntaxe : ``Instruction param1 param2 � ``

&nbsp;

la forme � exec �. elle permet d�ex�cuter une commande directement, sans passer par le shell. Dans cette forme, les �l�ments de la commande sont parser dans un tableau JSON. C�est la forme privil�gi� pour les instructions CMD et ENTRYPOINT.

Syntaxe : ``Instruction [� param1 �, � param2 �, � ]``

&nbsp;

### 2) Premi�res instructions

&nbsp;

Voici maintenant quelques explications sur les instructions indispensables � la cr�ation de toute image docker.

&nbsp;

#### FROM :
L'instruction ``FROM`` permet de sp�cifier l�image de base du Dockerfile. En r�alit�, la premi�re instruction d�un Dockerfile est toujours une instruction ``FROM``.

Cette instruction nous permet de construire notre nouvelle image autour d'un environnement conteneurisable, avec �ventuellement le runtime n�cessaire � l�ex�cution de notre application.

&nbsp;

Elle prend la forme suivante :

``FROM [--platform=<platform>] <image>[:<tag>] [AS <name>]``

&nbsp;

``<platform>``  permet de sp�cifier la plateforme vis�e pour instancier l�image, g�n�ralement cette option est omise.

``<image>`` c�est le nom de l�image qui doit service d�image de base pour notre Dockerfile

``<tag>`` permet de cibl� une version particuli�re de l�image. Si aucun tag n�est sp�cifi�, c�est la version la plus r�cente qui est r�cup�r�e.

``<name>`` est utilis� pour donner un nom � cette image de base. C'est indispensable si vous souhaiter interagir avec cette image � partir d'une autre image de base, ce que nous verrons plus loin dans ce chapitre.

&nbsp;

Voici quelques exemples :

- Si je souhaites conteneuriser une application PHP 7 je peux par exemple �crire l�instruction suivante au d�but de mon Dockerfile :

	```
	FROM php:7.0-apache
	```

	Je r�cup�re ainsi une image qui nous permettra d�ex�cuter une application PHP 7 avec serveur apache.

&nbsp; 

- Si je souhaite conteneuriser une application .NET 6, je peux utiliser l'instruction suivante :

	```
	FROM mcr.microsoft.com/dotnet/aspnet:6.0
	```

	J�aurais ainsi dans mon image un environnement conteneurisable permettant d�ex�cuter une application .NET 6

&nbsp;

#### COPY :
L'instruction ``COPY`` est une instruction tr�s simple et n�anmoins indispensable pour conteneuriser une application.

Elle permet de copier dans la nouvelle image tous les fichiers/dossiers n�cessaires au fonctionnement de l�application que vous souhaitez conteneuriser. Les �l�ments ainsi copi�s seront ajout�s au syst�me de fichier du conteneur cr�er � partir de la nouvelle image.

&nbsp;

Elle prend la forme suivante :

```
COPY [--chown=<user>:<group>] <source> ... <destination>
```

&nbsp;

``<source>`` est un emplacement du syst�me de fichier de votre machine contenant les fichiers � copier.

``<destination>`` est un emplacement dans le syst�me de fichier du futur conteneur.

Si un chemin comporte des espaces ou autres caract�res blancs, il faut penser � l�entourer de guillemets. xemple :

``"C:\source\mon projet"`` 

&nbsp; 

Il est possible de sp�cifier un utilisateur ou un groupe qui auraient des droits sp�cifiques sur les �l�ments � copier, ces droits s�appliquant dans le syst�me de fichier du futur conteneur.

&nbsp;

Voici un exemple d�utilisation :

- On souhaite copier le contenu du dossier *aspnetapp* dans un dossier *aspnetapp* pr�sent sur le conteneur.

	```
	COPY aspnetapp/* ./aspnetapp/
	``` 

	Ici tous les fichiers contenus dans le dossier � aspnetapp � (qui doit se trouver dans le m�me dossier que le dockerfile pr�sent sur votre machine), seront copi�s dans un nouveau dossier nomm� � aspnetapp � et situ� � la racine du syst�me de fichier du futur conteneur.

&nbsp;

#### ENTRYPOINT :

Cette instruction permet de changer notre conteneur en un conteneur ex�cutable. En effet il permet de sp�cifier la commande qui sera ex�cut� lors du d�marrage du conteneur.

Comme expliqu� pr�c�demment, l�instruction ``ENTRYPOINT`` peut prendre deux formes : la forme � shell � ou la forme � exec �. Ici nous utiliserons la forme � exec �, car c�est la forme privil�gi�e pour cette instruction.

&nbsp; 

L�instruction ``ENTRYPOINT`` prend la forme suivante :

``ENTRYPOINT ["executable", "param1", "param2"]``

&nbsp;

Voici un exemple d�utilisation :

```
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

&nbsp;

### 3) Exemple d�un Dockerfile minimaliste

&nbsp;

Les instructions pr�sent�es dans cette section permettent de cr�er une image de notre application en quelques instructions :

```
FROM mcr.microsoft.com/dotnet/aspnet:6.0 AS runtime
COPY published/* ./app
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```
 
&nbsp;

Dans cet exemple, une image du runtime ASP.NET 6 (librairie permettant de cr�er des applications web en C#) est utilis�e comme image de base pour notre nouvelle image. Puis le contenu du dossier � published � (pr�sent sur la machine h�te) est copi� dans un nouveau dossier nomm� � app � directement dans le syst�me de fichier du futur conteneur. Enfin la commande � dotnet aspnetapp.dll � sp�cifi�e � l'aide de l'instruction ``ENTRYPOINT`` permettra d�ex�cuter notre application au d�marrage du conteneur.

&nbsp;

Nous verrons dans la prochaine section le syst�me de couche des images docker, le cache de Docker, et comment optimiser les images.

&nbsp;

&nbsp;

## Optimisation des fichiers Dockerfile

&nbsp;

### 1) Le cache de Docker et le syst�me en couche

&nbsp;

Avant d�aller plus dans l�exploration des instructions disponibles dans les Dockerfile, il convient de prendre un peu de temps pour expliquer une partie du fonctionnement de la construction des images Docker, et notamment le syst�me de cache (build cache).

Docker est r�put� pour son efficacit� � sa simplicit� d�utilisation pour construire des images.

&nbsp;

Comment Docker peut-il �tre aussi performant pour la cr�ation des images ?

&nbsp;

Docker a �t� con�u avec un syst�me de cache permettant de rendre la construction d�image aussi rapide que possible. Le syst�me de cache permet de cr�er des �tapes interm�diaires, appel�es � couche �, qui pourrons �tre r�utilis�es le cas �ch�ant pour construire de futures images. En r�alit�, chaque instruction pr�sente dans un Dockerfile va g�n�rer une couche qui sera enregistr�e dans le cache durant la construction de l'image.

Chaque couche cr��e pendant "le build" de l'image est immutable : elle ne peut pas �tre modifi�e par les couches suivantes. Chaque couche �tant sauvegard�e dans le cache, elle poss�de un identifiant unique. Si Docker a besoin de l�une d�entre elle pour construire une nouvelle image, il peut l�utiliser directement sans se soucier d�un probl�me de compatibilit�. Au final, chaque image constitue un empilement de plusieurs couches.

&nbsp;

Il est �galement possible de cr�er des images sans utiliser le cache de Docker, en ajoutant l�option ``--no-cache=true`` avec la commande � docker build �

&nbsp;

### 2) Optimisation des images :

&nbsp;

Comment optimiser la construction des images Docker ?

&nbsp;

Une premi�re piste peut consister � construire un fichier Dockerfile le plus simple qui soit, dans la mesure du possible, tant que celle-ci produit une image fonctionnelle. Aussi il convient de ne pas alourdir l�image inutilement en copiant dans l�image, � l�aide de l�instruction ``COPY``, des fichiers qui ne seront jamais utilis�s.

Il faut �galement prendre en compte la m�canique du cache de Docker qui rentre en jeu lors de la cr�ation d�une image : si une instruction ex�cut�e entraine des modifications par rapport � la couche associ�e (pr�sente dans le cache), cette couche est reg�n�r�e.

&nbsp;

<u>Exemple 1 :</u> vous modifiez un fichier dans votre projet � program.cs �. Dans ce cas l�instruction ``COPY`` qui permet de copier le contenu du projet dans l�image est r�ex�cut�e, elle ne peut pas s�appuyer sur le cache.

<u>Exemple 2 :</u> vous utilisez l'instruction ``RUN apt-get update && apt-get install`` pour votre future conteneur, et vous modifiez la commande en y ajoutant un nouvel argument � -y �. La commande a chang�, le syst�me de cache ne peut pas �tre utilis� pour cette couche.

&nbsp;

La modification d�une couche entraine le r�g�n�ration de toutes les couches suivantes !

&nbsp;

Pour cette raison, on mettra d�abord en d�but de fichier les instructions qui vont configurer l�environnement du conteneur, notamment avec l�utilisation de l'instruction ``RUN`` ou l'instruction ``ENV`` (ces deux instructions seront pr�sent�es dans la section suivante). En effet ces instructions �tant moins susceptibles d��tre modifi�es d�une construction d�image � une autre, les couches existantes dans le cache pourront �tre r�utilis�es.

De la m�me mani�re, on pr�f�ra mettre � la fin du fichier les instructions qui copient des fichiers dans l�image, afin de limiter le nombre de couche qu�il faudra reg�n�rer � la prochaine construction d�image.

&nbsp; 

Attention, certaines couches peuvent sembler ne pas avoir besoin d��tre r�g�n�r�es, notamment les instructions li�es � la mise � jour et la r�cup�ration de paquets. N�anmoins cela peut constituer un pi�ge dans la conception d�image Docker.

L'instruction pr�sent�e pr�c�demment : ``RUN apt-get update && apt-get install`` va cr�er une image avec l�installation des paquets et leurs mise � jour comme r�sultat.

N�anmoins dans les constructions d�image futur, comme l�instruction associ�e n�a pas g�n�r� de modification par rapport � la couche existante dans le cache, celle-ci est r�utilis�e. Par cons�quent, l�installation des paquets et des mise � jours n�est pas actualis�e, elle correspond aux op�rations effectu�es lors de la premi�re g�n�ration de l�image, lorsque la couche associ�e � l�instruction RUN a �t� mise en cache !

&nbsp;

Si l�on souhaite n�anmoins reg�n�rer cette couche � chaque construction d�image, pour s�assurer que les paquets soient bien install�s dans leurs derni�res versions, il est possible de contourner le probl�me de 3 fa�ons :

- Placer l�instruction ``RUN`` de fa�on � ce que la couche associ�e soit syst�matiquement reg�n�r�e (tr�s probable si elle est plac�e apr�s une instruction ``COPY``).

- Utiliser l�option ``�no-cache`` lors de la construction de l�image avec la commande ``docker build``, comme pr�sent�e pr�c�demment.

- Utiliser la commande ``docker builder prune`` pour vider le cache de Docker.

&nbsp;

Maintenant que nous en savons d�avantage sur le fonctionnement interne de Docker, nous pouvons voir d�autres instructions qui nous permettrons d�apporter plus de flexibilit� � nos Dockerfile.

&nbsp;

&nbsp;

## Plus d�instructions pour les Dockerfiles

&nbsp;

Nous avons d�j� vu dans la premi�re section les instructions ``FROM``, ``COPY`` et ``ENTRYPOINT``. C�est un bon d�but mais ces instructions offrent un nombre de possibilit�s limit�es pour nos DockerFile. Comment ex�cuter une commande shell dans notre conteneur par exemple ?

Dans la section pr�c�dente, nous avons vu un aper�u de la commande ``RUN`` qui permet de r�pondre � ce besoin. Nous avons �galement �voquer la commande ``ENV`` qui permet de configurer l�environnement du futur conteneur.

&nbsp;

Voyons en d�tail la syntaxe de ces instructions, et d�autres encore, afin d�exploiter tout le potentiel des DockerFile.

&nbsp;

#### RUN :

L'instruction ``RUN`` peut utiliser la forme � shell � et la forme � exec �.

&nbsp;

<u>Forme � shell � :</u>

```
RUN <command>
```

&nbsp;

<u>Forme � exec � :</u>

```
RUN ["executable", "param1", "param2"]
``` 

&nbsp;

On utilise l'instruction ``RUN`` pour ex�cuter des commandes dans le shell disponible � l'int�rieur de l�image Docker. Il faut bien comprendre que les commandes sont ex�cut�es <u>au moment o� l'on construit l�image</u>, et non au d�marrage du conteneur.

&nbsp; 

Voici un exemple d�utilisation :

```
RUN ["/bin/bash", "-c", "echo hello"]
```

&nbsp;

Il existe des options sp�cifiques � l�instruction ``RUN`` qu�il vous faudra explorer directement dans la [r�f�rence des Dockerfile](https://docs.docker.com/engine/reference/builder/#run).

&nbsp;

#### CMD :

Comme l'instruction ``RUN``, l'instruction ``CMD`` peut utiliser les deux formes de commande.

&nbsp;

Forme � shell � : 
```
CMD command param1 param2
```
&nbsp;

Forme � exec � : 
```
CMD ["executable","param1","param2"]
```
&nbsp;

La particularit� de l�instruction ``CMD`` est qu�il ne peut n'y en avoir qu�une seule par Dockerfile. Il y a une raison � cela : L�instruction ``CMD`` est utilis�e pour fournir des arguments par d�faut � prendre en compte lors du d�marrage du conteneur. Elle peut �galement sp�cifier la commande utilis�e lors du d�marrage. Dans le cas contraire, il est n�cessaire de rajouter � la suite une instruction ``ENTRYPOINT`` qui permettra de sp�cifier l�ex�cutable � d�marrer, l�instruction ``CMD`` est utilis�e dans ce cas uniquement pour passer des arguments � l�instruction ``ENTRYPOINT``.
 
&nbsp;

Voici un exemple d�utilisation :
```
CMD ["/usr/bin/wc","--help"]
``` 

&nbsp;

#### WORKDIR :

L�instruction ``WORKDIR`` est plut�t simple d�utilisation. Elle est utilis�e pour d�finir le r�pertoire de travail au sein de l'image � construire pour les instructions Dockerfile suivantes. Les instructions directement impact�es par l�utilisation de l�instruction ``WORKDIR`` sont ``RUN``, ``CMD``, ``ENTRYPOINT``, ``COPY`` et ``ADD``.

&nbsp; 

L�instruction ``WORKDIR`` prend la forme suivante :
```
WORKDIR /path/to/workdir
```

&nbsp;

Voici un exemple d�utilisation :
```
WORKDIR /src
```

&nbsp;

#### ENV :

L�instruction ``ENV`` permet de d�clarer une variable d�environnement � l�int�rieur de l�image et de lui affecter une valeur. Bien entendu, cette variable pourra �tre utilis�e par les instructions suivantes.

&nbsp; 

L�instruction ``ENV`` prend la forme suivante :
```
ENV <key>=<value>
```

&nbsp;

Voici un exemple d�utilisation :
```
ENV APP_NAME="MyApp"
```

&nbsp;

Il est possible de cr�er plusieurs variables avec une m�me instruction ``ENV``.

Il existe �galement une syntaxe alternative qui omet le signe � = �. Cette approche n�est pas recommand�e, elle rend l�instruction moins lisible et ne permet pas de cr�er plus d�une variable par instruction ``ENV``.

&nbsp;

#### EXPOSE :

L�instruction ``EXPOSE`` permet de d�finir un port d��coute pour le futur conteneur. Il est possible de sp�cifier le protocole pour l��coute de port (TCP ou UDP) par d�faut la valeur choisie est TCP.

Cette instruction ne rend pas le port du conteneur accessible pour autant. En r�alit�, cette instruction sert plut�t de documentation pour la personne qui va mettre en route le conteneur. C�est l�option -p de la commande � docker run � qui permettra d�exposer le port du conteneur.

&nbsp;

L�instruction ``EXPOSE`` prend la forme suivante :
```
EXPOSE <port>[<port>/<protocol>...]
```

&nbsp;

Voici un exemple d�utilisation :
```
EXPOSE 80/tcp
```

&nbsp;

#### VOLUME :

L�instruction ``VOLUME`` permet de sp�cifier un point de montage pour un volume qui sera marqu� comme un volume mont� en externe depuis l�h�te du conteneur ou depuis un autre conteneur.

&nbsp;

L�instruction ``VOLUME`` peut prendre la forme suivante :
```
VOLUME ["/volume-path"]
```
Mais aussi la forme :
```
VOLUME /volume-path
```

&nbsp;

Voici un exemple d�utilisation :
```
VOLUME /myvol
```

&nbsp;

Il est possible de cr�er plusieurs volumes avec une seule instruction ``VOLUME``.

&nbsp;

Lorsque le conteneur est cr�� � l�aide de la commande ``docker run``, le ou les volume(s) sont cr��s par la m�me occasion, avec comme contenu le r�pertoire accessible � leur emplacement respectif.

&nbsp;

#### ADD :

L�instruction ``ADD`` est similaire � l�instruction ``COPY``, mais elle offre des fonctionnalit�s suppl�mentaires (par ailleurs cette instruction est plus ancienne que l'instruction ``COPY`` !). Pourquoi avoir deux instructions qui fonctionnent de la mani�re ? Eh bien car l�une est bien plus fournie en fonctionnalit�, mais aussi plus complexe. L� o� l�instruction ``COPY`` ne peut que copier des fichiers d�un emplacement de l�h�te vers l�image, l�instruction ``ADD`` a en plus la possibilit� :

- De r�cup�rer des fichiers depuis une URL pour les ajouter � une image.

- De d�zipper une archive et placer son contenu dans la nouvelle image.

&nbsp;

On voit ici l�int�r�t que peut apporter l'instruction ``COPY`` sur l'instruction ``ADD`` : si l�on souhaite copier une archive dans l�image, alors on choisira ``COPY``. Au contraire si l�on souhaite d�zipper et copier le contenu de l'archive dans l�image, alors on choisira l�instruction ``ADD``.

&nbsp;

L�instruction ``ADD`` prend la forme suivante :
```
ADD [--chown=<user>:<group>] ["<source>",... "<destination>"]
```

&nbsp;

Comme pour la l�instruction ``COPY`` nous avons les arguments � source � et � destination � � sp�cifier.

``<source>`` est un emplacement du syst�me qui comporte les fichiers � copier.

``<destination>`` est un emplacement dans le syst�me de fichier du futur conteneur.

&nbsp; 

Et comme pour l�instruction ``COPY`` Il est possible de sp�cifier un utilisateur ou un groupe qui aurait des droits sp�cifiques sur les �l�ments � copier, les droits s�appliquant dans le syst�me de fichier du futur conteneur.

&nbsp;

Voici un exemple d�utilisation :
```
ADD home/* /mydir/ 
```

&nbsp;

Nous venons de voir beaucoup de nouvelles instructions, et avec celles-ci vous devriez �tre en mesure de cr�er toutes les images Docker n�cessaires au d�veloppement, aux tests et � la mise en production. Toutefois, n�h�sitez pas � consulter la documentation pour approndir vos connaissances sur Dockerfile, et les quelques instructions qui n�auront pas �t� pr�sent�es ici.

&nbsp;

Il est temps d�sormais de pratiquer ! Dans la prochaine section nous r�aliserons un exercice guid� sur la cr�ation d�un Dockerfile et la cr�ation d�un conteneur � partir d�une image Docker, elle-m�me g�n�r�e gr�ce � notre Dockerfile.