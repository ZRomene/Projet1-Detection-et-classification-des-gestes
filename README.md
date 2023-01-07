# Projet 1  "Détection et classification des gestes"
###### Réalisé par : *Zaynab ROMENE*
#
Le but de ce projet est de poser 3 questions à l'intermédiaire du port série d'Arduino et de détecter les gestes d'utilisateurs le signe Yes (✓) ou le signe (X) pour savoir sa réponse et l'afficher.
Ceci est fait à l'aide d'un modèle d'intelligence artificielle qu'on va mettre en place dans ce même TP.

## 1- Matériel utilisé: 
#### 1-1- Hardware 
Nous utilisons le Kit d'apprentissage Tiny ML d'arduino pour explorer l'Embedded Machine Learning.C'est une nouvelle technologie qui active l'intelligence artificielle juste à côté du monde physique à l'aide des données récupérées par les capteurs. Ce kit comprend un Arduino Nano 33 BLE Sense. Il est basé sur le microcontrôleur nRF52840 et fonctionne sur le système d'exploitation Arm® Mbed™. Le Nano 33 BLE Sense offre non seulement la possibilité de se connecter via Bluetooth® Low Energy, mais est également équipée de capteurs pour détecter la couleur, la proximité, le mouvement, la température, l'humidité, l'audio et plus encore. Vous pouvez cliquer sur l'image ci-dessous pour plus de détails ou bien il suffit de lire [le datasheet de la carte][df4]. 
[![N|Solid](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQ7IfVcPvM7SwqYIDul2PXhhBmPBYTT7S1rNZ-sMr3BMiu8tbeMrWcBBKtpYS2mg7CYHs4&usqp=CAU)](https://docs.arduino.cc/hardware/nano-33-ble-sense) 
Le processeur est caractérisé par : 
* 64 MHz Arm® Cortex-M4F (with FPU)
* 1 MB Flash + 256 KB RAM

Ce qui nous intéresse dans ce projet c'est la centrale inertielle IMU pour la détection de mouvement. LSM9DS1, comme mentionné dans son [datasheet][df1], comprend un accéléromètre 3D, un gyroscope et un magnétomètre et permet d’utiliser le module IMU Arduino Nano 33 BLE Sense et de détecter par la suite l'orientation, le mouvement ou\et les vibrations.
![] (LSM9DS1.png "La capteur LSM9DS1")
##### 1-1-1- L'accéléromètre 
L'accéléromètre permet de mesurer les forces d'accélération statiques (la force de gravité continue) et dynamiques (les mouvements ou les vibrations).
![] (Les axes de accelerometre.png "Les axes d'accéléromètre")
- La plage de l'accéléromètre est réglée sur [-4, +4]g -/+0,122 mg
- Le débit de données de sortie de l'accéléromètre est fixé à 104 Hz

##### 1-1-2- Le gyroscope
Le gyroscope permet de mesurer et maintenir l'orientation et la vitesse angulaire ( variation de l'angle de rotation  par unité de temps) d'un objet.
Les gyroscopes sont plus avancés que les accéléromètres, car ils peuvent mesurer l'inclinaison et l'orientation latérale d'un objet, alors qu'un accéléromètre ne peut mesurer que son mouvement linéaire.
![] (Les axes de gyroscope.png "Les axes du gyroscope")
- La plage du gyroscope est réglée sur [-2000, +2000] dps +/-70 mdps
- Le débit de données de sortie du gyroscope est fixé à 104 Hz

##### 1-1-3- Le magnétomètre
Le magnétomètre permet de mesures le magnétisme concrétement la direction, l'intensité ou la variation relative d'un champ magnétique à un endroit particulier.
![] (magnetometer.png "Fonctionnement du magnétomètre")
- La plage du magnétomètre est réglée sur [-400, +400] uT +/-0,014 uT 
- Le débit de données de sortie du magnétomètre est fixé à 20 Hz


