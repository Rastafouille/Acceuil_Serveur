# ROS
## Installation
<http://www.instructables.com/id/Ros-Indigo-install-on-Ubuntu-1/>

j'ai enregistrer lenvironnement dans Bashrc, mais a chaque shell
`source ~/.bashrc`

After you put the who rplidar_ros into the catkin_ws and compile, you will need to do one command to source the folder
`source ~/catkin_ws/devel/setup.bash`

Sourcer :

    $ source devel/setup.bash

## ROSBASH Generation Robots

`git clone git@dev.humarobotics.com:rosbash`
modifier `/home/"utilisateur"/.bashrc`
ajouter: `source ~/rosbash/rosbash.bash`
redémarrer les shells
`devel` pour initialiser
`rkill`, `rosrun`, `roslaunch`

## Topics
Lors de la création d'un *Publisher* ou *Subscriber*, il est possible (et même vivement conseillé depuis ROS Indigo) de spécifier la taille de la file (*queue*) avec le paramètre `queue_size`. Dans le cas du *Publisher*, cela dépend des latences de communication entre les noeuds, alors que pour le *Subscriber*, cela dépend du temps de traitement lors de la réception d'un message. Une file trop grande pour le *Subscriber* peut engendrer des retards dans le traitement, mais garantit de traiter tous les messages. Dans le cas de capteurs publiant régulièrement leur état, une petite taille de file (1) paraît suffisant car garantit de toujours obtenir la dernière mesure. 

### Fréquence et bande passante d'un topic
La fréquence de publication d'un topic (messages par seconde) peut être affichée avec la commande :

		$ rostopic hz topic-name

De même, la bande passante utilisée (en octets par seconde) est calculée avec la commande :

		$ rostopic bw topic-name

## Autres ROS
### Vérification de problèmes
La commande `roswtf` effectue tout un tas de tests pour vérifier la présence d'éventuels problèmes.

## Services
### Ligne de commande
- Liste des services

        $ rosservice list

- Informations sur un service :

        $ rosservice info /turtle1/teleport_absolute

- Appel d'un service sans arguments :

        $ rosservice call /clear

- Appel d'un service avec arguments :

        $ rosservice call /turtle1/teleport_absolute 5 5 0

### Python
Sans arguments :

    from std_srvs.srv import Empty
    rospy.wait_for_service("/clear")
    clear = rospy.ServiceProxy("/clear", Empty)
    clear()

Avec des arguments :

    from turtlesim.srv import TeleportAbsolute
    rospy.wait_for_service("/turtle1/teleport_absolute")
    teleport = rospy.ServiceProxy("/turtle1/teleport_absolute", TeleportAbsolute)
    teleport(5, 5, 0)
### Créer un service
#### Fichier srv
Créer un fichier `.srv` dans un dossier `srv` à la racine du paquet.

    float32 x
    float32 x
    ---

#### CMakeLists
Il faut ajouter la dépendance à `genmsg` :

    find_package(catkin REQUIRED COMPONENTS
        [...]
        genmsg
    )

Décommenter la partie sur les fichiers de service :

    add_service_files(
      FILES
      Goal.srv
    )

Et la partie de génération des services :

    generate_messages(
       DEPENDENCIES
       geometry_msgs
       std_msgs
    )

#### Python
Il faut commencer par importer notre nouveau service :

    from turtle_goal import Goal, GoalRequest

*Remarque : le `GoalRequest` est généré automatiquement.*

On crée le service à la manière d'un subscriber :

    self.service=rospy.Service("/turtle1/goal", Goal, self.cb_service)

Et maintenant, on implémente la fonction appelée par le service :

    def cb_service(self, req):
        self.go(req.x, req.y)
        return GoalResponse()

On peut alors tester avec :

    rosservice call /turtle1/goal 2 7

Attention, il faut gérer la concurrence des appels aux services nous-même.

## Outils ROS
La gestion des paquets se fait avec `rospack`. Pour mettre à jour l'index des paquets :

    $ rospack profile

Déplacement dans le répertoire d'un paquet :

    $ roscd mon_paquet

CREATION MODELE 

