# WSL-Debian-Docker-Compose-Wordpress
Installer Wordpress avec Windows WSL Debian, Docker Compose.

# 📘 Installer Debian sous WSL avec Docker et WordPress (via docker-compose)

## 🎯 Objectifs

* Installer **Debian** sous **WSL2** sur Windows 11
* Configurer **Docker** et **Docker Compose** dans Debian
* Déployer **WordPress** via `docker-compose`
* Comprendre et manipuler un fichier `docker-compose.yml`

---

## 🔧 Prérequis

* Windows 11 (avec mises à jour récentes)
* Accès administrateur
* Terminal PowerShell ou Windows Terminal

---

## 🛠️ Installation de WSL et Debian

### 1. Installer WSL (si non présent)

Ouvrir **PowerShell en mode administrateur** :

```powershell
wsl --install
```

Cela installe WSL2 avec Ubuntu par défaut.

### 2. Installer Debian

```powershell
wsl --install -d Debian
```

* Ouvrir le terminal et switcher avec la flèche du temrinal sur **debian**.

Configurez le compte utilisateur UNIX (nom + mot de passe).

---

## 🐳 Installer Docker dans Debian (WSL)

### 1. Installer les dépendances

```bash
sudo apt update
sudo apt install -y ca-certificates curl gnupg lsb-release
```

### 2. Ajouter le dépôt Docker officiel

```bash
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

### 3. Installer Docker

```bash
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io
```

### 4. Ajouter l'utilisateur au groupe docker

```bash
sudo usermod -aG docker $USER
newgrp docker
```

Redémarrez le terminal si nécessaire.

### 5. Tester l'installation

```bash
docker run hello-world
```

---

## 📦 Installer Docker Compose (v2)

```bash
DOCKER_CONFIG=${DOCKER_CONFIG:-$HOME/.docker}
mkdir -p $DOCKER_CONFIG/cli-plugins
curl -SL https://github.com/docker/compose/releases/latest/download/docker-compose-linux-$(uname -m) \
  -o $DOCKER_CONFIG/cli-plugins/docker-compose
chmod +x $DOCKER_CONFIG/cli-plugins/docker-compose
```

### Tester :

```bash
docker compose version
```

---

## 🌐 Déployer WordPress avec `docker-compose.yml`

### 1. Créer un dossier projet

```bash
mkdir ~/wordpress-docker && cd ~/wordpress-docker
```

### 2. Créer le fichier `docker-compose.yml`

```bash
nano docker-compose.yml
```

### 3. Coller ce contenu :

```yaml
version: '3.9'

services:
  db:
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wpuser
      MYSQL_PASSWORD: wppass
      MYSQL_ROOT_PASSWORD: rootpass
    volumes:
      - db_data:/var/lib/mysql

  wordpress:
    image: wordpress:latest
    restart: always
    ports:
      - "8000:80"
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wpuser
      WORDPRESS_DB_PASSWORD: wppass
      WORDPRESS_DB_NAME: wordpress
    depends_on:
      - db

volumes:
  db_data:
```

### 4. Lancer WordPress

```bash
docker compose up -d
```

> Accéder à : [http://localhost:8000](http://localhost:8000)

---

## 🤖 Commandes utiles

* Arrêter les conteneurs :

  ```bash
  docker compose down
  ```
* Voir les logs :

  ```bash
  docker compose logs -f
  ```

---

## 🧠 Récapitulatif

| Action                   | Commande principale          |
| ------------------------ | ---------------------------- |
| Installer WSL + Debian   | `wsl --install -d Debian`    |
| Installer Docker         | `sudo apt install docker-ce` |
| Installer Docker Compose | via plugin GitHub            |
| Lancer WordPress         | `docker compose up -d`       |
