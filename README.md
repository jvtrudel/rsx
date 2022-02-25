# Remote Shell Execution



Exécute une commande shell (presque qu') aussi facilement que localement.


## Principes de design

 - exécute des commandes remotes ou scripts de manière transparente
 - avoir le moins de overhead possible
 - est basé sur ssh et gpg


## Configuration


Manuellement:

  - un profil de connection dans ~/.ssh/profil,
  - un mot de passe dans un fichier,
  - une clef ssh inscrite sur le serveur,
  - un agent ssh en activité
  - un ne clef inscrite à l'agent


Configuration automatique:

```
# À partir d'un profil existant
. <(rsx start <hid_profil>)

# Désactiver le(s) profile(s)
rsx stop
```

Un profil activé est une entrée de profil ssh et un agent démarré.

L'implémentation de start doit inclure le stop dans un trap pour que la session se ferme automatiquement.

Le stop est optionel.

Les profils sont en

## Usage


rsx argument-commande passe les commandes par stdin.

```
# commande simple 
echo "whoami" | rsx

# ou
rsx <<< "whoai;pwd"

# un script dans un fichier
cat myscript.sh | rsx

# un script à la volée
cat <<-EOM | rsx
whoami
pwd
ls
EOM
```

## Profiles


Un profil contient l'ensemble des informations nécessaires à la connection et à l'exécution sur un serveur.

Un profil contient une configuration ssh et ses variations possibles. Par exemple, la configuration pour plusieurs utilisateurs.

Un profil contient les configurations d'utilisateurs: username, password. Peut contenir les informations de plusieurs utilisateurs et plusieurs mots de passe pour un même utilisateur.


Schema:

```
hid_profile_name:

  default:
    user: *user1
    server: *server1
    sshkey: *sshkey1
    gpgkey: *gpgkey1
  users:
    user1: &user1
        passwd: xxxxx
        alt_passwd:
          - label: tmp
            passwd: xxxyxx
  servers:
    server1: &server1
      ip: ...
      hostname: ...
      # peut prendre toutes les informations d'une configuration ssh
      # lorsqu'il y a plusieurs valeurs possibles, on les met dans alt_..., exemple alt_hostname
  sshkeys:
    sshkey1: &sshkey1
      path: ...
      fingerprint: ... # généré automatiquement
      md5sum: ... # généré automatiquement
      passphrase: ... 
  gpgkeys:
    gpgkey1: &gpgkey1
      username: ...
      passphrase: ...
```

Création et configuration des profiles:


```
# nouveau profile:
rsx new profile jvtrudel

# nouvel utilisateur
rsx jvtrudel new user # interactive
rsx jvtrudel new user name=totoro  
...


# importation d'un profile
rsx import unencrypted_profile.file

cat unencrypted_profile.file | rsx import
```


# Cas intéressants


## Escalation de privilège

### sudo

### autre utilisateur

### sudo avec autre utilisateur

### Généralisation du chainage d'utilisateurs

## Changement de mot de passe

Changer de mot de passe et continuer avec une commande sudo


