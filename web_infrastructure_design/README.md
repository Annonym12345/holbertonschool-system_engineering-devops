# Web Infrastructure Design

Ce projet a pour objectif de concevoir et comprendre différentes architectures web, allant d'une infrastructure simple sur un seul serveur à une architecture sécurisée, surveillée et capable de monter en charge.

---

# Tâche 0 : Infrastructure Web Simple

## Diagramme

```text
                Utilisateur
                     |
                     | HTTP/HTTPS
                     |
             www.foobar.com
                     |
                Enregistrement A
                     |
                  8.8.8.8
                     |
    +--------------------------------+
    |            SERVEUR             |
    |                                |
    |  +--------------------------+  |
    |  |      Nginx Web Server    |  |
    |  +------------+-------------+  |
    |               |                |
    |  +------------v-------------+  |
    |  |   Serveur d'Application  |  |
    |  +------------+-------------+  |
    |               |                |
    |  +------------v-------------+  |
    |  |   Fichiers Application   |  |
    |  +------------+-------------+  |
    |               |                |
    |  +------------v-------------+  |
    |  |    Base de Données       |  |
    |  |         MySQL            |  |
    |  +--------------------------+  |
    +--------------------------------+
```

### Composants

- Nom de domaine : foobar.com
- Enregistrement DNS A : www.foobar.com → 8.8.8.8
- Serveur Web Nginx
- Serveur d'Application
- Code de l'Application
- Base de Données MySQL

### Limites

- Point unique de défaillance (SPOF)
- Temps d'arrêt lors des déploiements
- Impossible de gérer un trafic important

---

# Tâche 1 : Infrastructure Web Distribuée

## Diagramme

```text
                    Utilisateur
                         |
                   www.foobar.com
                         |
                  +-----------+
                  | HAProxy   |
                  +-----+-----+
                        |
           +------------+------------+
           |                         |
+----------v---------+     +---------v----------+
|     SERVEUR 1      |     |     SERVEUR 2      |
|--------------------|     |--------------------|
| Nginx              |     | Nginx              |
| Application        |     | Application        |
| Code Application   |     | Code Application   |
+---------+----------+     +----------+---------+
          |                           |
          +------------+--------------+
                       |
          +------------v------------+
          |      MySQL Primaire     |
          +------------+------------+
                       |
          +------------v------------+
          |      MySQL Réplique     |
          +-------------------------+
```

### Éléments ajoutés

- Load Balancer HAProxy
- Deuxième serveur web
- Réplication MySQL

### Avantages

- Répartition de charge
- Meilleure disponibilité
- Tolérance aux pannes

### Limites

- Le load balancer reste un SPOF
- Pas de HTTPS
- Pas de firewall
- Pas de monitoring

---

# Tâche 2 : Infrastructure Sécurisée et Supervisée

## Diagramme

```text
                         Internet
                             |
                           HTTPS
                             |
                      +-------------+
                      | Firewall    |
                      +------+------+
                             |
                      +------+------+
                      |   HAProxy   |
                      | Certificat  |
                      | SSL/TLS     |
                      +------+------+
                             |
              +--------------+--------------+
              |                             |
    +---------v---------+       +-----------v--------+
    | Firewall          |       | Firewall           |
    | Serveur 1         |       | Serveur 2          |
    |-------------------|       |--------------------|
    | Nginx             |       | Nginx              |
    | Application       |       | Application        |
    | Agent Monitoring  |       | Agent Monitoring   |
    +---------+---------+       +----------+---------+
              |                            |
              +-------------+--------------+
                            |
                   +--------v--------+
                   | MySQL Primaire  |
                   | Monitoring      |
                   +-----------------+
```

### Éléments ajoutés

- 3 Firewalls
- Certificat SSL/TLS
- 3 Agents de monitoring

### Avantages

- Chiffrement HTTPS
- Protection contre les accès non autorisés
- Collecte de métriques et de logs
- Surveillance des performances

### Limites

- SSL terminé au niveau du load balancer
- Une seule base de données en écriture
- Tous les composants ne sont pas encore séparés

---

# Tâche 3 : Passage à l'Échelle (Scale Up)

## Diagramme

```text
                           Internet
                               |
                  +------------+------------+
                  |      HAProxy #1         |
                  +------------+------------+
                               |
                  +------------+------------+
                  |      HAProxy #2         |
                  |      (Cluster)          |
                  +------------+------------+
                               |
               +---------------+---------------+
               |                               |
       +-------v-------+               +-------v-------+
       | Web Server 1  |               | Web Server 2  |
       |    Nginx      |               |    Nginx      |
       +-------+-------+               +-------+-------+
               |                               |
               +---------------+---------------+
                               |
                    +----------v----------+
                    | Serveur Application |
                    +----------+----------+
                               |
                    +----------v----------+
                    |   Serveur MySQL     |
                    +---------------------+
```

### Éléments ajoutés

- Deuxième Load Balancer HAProxy
- Serveurs Web dédiés
- Serveur Application dédié
- Serveur Base de Données dédié

### Avantages

- Haute disponibilité
- Meilleure répartition des ressources
- Maintenance simplifiée
- Évolutivité améliorée

---

# Concepts Importants

## Serveur

Un serveur est une machine qui fournit des services à d'autres ordinateurs via un réseau.

## Nom de domaine

Permet d'accéder à un site web à l'aide d'un nom facile à retenir plutôt qu'une adresse IP.

## Serveur Web (Nginx)

Reçoit les requêtes HTTP/HTTPS et distribue les ressources aux utilisateurs.

## Serveur d'Application

Exécute la logique métier de l'application.

## Base de Données

Stocke et récupère les informations nécessaires au fonctionnement de l'application.

## Load Balancer

Répartit le trafic entre plusieurs serveurs afin d'éviter la surcharge.

## Firewall

Filtre les connexions entrantes et sortantes afin de protéger l'infrastructure.

## Monitoring

Permet de surveiller l'état, les performances et la disponibilité des serveurs.

---

## Auteur

Holberton School – System Engineering & DevOps
