# Comprendre le Garbage Collection (GC) en JS et le moteur V8 de Google

La gestion de la mémoire est un aspect crucial de la programmation, influençant à la fois la performance et la stabilité des applications. En JavaScript, cette gestion repose sur un mécanisme appelé garbage collection (GC).

## Qu’est-ce que le Garbage Collection en JavaScript ?

Le garbage collection en JavaScript est un processus automatisé qui gère l’allocation et la libération de la mémoire. Il identifie et supprime les objets qui ne sont plus nécessaires, libérant ainsi de la mémoire et évitant les fuites de mémoire. Ce processus repose sur le concept de reachability (accessibilité).

## V8 : Le Moteur JavaScript de Google Chrome

V8 est le moteur JavaScript utilisé par Google Chrome et Node.js. Il a été développé par Google pour améliorer les performances de JavaScript dans le navigateur Chrome. V8 compile le code JavaScript en code machine natif, ce qui permet une exécution rapide et efficace.

## Les Concepts Clés du Garbage Collection

### Objets Reachables et Non-Reachables

- **Reachables (Accessibles)** : Un objet est considéré comme accessible s’il peut être atteint directement ou indirectement par une chaîne de références à partir des racines du programme (par exemple, des variables globales ou des variables locales actuellement utilisées).
- **Non-Reachables (Non-Accessibles)** : Un objet est considéré comme non-accessible s’il ne peut être atteint par aucune chaîne de références à partir des racines du programme. Ces objets sont candidats à la suppression par le GC.

## Les Algorithmes de Garbage Collection Utilisés par Google Chrome

Les principaux algorithmes sont :

### Marquage et Balayage (Mark-and-Sweep)

- **Marquage** : V8 commence par marquer tous les objets accessibles. Il parcourt les racines du programme et marque tous les objets qu’il peut atteindre.
- **Balayage** : Après le marquage, V8 balaie la mémoire pour identifier les objets non marqués. Ces objets non-accessibles sont alors supprimés.

### Marquage et Compactage (Mark-and-Compact)

Après le processus de marquage, V8 déplace les objets accessibles pour les regrouper, réduisant ainsi la fragmentation de la mémoire. Cette étape de compactage améliore l’efficacité de la gestion de la mémoire en facilitant l’allocation de nouveaux objets.

### Scavenging

Utilisé pour la collecte des objets de courte durée (new space). V8 divise la mémoire en deux espaces : un pour les objets nouvellement créés (new space) et un pour les objets de longue durée (old space). Le Scavenging se concentre sur le new space, en déplaçant les objets accessibles vers un espace libre et en supprimant les objets non-accessibles.

## Identification des Objets Non-Reachables

Le processus de garbage collection en JavaScript repose sur l’identification des objets non-reachables, ce qui se fait en plusieurs étapes :

### Initialisation des Racines

Le GC commence par identifier les racines du programme. Ces racines sont les points de départ pour le marquage des objets accessibles. Elles incluent :

- Les variables globales
- Les piles d’appels
- Les registres

### Marquage des Objets Accessibles

À partir des racines, le GC parcourt la mémoire et marque tous les objets qu’il peut atteindre. Ce marquage se fait en suivant les références entre objets. Par exemple, si un objet A référence un objet B, alors B est marqué comme accessible si A l’est aussi.

Lorsque le GC visite un objet via une référence, il le marque comme accessible. Le marquage peut être implémenté de plusieurs façons, par exemple en utilisant un bit de marquage dans l’en-tête de chaque objet. Ce bit indique si l’objet a été visité et est accessible.

### Parcours des Références

Le GC continue à suivre les références récursivement jusqu’à ce qu’il n’y ait plus d’objets à marquer. À ce stade, tous les objets accessibles sont marqués. À partir des racines, le GC utilise une traversée de graphe pour parcourir les références entre objets. Ce parcours peut être implémenté de différentes manières, mais l’objectif est de suivre chaque référence à partir des racines pour trouver tous les objets accessibles.

### Balayage des Objets Non-Marqués

Après le marquage, le GC balaie la mémoire pour identifier les objets non marqués. Ces objets non-accessibles, ou non-reachables, sont alors considérés comme inutiles et peuvent être supprimés.
