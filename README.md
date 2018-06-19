# Projet fil rouge realisé au m2i formation
## Introduction
Ce projet consiste à l'installation des 3 outils principaux : Ansible, Jenkins et Docker; sur une machine virtuel "leader" crée avec Vagrant.
## Envirenement avec Vagrant
Vagrantfile crée 4 machines virtueles :

- leader : avec jenkins, ansible, docker et maven
- mvnexus : pour le repository nexus
- mvgitlab : pour l'installation du gitlab (optional)
- mvcible : infra cible pour le deploiement des conteneurs avec ansible depuis le leader

Pour lancer les machines il suffit de faure un `vagrant up`

Uiliser `vagrant provision leader` pour relancer les playbooks ansible

Après lancement de toutes les machines il faut configurer Jenkins pour la creation d'un noeud ciblant la machine leader (configuration par ssh avec les clés publiques)

Le dossier jee-projet contient les fichier (dont Dockerfile) qu'il faut mettre sur GitLab
Variable: Utiliser le ficher Dockerfile dans le dossier docker-build

## Les Build and Run sur jenkins
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