#### 1-2- Software 
Jupyter notebook c'est un outil qui permet d'exécuter des lignes de code en python. Il est utilisé pour des nombreuses applications comme l'analyse de données ou le traitement d'images. Dans notre cas, on l'utilise pour entrainer et générer un modèle de machine Learning.
[![N|Solid](https://docs.servicestack.net/assets/jupyter-python.6188762b.png)](https://jupyter.org/) 
Bien évidemment , on utilise l'IDE de la carte Arduino pour la programmation de la carte et pour utiliser le modèle entrainé. Vous pouvez avoir plus d'informations en cliquant sur l'image suivante:
[![N|Solid](https://www.1min30.com/wp-content/uploads/2018/12/Logo-Arduino-1.jpg)](https://www.arduino.cc/)

## 2- Les étapes : 
Ce projet est très similaire à [l'exemple suivant][df2] . La seule différence c'est la nature du geste enregistré. On enregistre des Oui et des non ou lieu de punch et flex. On peut alors s'en inspirer.
### 2-1- Construire et nettoyer la base de données
Après avoir téléchargé le fichier .zip  qu'on peut trouver dans [ce lien][df3]. 
Pour collecter les données des gestes Oui V et Non X, on utilise le code IMU_capture. Ce code nous permet d'obtenir les coordonnées de position et d'accélération de la carte en mouvement. Ces coordonnées sont respectivement, ax,ay,az,bx,by,bz. Alors, en faisant chaque mouvement on obtient dans le monitor série les données de ces déplacements répétés 10 fois. On copie les données du monitor série et on les copie dans un fichier de notepad++ puis on enregitre le ficher avec l'extension.csv. 
On a alors deux fichiers CSV un pour chaque geste. 
Il est  indispensable de nettoyer la base de données en supprimant les lignes vides et a vérifiant que la première ligne contient le titre de chaque colonne. 
Une base non adéquate peut déclencher des problèmes ultérieurement.
### 2-2- Entraîner le modèle
On ouvre le fichier Juyter ' *arduino_tinyml_workshop.ipynb* ' qui existe dans ce [ tutoriel ][df4]: . Et on l'adapte aux nouveaux gestes en modifiant les labels et les fichiers.csv. Puis, on exécute le code pour entrainer le modèle en divisant les données en 60% d'entrainement, 20% de test et 2% de validation. Une fois entrainé, on peut récupérer le modèle *model.h* et le télécharger.
### 2-3- Introduire le modèle et préparer le questionnaire
Pour tester le modèle, on ouvre le code Arduino IMU_classifier. Et on remplace l'ancien modèle qui existe dans le dossier de ce code par le nouveau modèle entrainé. Après le téléversent du code dans la carte, on ouvre le monitor série pour tester le fonctionnement; pour ce faire, il faut savoir que les mouvements d'Oui et Non enregistré dans ce projet sont faits de la manière suivante:
![] (oui.png "OUI")
![] (non.png "NON")

Après la vérification de performance, on peut ajouter le code du questionneur. J'ai choisi à ce propos de regrouper tous les lignes de code dans une fonction que j'ai appelée Acquisition. Et puis j'ai posé les trois questions au fur et à mesure dans la boucle Loop en appelant à chaque fois la fonction acquisition pour détecter le geste de l'utilisateur et savoir par la suite sa réponse à la question posée sur le terminal.
![] (.png "")
## 3- Résultat : 
Comme il est montré le capture d'écran ci-dessous, on a réussi à entrainer un modéle de machine learing à l'aide du jupyter notebook et d'utiliser le modèle entrainé dans le microcontroleur pour répondre qu questionnaire.
![] (final.png "Le résultat final")
## Ressources 
- https://create.arduino.cc/projecthub/projects/tags/tinyml
- https://docs.arduino.cc/tutorials/nano-33-ble-sense/imu-gyroscope
- https://docs.arduino.cc/tutorials/nano-33-ble-sense/imu-accelerometer
- https://docs.arduino.cc/tutorials/nano-33-ble-sense/imu-magnetometer
- https://experiments.withgoogle.com/collection/tfliteformicrocontrollers
- https://github.com/walid213/tinyml-workshop
- https://github.com/don/tinyml-workshop
- https://github.com/tensorflow/tflite-micro-arduino-examples#how-to-install

[df1]: https://content.arduino.cc/assets/Nano_BLE_Sense_lsm9ds1.pdf?_gl=1*nrlelw*_ga*MTg0NTMwMTQ0NC4xNjcwNDMwOTEw*_ga_NEXN8H46L5*MTY3Mjk0MjIyOS45LjEuMTY3Mjk0NDUwMy4wLjAuMA..
[df2]: https://github.com/walid213/tinyml-workshop
[df3]:https://github.com/don/tinyml-workshop
[df4]: https://docs.arduino.cc/static/bdb53f29f29a67b0df0243b265617e7b/ABX00031-datasheet.pdf