`.sdf` spécifique Gazebo et `.urdf` pour ROS mais Gazebo les gere aussi
créer dossier `tourelle_description`
importer le `.stl` dedans
modifier avec blender, echelle, position, orientation, origine
pour echelle et orientaiton, modifier directement les parametres dans la table de droite
modife origine :
- en bas, edit mode
- touche "C" pour selection
- "shift + S" puis "cursor to selected", le curseur se met sur le barycentre des points selectionnés
- en bas, objet mode
- "origin to 3D cursor"
- mettre position a 0 0 0
modif du zoom, tip "N" et passer le start clip a 0.001 dans la table de droite
file/export/STL pour enregister
si probleme d'affichage dans meshlab : "Normalize Face Normals"

## MODELE DANS GAZEBO 
###URDF
définition des links et joins dans le fichier URDF
visualisation URDF dans RVIZ pour validation de l'assemblage
`roslaunch urdf_tutorial display.launch model:=tourelle.urdf gui:=true`

creer tourelle_gazebo.launch pour charger le world et le .urdf et le controleur (control.launch) :       
Exposer le model dans ROS : creation du fichier control.LAUNCH qui appelle un autre fichier de config .YAML
Ajouter les transmissions dans URDF
Créer les fichiers de contrôle:
- `tourelle_control.yaml`
- `tourelle_control.launch`

### PHANTOM + TOURELLE AVEC XACRO 
xacro pour la generation automatique urdf ou la defetion de macro
il nous faut une xacro pour definir un macro de creation tourelle, une idem pour phantomX et une pour faire un urdf d'assemblage.
On peut faire des xacro de creation urdf pour plusieurs types d'assemblages, Tourelle+Baselink, PhantomX+Base link ou PhantomX+tourelle
pour lancer la generation de l'urdf a partir du xacro : `rosrun xacro xacro phantomx.xacro >phantomx.urdf`

### AJOUT CAPTEURS
dans l'URDF tag gazebo, en 2 parties, Sensor et plugin



###########################################################################################################################

# GIT

Pour le premier
`git init .`
`git add *`
`git commit` (`-a` pour pas faire le `git add *`)
`git remote add origin git@dev.humarobotics.com:tourelle_description` (apres un git clone pas besoin, il crée automatiquement un origin)
`git push origin master`
pour push
    `git add file` si nouveau fichier
    `git commit`
    `git push origin master`
pour pull
`git pull origin master`
`git status` pour voir s'il y a des modifs entre dernier pull ou commit avec tes modifs en cours, et de nouveaux fichiers
`git merge` pour comparer des modifs de ton commit avec un version en cours
`git diff` montrer les modif entre copie de travail et dernier commit
`git log` montre tout l'historique des commit
dossier .git cacher avec tout de stocké
`git checkout xxxxx` pour charger un ancien commit et git chaeckout master pour recharger le dernier commit

## Annuler les modifications non committées

    $ git checkout -f

## Interface graphique
gitk, gitg

## proposer des modifications
forker le repository dans son git depuis le git a modifier
faire les modifs et les pusher dans le repo forker
faire un pullrequest pour proposer la modif

## Fin de lignes
### Réglage
Pour configurer git afin qu'il gère des fin de ligne à la mode UNIX :

    $ git config --global core.autocrlf input

À la mode Windows :

    $ git config --global core.autocrlf true

Source : <https://help.github.com/articles/dealing-with-line-endings/>

### Problème avec scripts Python
Un mauvais réglage des fins de ligne peut conduire à des erreurs lors de l'exécution de scripts Python.
En effet, lorsque l'on lance un script comme un exécutable `./mon_script.py` ou par un fichier launch de ROS, c'est la première ligne qui est lue, le **hashbang** :

    #!/usr/bin/env python

Sous Linux, si le fichier est encodé avec des fins de ligne DOS, le `#` est mal lu, le script ne s'exécute pas et la sortie est la suivante :

    : No such file or directory

Source : <http://stackoverflow.com/questions/19764710/python-script-gives-no-such-file-or-directory>

## Quelques commandes utiles
### Comparer deux branches

    $ git diff master..branche

### Comparer un fichier dans deux branches

    $ git diff branche1 branche2 -- fichier.txt

Source : <http://stackoverflow.com/questions/4099742/how-can-i-compare-files-from-two-different-branches>

