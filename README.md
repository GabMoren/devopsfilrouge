# Projet fil rouge realisé au m2i formation
## Introduction
Ce projet consiste à l'installation des 3 outils : Ansible, Jenkins et Docker; sur une machine virtuel "leader" crée avec Vagrant.
A partir des playbooks lancés sur la machine leader, on peut installer les autres logiciels sur les autres machines.

Les jobs sur jenkins font appel à des playbooks pour le depliement des contineurs docker.

## Envirenement avec Vagrant
Vagrantfile crée 4 machines virtueles :

- leader : avec jenkins, ansible, docker et maven
- mvnexus : pour le repository nexus et egalement le serveur sonar
- mvgitlab : pour l'installation du gitlab (optional)
- mvcible[1:3] : infra cible pour le deploiement des conteneurs avec ansible depuis le leader.

Pour lancer les machines il suffit de faire un `vagrant up`
Uiliser `vagrant provision leader` pour relancer les playbooks ansible. Le playbook lancé au démarrage de la machine est le playjenkins.yml qui contient les roles suivants :

- java
- docker
- registry
- maven

## Les playbook ansible
Dans le dossier ansible de la racine du projet se trouvent tout les fichier yml plus les roles respectives. Ce dossier est pressent dans toutes les machines virtueles sur /vagrant/ansible.

Pour lancer un playbook ansible il suffit de faire la commande `ansible-playbook /vagrant/ansible/<play-nom>` depuis la machine leader

Les differents playbook sont :

- playjenkins.yml
- playnexus.yml, avec les roles : docker, nexus et sonar (optional)
- playgitlab.yml, avec les roles : docker et gitlab
- tomcat-deploy.yml, qui deploie un conteneur tomcat d'une image crée avec jenkins (voir configuration jenkins). **remplacé par docker swarm**
- playswarm.yml, qui s'en charge de l'initiation du docker swarm avec leader en tant que manager et les cibles en tant que workers.
- swarm-deply.yml, qui s'en charge du déploiement de l'image crée avec jenkins en mode service sur le swarm docker

## Configuration jenkins
Après lancement de toutes les machines il faut configurer Jenkins pour la creation d'un noeud ciblant la machine leader (configuration par ssh avec les clés publiques)

Le dossier jee-projet contient les fichier (dont Dockerfile) qu'il faut mettre sur GitLab
Variable: Utiliser le ficher Dockerfile dans le dossier docker-build


BUILD job sur jenkins avec commande bash:
```
mvn clean package #or deploy
docker build (-f /path/to/Docker/file) -t 192.168.33.101:5000/tomcat-deploy .
docker push 192.168.33.101:5000/tomcat-deploy
```

RUN job sur jenkins avec déclanchement apres job build :
```
ansible-playbook /vagrant/ansible/tomcat-deploy.yml
```
