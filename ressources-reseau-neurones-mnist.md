# Réseau de neurones from scratch (numpy) sur MNIST - Ressources

Règle du projet : zéro IA, numpy comme seule bibliothèque, aucune source qui donne le code du projet.
Chaque source ci-dessous explique le QUOI et le POURQUOI, jamais le COMMENT tout fait.

---

## 1. Les données : MNIST et le format IDX

- [THE MNIST DATABASE - Yann LeCun](https://yann.lecun.com/exdb/mnist/)
  - Ta spec. Lire la section "FILE FORMATS FOR THE MNIST DATABASE" en bas de page (nombre magique, dimensions, ordre des octets).
  - Le tableau des taux d'erreur publiés sert d'oracle (ex : 2-layer NN, 300 hidden units, MSE : 4,7 % d'erreur).
- Téléchargement : les fichiers du site officiel renvoient souvent une erreur 403. Utiliser le miroir S3 (téléchargement manuel dans le navigateur) :
  - [train-images-idx3-ubyte.gz](https://ossci-datasets.s3.amazonaws.com/mnist/train-images-idx3-ubyte.gz)
  - [train-labels-idx1-ubyte.gz](https://ossci-datasets.s3.amazonaws.com/mnist/train-labels-idx1-ubyte.gz)
  - [t10k-images-idx3-ubyte.gz](https://ossci-datasets.s3.amazonaws.com/mnist/t10k-images-idx3-ubyte.gz)
  - [t10k-labels-idx1-ubyte.gz](https://ossci-datasets.s3.amazonaws.com/mnist/t10k-labels-idx1-ubyte.gz)

---

## 2. Python et numpy : doc officielle uniquement

### Python
- [Module gzip](https://docs.python.org/3/library/gzip.html) - ouvrir les .gz sans décompresser à la main.
- [Module struct](https://docs.python.org/3/library/struct.html) - lire l'en-tête big-endian.
- [int.from_bytes](https://docs.python.org/3/library/stdtypes.html#int.from_bytes) - alternative plus simple à struct.

### numpy
- [Broadcasting](https://numpy.org/doc/stable/user/basics.broadcasting.html) - A LIRE EN ENTIER, piège n°1 du projet.
- [numpy.frombuffer](https://numpy.org/doc/stable/reference/generated/numpy.frombuffer.html) - octets bruts vers tableau.
- [Data types](https://numpy.org/doc/stable/user/basics.types.html) - comprendre pourquoi uint8 déborde.
- [Random Generator (default_rng)](https://numpy.org/doc/stable/reference/random/generator.html) - initialisation reproductible avec une graine.
- [Index de la doc numpy](https://numpy.org/doc/stable/reference/index.html) - pour np.exp, np.log, np.max, np.argmax, l'opérateur @, et les paramètres axis / keepdims.

---

## 3. Les maths

### Intuition (aucun code)
- [3Blue1Brown - série Neural Networks](https://www.3blue1brown.com/topics/neural-networks) - chapitres 1 à 4 (versions texte disponibles).
  - [What is backpropagation really doing?](https://www.3blue1brown.com/lessons/backpropagation)
  - [Backpropagation calculus](https://www.3blue1brown.com/lessons/backpropagation-calculus)
- [Khan Academy - Multivariable calculus](https://www.khanacademy.org/math/multivariable-calculus) - dérivées partielles et gradient (pas au programme de terminale).

### Rigueur avec des matrices
- [The Matrix Calculus You Need For Deep Learning - Parr et Howard (explained.ai)](https://explained.ai/matrix-calculus/)
  - Version arXiv : [arXiv 1802.01528](https://arxiv.org/abs/1802.01528)
  - Pour dériver la backprop en version matricielle. Des maths, pas de code.
- [Deep Learning - Goodfellow, Bengio, Courville](https://www.deeplearningbook.org/) (gratuit en HTML, sans code)
  - [Chapitre 4 - Numerical Computation](https://www.deeplearningbook.org/contents/numerical.html) : 4.1 overflow/underflow et stabilité du softmax, 4.3 descente de gradient (grand oral).
  - [Chapitre 6 - Deep Feedforward Networks](https://www.deeplearningbook.org/contents/mlp.html) : entropie croisée, backprop en 6.5.
  - [Chapitre 8 - Optimization](https://www.deeplearningbook.org/contents/optimization.html) : SGD, mini-batchs, learning rate.

---

## 4. Initialisation des poids (articles originaux)

- [Glorot et Bengio (2010) - Understanding the difficulty of training deep feedforward neural networks](https://proceedings.mlr.press/v9/glorot10a.html)
  - Origine de l'initialisation "Xavier". Lire l'introduction et la section 4 (raisonnement sur les variances).
  - [PDF direct](https://proceedings.mlr.press/v9/glorot10a/glorot10a.pdf)
- [He et al. (2015) - Delving Deep into Rectifiers](https://arxiv.org/abs/1502.01852)
  - Même idée adaptée à ReLU. Lire la section 2.2.

---

## 5. Méthode et debug

- Notes Stanford CS231n - lire UNIQUEMENT ces pages (petits extraits de quelques lignes, pas le projet) :
  - [Optimization 2 - Backpropagation](https://cs231n.github.io/optimization-2/) : la backprop vue comme un graphe de calcul.
  - [Neural Networks 3 - Gradient checks](https://cs231n.github.io/neural-networks-3/#gradcheck) : différences centrées, erreur relative, pièges.
  - [Linear classification - Softmax](https://cs231n.github.io/linear-classify/#softmax) : interprétation probabiliste et astuce de stabilité.
- [A Recipe for Training Neural Networks - Andrej Karpathy](https://karpathy.github.io/2019/04/25/recipe/)
  - Méthode de vérification étape par étape, symptômes et bugs associés.

---

## 6. A ÉVITER (contiennent le code complet)

- Le livre "Neural Networks and Deep Learning" de Michael Nielsen (code complet sur MNIST dès le chapitre 1).
- La page "Neural Network Case Study" de CS231n (réseau 2 couches complet en numpy).
- Tout ce qui s'appelle "MNIST from scratch numpy" : dépôts GitHub, notebooks Kaggle, articles Medium / Towards Data Science.
- micrograd et la série vidéo "Zero to Hero" de Karpathy (il code devant toi).
- Les résumés IA des moteurs de recherche :
  - Google : cliquer sur l'onglet "Web" sous la barre de recherche (liens sans aperçu IA).
  - DuckDuckGo : désactiver les réponses IA dans les paramètres.

---

## 7. Oracles à garder sous les yeux

- Loss initiale avec des poids aléatoires : environ ln(10) = 2,30.
- Gradient checking : erreur relative vers 1e-7 = OK, vers 1e-2 = backprop fausse.
- Surapprendre 10 images : la loss doit tomber presque à 0.
- Régression softmax seule : environ 92 % sur le test.
- MLP 784 - 128 - 10 : objectif 97-98 % sur le test.

---

## TL;DR

- Données : page MNIST de LeCun (section du bas) + miroir ossci-datasets.
- Outils : doc Python (gzip, struct) et numpy (Broadcasting d'abord).
- Maths : 3Blue1Brown, puis Parr et Howard, avec Goodfellow chapitres 4, 6 et 8 comme référence.
- Debug : CS231n (Optimization 2, Gradient checks) + la Recipe de Karpathy.
- Initialisation : Glorot (2010) et He (2015).
- Interdit : Nielsen, CS231n Case Study, tutos "from scratch", aperçus IA.
