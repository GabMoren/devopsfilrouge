Vagrantfile lance 4 MV :

leader : avec jenkins ansible et docker et maven
mvnexus : pour le repository nexus
mvgitlab : pour le vcs gitlab
mvcible : infra cible pour le deploiement des apps avec ansible-docker depuis leader

Après lancement de toutes les machines il faut configurer Jenkins pour la creation d'un noeud ciblant la machine leader (configuration par ssh avec les clés publiques)

Le dossier jee-projet contient les fichier (dont Dockerfile) qu'il faut mettre sur GitLab
Variable: Utiliser le ficher Dockerfile dans le dossier docker-build

BUILD item sur jenkins
bash command:
mvn clean deploy // package
docker build (-f /path/to/Docker/file) -t 192.168.33.101:5000/tomcat-deploy .
docker push 192.168.33.101:5000/tomcat-deploy

RUN item sur jenkins
bash command:
ansible-playbook /vagrant/ansible/tomcat-deploy.yml