### Supprimer une branche :

    $ git branch -d ma_branche

Source : <http://makandracards.com/makandra/621-git-delete-a-branch-local-or-remote>

### Obtenir les branches distantes localement
Suite au clonage d'un répertoire, les branches sont bien rapatriées, mais compresées. Pour lister toutes les branches :

    $ git branch -a

Pour obtenir la version locale d'une branche présente sur `origin` :

    $ git checkout -b ma_branche origin/mas_branche

Source : <http://stackoverflow.com/questions/67699/clone-all-remote-branches-with-git>

## Insertion d'un depot Git dans un nouveau avec conservation de l'historique
Le module Kinematics (repris de Martin) a été développé dans `Documents/gitPython`, puis, il a été déplacé dans le paquet ROS dans le workspace catkin. Le problème, c'est que copier-coller le fichier manuellement supprime tout l'historique git. Pour éviter cela, le principe est d'ajouter l'ancien dépôt comme source du nouveau dans une nouvelle branche puis fusionner cette nouvelle branche avec l'ancienne.

        git remote add other ~/Documents/gitPython
        git fetch other
        git checkout -b newBranch other/master
        git mv Kinematics.py scripts
        git commit -m "Ajout du module Kinematics."
        git checkout master
        git merge newBranch
        git commit
        git remote rm other
        git branch -d newBranch

*Remarque : petit soucis avec un double .gitignore lors du merge.*

Source : <http://stackoverflow.com/questions/1683531/how-to-import-existing-git-repository-into-another>

###########################################################################################################################

# Sublime Text
## Installation
	sudo add-apt-repository ppa:webupd8team/sublime-text-3
	sudo apt-get update
	sudo apt-get install sublime-text-installer

## Package Control
Dans View / Show Console :

	import urllib.request,os,hashlib; 
	h = 'eb2297e1a458f27d836c04bb0cbaf282' + 'd0e7a3098092775ccb37ca9d6b2e4b7d'; 
	pf = 'Package Control.sublime-package'; 
	ipp = sublime.installed_packages_path(); 
	urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); 
	by = urllib.request.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); 
	dh = hashlib.sha256(by).hexdigest(); 
	print('Error validating download (got %s instead of %s), please try manual install' % (dh, h)) if dh != h else open(os.path.join( ipp, pf), 'wb' ).write(by) 

###########################################################################################################################

# MarkDown
 Dans Tools / Command Palette :

	install package
 Puis installer :

	Markdown Preview
	Markdown extended
	Monokai extended

## Conversion Markdown-Html sous Windows
### Avec python-markdown
#### Installation
La distribution Python Anaconda étant installé sur le poste, il est possible d'installer [python-markdown](https://pypi.python.org/pypi/Markdown) en lançant le prompt Anaconda puis :

		pip install markdown

Une fois installée, elle est assez simple d'utilisation et [bien documentée](http://pythonhosted.org//Markdown/index.html).

#### Utilisation
Pour convertir un fichier Markdown en Html, se placer dans le dossier du fichier, maintenir la touche `Maj.` enfoncée en faisant un clic droit. Choisir alors "Ouvrir une fenêtre de commande ici". Il ne reste plus qu'à écrire :

		markdown_py nom_du_fichier_source.md > nom_du_fichier_de_sortie.html

Attention, il y a des soucis d'encodage avec l'unicode. Dans le navigateur, changer l'encodage pour utf-8.

#### Utiliser l'extension TOC
Pour afficher une table des matières, on peut utiliser l'[extension TOC](http://pythonhosted.org//Markdown/extensions/toc.html) incluse dans python-markdown.

Pour cela, il suffit de placer `[TOC]` à l'endroit du fichier où l'on souhaite afficher la table des matières.

Pour générer le fichier Html, il faut alors spécifier l'utilisation de l'extension, dans ce cas, on utilise un autre syntaxe :

		python -m markdown -x markdown.extensions.toc notes_romain.md > notes_romain.html

