# LAB 5 — Analyse de l’application Android UnCrackable-Level2

Étudiante : HAFSSA AZARG

Date : 09/03/2026

Outils utilisés : ADB, JADX, Ghidra, Python, PowerShell



# 1. Présentation du laboratoire

L’objectif de ce laboratoire est d’analyser une application Android protégée afin de comprendre comment elle vérifie une clé secrète.
Pour cela, nous utilisons des outils de reverse engineering afin d’examiner à la fois le code Java et le code natif (.so).

L’application étudiée est UnCrackable-Level2, qui cache son mécanisme de vérification dans une bibliothèque native.

# 2. Exploration initiale de l’application

Étape 1 — Installation et exécution de l’APK

La première étape consiste à installer l’application sur l’émulateur ou l’appareil Android.

Commande :

![](https://github.com/user-attachments/assets/33401698-3ae6-4c9b-8770-0149d7c75b45)
![](https://github.com/user-attachments/assets/d933afa3-5fbd-40c3-80e6-07384c5d23b3)
![](https://github.com/user-attachments/assets/0a6ebba4-52e5-4b30-89aa-a876a47bcea2)
![](https://github.com/user-attachments/assets/dd45b28a-47a5-4094-8ce6-7b82c9643d6f)
![](https://github.com/user-attachments/assets/d3709c9d-dfd1-4bb0-9765-76272679c5cb)

Ensuite, l’application est lancée pour observer son comportement.
Elle affiche un champ permettant de saisir un secret.

# 3. Analyse du code Java
 Étape 2 — Décompilation avec JADX

Pour examiner la structure Java de l’application, on utilise JADX, un outil qui permet de reconstruire le code source à partir d’un APK.

Commande :

 jadx-gui UnCrackable-Level2.apk

Après l’ouverture de l’APK, on peut parcourir les classes de l’application.

Étape 3 — Analyse de MainActivity

Dans la classe MainActivity, on observe que la saisie utilisateur est envoyée vers une fonction de validation.

![](https://github.com/user-attachments/assets/0ae2f181-f63e-4c63-9339-8e165b0feb79)

Cette classe semble responsable de la vérification du secret.

# 4. Identification de la vérification
Étape 4 — Repérer l’appel de validation dans MainActivity 

En examinant cette classe, on remarque que la méthode bar() est déclarée comme native.

![](https://github.com/user-attachments/assets/0e51d2d2-17be-41d7-bbef-f06b0c9faac7)
Cela signifie que la logique principale n’est pas écrite en Java, mais dans une bibliothèque native compilée en C/C++.

# 5. Extraction de la bibliothèque native
Étape 5 — Identifier la classe qui effectue la vérification

![](https://github.com/user-attachments/assets/4e5e6220-6774-4458-87f0-3802578ca3fb)

Étape 6 — Extraire le contenu de l’APK

![](https://github.com/user-attachments/assets/7af98700-f2b0-40ce-968b-77d306ec6220)

La bibliothèque native libfoo.so est prête pour l’analyse statique

Étape 7 — Importer libfoo.so dans Ghidra

# Lancer Ghidra :

![](https://github.com/user-attachments/assets/9b9448c0-84b9-432c-b4f5-230b2c45c964)
# Créer un nouveau projet

![](https://github.com/user-attachments/assets/e6fb8b00-4d12-4c95-adbc-149f2ff6f456)
# Importateur :libfoo.so

![](https://github.com/user-attachments/assets/43b70ded-6442-46af-8032-a87e4660a600)

# Analyse Lancer Auto
Observation Ghidra génère :
- le pseudo-code
- la liste des fonctions exportées
  
# 6. Compréhension de la vérification

Étape 8 — Chercher la fonction JNI liée à bar

![](https://github.com/user-attachments/assets/926f17d1-cbb6-4b87-b2ef-36dcc5afa1e8)

# 7. Décodage du secret

Dans certains cas, la chaîne peut être stockée en hexadécimal.

Exemple de conversion avec Python :

# hex_data = "6873696620656874206c6c6120726f6620736b6e616854"
# ascii_text = bytes.fromhex(hex_data).decode("ascii")
# print(ascii_text)

Résultat :

hsif eht lla 
Thanks for all the fish
# 8. Résultat obtenu

Le secret attendu par l’application est :

Merci pour tous les poissons

En entrant cette phrase dans l’application, la validation est acceptée.

# 9. Conclusion

Ce laboratoire permet de comprendre les bases du reverse engineering Android.

Les étapes principales sont :

analyser l’interface utilisateur

examiner le code Java avec JADX

identifier les fonctions natives

analyser la bibliothèque .so avec Ghidra

retrouver le secret caché

Cette approche montre comment certaines applications déplacent leur logique critique dans du code natif afin de rendre l’analyse plus difficile.
