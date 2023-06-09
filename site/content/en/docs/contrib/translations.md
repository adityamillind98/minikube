---
title: "Translations"
date: 2019-09-30
weight: 3
description: >
  How to add translations
---

All translations are stored in the top-level `translations` directory.

### Adding a New Language

* Add a new json file in the translations directory with the locale code of the language you want to add
  translations for, e.g. fr for French.

 ```
 ~/minikube$ touch translations/fr.json
 ~/minikube$ ls translations/
 de.json  es.json  fr.json  ja.json  ko.json  pl.json  zh-CN.json
 ```

* Run `make extract` from root directory to populate that file with the strings to translate in json
  form.

 ```
 ~/minikube$ make extract
 go run cmd/extract/extract.go
 Compiling translation strings...
 Writing to de.json
 Writing to es.json
 Writing to fr.json
 Writing to ja.json
 Writing to ko.json
 Writing to pl.json
 Writing to zh-CN.json
 Done!
 ```

* Add translated strings to the json file as the value of the map where the English phrase is the key.

 ```
 ~/minikube$ head translations/fr.json
 {
  "\"The '{{.minikube_addon}}' addon is disabled": "",
  "\"{{.machineName}}\" does not exist, nothing to stop": "",
  "\"{{.name}}\" profile does not exist, trying anyways.": "",
  "'none' driver does not support 'minikube docker-env' command": "",
  "'none' driver does not support 'minikube mount' command": "",
  "'none' driver does not support 'minikube podman-env' command": "",
  "'none' driver does not support 'minikube ssh' command": "",
  "'{{.driver}}' driver reported an issue: {{.error}}": "",
 ```

* Add the translations as the values of the map, keeping in mind that anything in double braces `{{}}` are variable names describing what gets injected and should not be translated.

 ```
 ~/minikube$ vi translations/fr.json
 {
         [...]
         "Amount of time to wait for a service in seconds": "",
         "Amount of time to wait for service in seconds": "",
         "Another hypervisor, such as VirtualBox, is conflicting with KVM. Please stop the other hypervisor, or use --driver to switch to it.": "",
         "Automatically selected the {{.driver}} driver": "Choix automatique du driver {{.driver}}",
         "Automatically selected the {{.driver}} driver. Other choices: {{.alternates}}": "Choix automatique du driver {{.driver}}. Autres choix: {{.alternatives}}",
         "Available Commands": "",
         "Basic Commands:": "",
         "Because you are using docker driver on Mac, the terminal needs to be open to run it.": "",
         [...]
 }
 ```

### Adding Translations To an Existing Language

* Run `make extract` to make sure all strings are up to date
* Edit the appropriate json file in the 'translations' directory, in the same way as described above.

### Testing translations

* You can verify that the translations are syntactically valid by running: `go test k8s.io/minikube/pkg/minikube/translate`

* Once you have all the translations you want, save the file and rebuild the minikube from scratch to pick up your new translations:

 ```
 ~/minikube$ make clean
 rm -rf ./out
 rm -f pkg/minikube/assets/assets.go
 rm -f pkg/minikube/translate/translations.go
 rm -rf ./vendor
 ~/minikube$ make
 ```
 Note: the clean is required to regenerate the embedded `translations.go` file

* You now have a fresh minikube binary in the `out` directory. If your system locale is that of the language you added translations for, a simple `out/minikube start` will work as a test, assuming you translated phrases from `minikube start`. You can use whatever command you'd like in that way.

* If you have a different system locale, you can override the printed language using the LANGUAGE environment variable:

 ```
 ~/minikube$ LANGUAGE=fr out/minikube start
 😄  minikube v1.29.0 sur Ubuntu 18.04
 ✨  Choix automatique du pilote docker. Autres choix: kvm2, ssh
 📌  Utilisation du pilote Docker avec le privilège root
 👍  Démarrage du noeud de plan de contrôle minikube dans le cluster minikube
 🚜  Extraction de l'image de base...
 🔥  Création de docker container (CPUs=2, Memory=7900Mo) ...
 🐳  Préparation de Kubernetes v1.26.1 sur Docker 20.10.23...
     ▪ Génération des certificats et des clés
     ▪ Démarrage du plan de contrôle ...
     ▪ Configuration des règles RBAC ...
 🔗  Configuration de bridge CNI (Container Networking Interface)...
     ▪ Utilisation de l'image gcr.io/k8s-minikube/storage-provisioner:v5
 🔎  Vérification des composants Kubernetes...
 🌟  Modules activés: storage-provisioner, default-storageclass
 🏄  Terminé ! kubectl est maintenant configuré pour utiliser "minikube" cluster et espace de noms "default" par défaut.
 ```