### Avec grip
Une autre solution est [grip](https://github.com/joeyespo/grip). Il permet de rendre les pages avec le CSS de Github (et de prendre en compte la syntaxe Markdown de Github).

#### Installation
Simplement avec `pip`

		pip install grip

#### Utilisation
Pour exporter le fichier localement dans un fichier Html :

		grip fichier_source.md --export ficher_de_destination.html

## Ascii art dans Sublime Text
Utilisation du plugin [Ascii Decoration](https://github.com/viisual/ASCII-Decorator) disponible dans le Package Control.

## Dictionnaire français dans Sublime Text

- Clôner le dépôt Github : <https://github.com/titoBouzout/Dictionaries>
- Copier les fichiers `French.aff` et `French.dic` dans un nouveau paquet de Sublime Text dans `~/.config/sublime-text-2/Packages/User/Language-French`
- Activer la vérification avec `<F6>` et choisir la langue dans le menu `View -> Dictionnary`.

Source : <http://blog.smarchal.com/correcteur-orthographique-francais-sublime-text/>




###########################################################################################################################

# Serveur Apache2

###########################################################################################################################

# SSH

## Connexion SSH 

    $ ssh LOGIN@SERVEUR_ADRESS -p PORT


## Connexion SSH sans mot de passe
Afin de se connecter au pcDuino en SSH sans qu'il nous demande à chaque fois le mot de passe, il faut configurer le SSH. Il faut donc avoir un jeu de clés sur le PC fixe (supposé vu qu'on utilise Git). Ensuite, il faut ajouter notre clé publique dans les clés autorisées du pcDuino :

    $ cat ~/.ssh/id_rsa.pub | ssh ubuntu@192.168.0.102 'cat >> .ssh/authorized_keys'

Sur le PC fixe, la passphrase de la clé SSH sera demandée à chaque fois. Pour éviter cela, on utilise le gestionnaire de mots de passe **Gnome Keyring**.

## Connexion SSH avec X11 forwarding
Le problème de la connexion SSH, c'est que l'on ne peut exécuter que des programmes en ligne de commande et non des utilitaires avec des interfaces graphiques. C'est en fait possible avec la méthode du X11 forwarding. Cela consiste à rediriger les flux X11 donc graphiques sur la machine de l'utilisateur. Pour cela, il suffit de lancer la connexion SSH avec l'option `-Y` :

    ssh -Y utilisateur@adresse_ip

En réalité, le X11 forwarding s'utilise avec l'option `-X` mais cette connexion est soumise à des règles de sécurité qui sont évitées avec `-Y`, ce qui suffit pour un connexion locale.

*EDIT : pas forcement utile :*

Pour utiliser le X11 forwarding dans GNU Screen, il faut reporter la valeur de la variable d'environnement `DISPLAY`. Pour cela, suite à la connexion SSH avec X11 forwarding, afficher la valeur de cette variable :

    echo $DISPLAY

Noter cette valeur et lancer Gnu Screen :

    screen

Dans chaque terminal de Gnu Screen, exporter la variable `DISPLAY` avec la valeur notée précédemment :

    export DISPLAY=localhost:10.0

##Envoi de fichiers et répertoires

De serveur à serveur depuis votre machine locale
Copie récursive d’un dossier d’un serveur (serveur1) vers un autre serveur (serveur2) depuis votre machine locale.
Cela nécessite d’avoir accès aux deux serveurs, depuis votre machine locale, vous lancez une commande qui copiera les fichiers d’un serveur à un autre.

    scp -r -p user@serveur1:chemin/vers/dossier/source user@serveur2:chemin/vers/dossier/destination

De serveur à serveur en étant connecté à un serveur
La commande est sensiblement la même, vous êtes connecté sur la machine où sont disponibles les fichiers.

    scp -r -P PORTS chemin/vers/dossier/source user@serveur2:chemin/vers/dossier/destination

L’option -r indique la récursivité
L’option -p préserve les dates de modification, d’accès, et les modes des anciens fichiers.
L'option -P PORTS juste aprés `scp` pour un port spécifique

###########################################################################################################################

# Autres
## Clavier numerique au demarrage
créer un fichier (s'il n'existe pas) `/etc/lightdm/lightdm.conf.d/20-lubuntu.conf`
et y mettre :
		
	[SeatDefaults]
	greeter-setup-script=/usr/bin/numlockx on

source : <http://doc.ubuntu-fr.org/numlockx>
il faut prélablement avoir installer numlockx en apt-get

## Changer message d'accueil ubuntu
Il s'agit du message informatif afficher au début d'une connexion SSh par exemple.

Créer le fichier `/etc/motd.tail` et y inscrire le message souhaité.

Exemple :

		* Penser a regler la date.
			 - Manuellement :
					 sudo date MMJJHHmmAAAA
			 - Ou plus precisement :
					 sudo ntpdate 192.168.0.103

Source : <http://serverfault.com/questions/407033/how-can-i-edit-the-welcome-message-when-ssh-start>

## Recherche de fichiers
Recherche de tous les fichiers Python dans le répertoire courant :

    find . -name "*.py"


## Processus
La liste des processus lancés par l'utilisateur dans le terminal :

    ps -a

Tuer un processus avec son `pid` :

    kill -9 pid

## Wiimote bluetooth
<http://www.raspberrypi-spy.co.uk/2013/02/nintendo-wii-remote-python-and-the-raspberry-pi/>
<http://linux.arcticdesign.fr/commande-bluetooth-laide-du-pyhton/>

pour activer le bluetooth sur le pc H@ri, a chaque connexion du dongle :
`sudo hciconfig hci0 reset`


## La date sur le pcDuino
Le pcDuino ne dispose pas de module horloge RTC (Real Time Clock) avec une pile permettant de conserver l'information de date.

Il se connecte alors à un serveur NTP lorsqu'il est conecté à internet. Mais sur le robot, il n'a pas accès à internet, il conserve alors une date par défaut (janvier 2010) mais cela pose problème à ROS qui utilise la date à de nombreuses reprises (logs...).

### ntpdate
La façon la plus fiable est d'utiliser `ntpdate` pour récupérer la date d'un ordianteur distant.

- Installer `ntpdate` sur les deux PC :

				$ sudo apt-get install ntpdate

- Installer **en plus** `ntp` sur le PC fixe :

				$ sudo apt-get install ntpdate ntp

- Ensuite il suffit de lancer la commande suivante après le démarrage sur le pcDuinop :

				$ sudo ntpdate adresse.ip.pc.fixe.

### En SSH
Une solution est d'utiliser la date du PC fixe et l'envoyer par SSH. Pour cela, il faut être connecté sur le pcDuino et établir une connexion SSH vers le PC fixe qui doit donc avoir un serveur SSH opérationnel.

    $ sudo date --set="$(ssh user@server 'date -u')"

Source : <http://www.commandlinefu.com/commands/view/9153/synchronize-date-and-time-with-a-server-over-ssh>

### Manuellement
Régler la date manuellement avec le format : "MMJJHHmmYYYY" :

    $ sudo date 071412302006

Source : <http://www.aide-ubuntu.com/date-visualiser-et-modifier-la>


###########################################################################################################################

# SCITE 
## GENERAL CONFIGURATION
    tabsize=4
    indent.size=4
    use.tabs=0

    # open files in the last directory:
    open.dialog.in.file.directory=1
    check.if.already.open=1

    # show all files:
    open.filter=$(all.files)Source Files (cpp,c,h,mak)|*.cpp;*.c;*.h;*.mak;makefile|Web Files (htm, html, css, xml, shtml, js, pl, asp)|*.html;*.htm;*.css;*.shtml;*.js;*.pl;*.xml;*.asp|Text (txt, doc)|*.txt;*.doc|

    # show status bar:
    statusbar.visible=1
    title.full.path=1
    line.margin.visible=1

    # Set monospaced font:
    font.base=$(font.monospace)
    font.small=$(font.monospace)
    font.comment=$(font.monospace)
    font.text=$(font.monospace)
    font.text.comment=$(font.monospace)
    font.embedded.base=$(font.monospace)
    font.embedded.comment=$(font.monospace)
    font.vbs=$(font.monospace)

    # Wrapping of long lines
    wrap=1

    # prompt me for any suspicous action
    are.you.sure.on.reload=1
    load.on.activate=1
    highlight.current.word=1
    #highlight.current.word.by.style=1
    highlight.current.word.colour=#00D040
    selection.back=#F00000
    command.go.*.py=python -u "$(FileNameExt)" $(1)
    file.patterns.cpp=$(file.patterns.cpp);*.nxc;*.cpp;*.h;*.cc