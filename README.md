# jenkins-training
Training repository to experiment jenkins

## 1. installation JAVA / MAJ système

```bash
# installer JDK 17 
sudo apt-get install -y openjdk-17-jdk-headless
# (optionnel pour nettoyage) et desinstaller JDK 11 (ou version antérieure, jenkins comptatible qu'avec 17+)
sudo apt-get remove --purge -y openjdk-11-*
sudo apt-get autoremove -y

# mettre à jour les packages système
sudo apt update -y
sudo apt upgrade -y
```

## 2. installation JENKINS

```bash
# ajouter la clé du référentiel jenkins a notre système
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins -y

# démarrage et verification du démarrage de jenkins
sudo systemctl start jenkins
sudo systemctl status jenkins

# activer jenkins au démarrage et le démarrer (démarrage persistant)
sudo systemctl enable --now jenkins
# désativer jenkins au démarrage et l'arrêter (arrêt définitif)
sudo systemctl disable --now jenkins

# au premier démarrage récupérer le mot de passe jenkins auto-généré
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

## 3. installation DOCKER

```bash
# installer docker en ajoutant au préalable la clé du référentiel
sudo apt-get install ca-certificates curl gnupg lsb-release -y
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update -y
sudo apt-get install docker-ce docker-ce-cli containerd.io -y
sudo systemctl enable --now docker

# ajouter l'utilisateur jenkins au groupe docker et verification (nécessaire pour le pilotage par jenkins de docker)
sudo usermod -aG docker jenkins
groups jenkins
```

## 3. tips JENKINS

Accès aux variables d'environnement jenkins
>http://localhost:8080/env-vars.html/
