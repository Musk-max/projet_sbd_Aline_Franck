Rapport Intermédiaire — Projet SBD — Differential privacy
Étudiants : Aline N’DEKO & Franck LAGOU
Lien github du projet : https://github.com/Musk-max/projet_sbd_Aline_Franck/
Compréhension du sujet
Le but principal du projet est d’implémenter l’algorithme Privelet, un mécanisme basé sur la confidentialité différentielle appliqué aux données ordinales unidimensionnelles. Il utilise la Haar Wavelet Transform (HWT) pour compresser et perturber les histogrammes de manière intelligente et structurée, avec un meilleur compromis précision/confidentialité que les mécanismes classiques.

Contexte 	
La confidentialité différentielle est un cadre formel pour garantir la protection de la vie privée des individus dans un jeu de données.
Cependant, ajouter du bruit aléatoire directement à chaque cellule d’un histogramme peut fortement dégrader la qualité des réponses aux requêtes.
L’algorithme Privelet, proposé par Xiao et al. (2011), offre une meilleure alternative en transformant les données avec des ondelettes avant d’ajouter du bruit, ce qui permet de réduire la variance du bruit sur certaines requêtes, notamment les requêtes de comptage par intervalles (range-count queries).
Déroulement du projet et approche adoptée
Lecture et compréhension de la section 4 (HWT & Privelet)
Étude du fonctionnement de la Haar Wavelet Transform : 
Décomposition d’un vecteur ordinal via un arbre binaire complet, calcul des coefficients (différences moyennes) puis reconstruction.On part d’un vecteur M de longueur 2^l (par exemple : [2, 4, 6, 8, 5, 7, 3, 9]).On construit un arbre binaire complet dont les feuilles (les nœuds tout en bas) sont simplement les valeurs de M. Donc si M = [v1, v2, ..., v8], les feuilles de l’arbre seront dans cet ordre : v1, v2, ..., v8.
Calcul des coefficients de HWT
Ensuite, pour chaque nœud interne de cet arbre (ceux qui ne sont pas des feuilles), on calcule un coefficient d'ondelette (wavelet coefficient).
Pour un nœud interne donné N, on regarde toutes les feuilles dans son sous-arbre gauche et on calcule leur moyenne a1.
Ensuite, on regarde toutes les feuilles dans son sous-arbre droit et on calcule leur moyenne a2.
👉 Ce coefficient capture la différence moyenne entre la partie gauche et droite du sous-arbre — il encode une forme de "variation locale".
Compréhension de l’instanciation de Privelet : ajout de bruit Laplacien pondéré par un facteur WHaar(c) selon la profondeur des coefficients.


Implémentation technique (Python)
Création d’un système de contrôle de version : github : https://github.com/Musk-max/projet_sbd_Aline_Franck/
Installation de l’environnement virtual venv 
Installation de numpy, scipy et pandas


Intégration des données réelles (Adult)
Chargement du fichier adult.data.
Extraction de la colonne "éducation".
Calcul de l’histogramme brut de la distribution des niveaux d’éducation.


État d’avancement

Lecture de l’article scientifique terminée.

Histogramme H1 fonctionnel.

HWT implémentée.

Mécanisme Privelet codé (coefficients + bruit + reconstruction) (à faire).

Intégration du calcul de la distance de Wasserstein (en cours.
Génération des graphiques de performance.

Étapes restantes (pour le rapport final)

Finaliser les tests expérimentaux (ε = 0.01, 0.1, 1, 10).

Générer les courbes d’évolution de la distance de Wasserstein.

Rédiger une analyse critique des résultats : compromis entre précision et confidentialité.

Rédaction finale du rapport (5 pages max), avec figures, commentaires, et références.

Ajout d’un README clair expliquant l’installation et l’utilisation du code Python.

Préparation de l’archive .ZIP finale à rendre.
