Rapport final — Projet SBD — Differential privacy
Étudiants : Aline N’DEKO & Franck LAGOU

Lien github du projet : https://github.com/Musk-max/projet_sbd_Aline_Franck/tree/module2

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
Création d’un système de contrôle de version : github : https://github.com/Musk-max/projet_sbd_Aline_Franck/tree/module2
Installation de l’environnement virtual venv 
Installation de numpy, scipy et pandas


Intégration des données réelles (Adult)
Chargement du fichier adult.data.
Extraction de la colonne "éducation".
Calcul de l’histogramme brut de la distribution des niveaux d’éducation.

Privelet Algorithm
Description de l’algorithme Privelet
Privelet est un algorithme permettant de garantir la confidentialité différentielle par l’intermédiaire de plusieurs étapes :

Application de la transformée de Haar sur les données,
Ajout de bruit Laplacien calibré sur les coefficients obtenus,
Reconstruction des données bruitées,
Analyse de la qualité du résultat.

Nous allons détailler ici chacune des étapes, en précisant les fonctions utilisées.
Décomposition en étapes et rôles des fonctions
Préparation des données
Fonction : pad_to_power_of_two(arr)
Rôle :
 La transformée de Haar classique nécessite que le nombre de points soit une puissance de deux. Si ce n'est pas le cas, cette fonction ajoute des zéros à la fin de l'histogramme jusqu’à atteindre la taille requise. Garantit la validité de la transformation Haar.
Application de la transformée Haar
Fonction : haar_wavelet_transform(data)
Rôle :
 La fonction transforme les données en une série de coefficients de Haar, représentant :
Les tendances globales (moyennes),


Les détails locaux (différences entre groupes de données).
Chaque étape de la transformation réduit la résolution des données, capturant ainsi à différentes échelles l’information. Elle permet de structurer l’information de manière hiérarchique, ce qui est crucial pour l'ajout efficace de bruit.
Calcul des poids de Haar
Fonction : compute_whaar_weights(num_coeffs)
Rôle :
 Attribuer un poids spécifique à chaque coefficient en fonction de son niveau de granularité.
Les coefficients représentant des détails fins ont généralement moins d’importance.


Ceux représentant des moyennes globales sont plus sensibles.
Ces poids servent à calibrer la quantité de bruit ajouté à chaque coefficient pour équilibrer confidentialité et précision.
Ajout de bruit Laplace
Fonction : add_laplace_noise(coeffs, epsilon)
Rôle : Ajouter un bruit aléatoire de type Laplacien à chaque coefficient transformé. Le bruit est proportionnel au poids du coefficient et inversement proportionnel au paramètre ε (epsilon), garantissant :
Plus ε est petit → plus de bruit → plus de confidentialité,


Plus ε est grand → moins de bruit → plus de précision.
Cette étape est celle qui assure la confidentialité différentielle.
Reconstruction inverse
Fonction : inverse_haar_wavelet_transform(coeffs)
Rôle : Revenir du domaine des coefficients bruités vers un histogramme bruité.
La reconstruction utilise l'inverse de la transformation Haar,


Les données sont ramenées à leur échelle originale.
C’est ce résultat final qui sera publié comme réponse à la requête, garantissant la confidentialité.

Experimental Evaluation
Évaluation de la qualité des résultats
Fonction : wasserstein_distance(u, v) (de scipy.stats)
Rôle: Mesurer la distance de Wasserstein (aussi appelée Earth Mover’s Distance) entre l’histogramme original et celui bruité.
Plus la distance est faible, plus l'histogramme bruité est proche du vrai histogramme.

Cela nous donne une quantification objective de la perte d'information.

Protocole expérimental
Nous avons évalué la qualité en suivant cette méthode :
Pour chaque valeur de ε parmi {0.01, 0.1, 1, 10},
Répéter l’expérience 20 fois pour compenser l’aléa du bruit,
Calculer les moyennes, minimum et maximum des distances.

Analyse des résultats

Les résultats confirment que :
Avec ε = 0.01, la distance est grande → forte perte d'information mais confidentialité maximale,

Avec ε = 10, la distance est faible → précision quasi intacte mais confidentialité moindre.
On retrouve donc le compromis classique entre confidentialité et utilité.
III. Conclusion
À travers ce projet, nous avons pu :
Comprendre l'intérêt de l'approche multi-échelles de Privelet,

Vérifier empiriquement l'impact du paramètre ε sur la précision des résultats,

Confirmer que la transformée Haar permet un ajout plus intelligent de bruit que l’ajout naïf.
En résumé, Privelet s’avère être une méthode performante pour les données ordinales lorsque la préservation de la confidentialité est essentielle.
Annexe : Liste des fonctions et leur rôle
