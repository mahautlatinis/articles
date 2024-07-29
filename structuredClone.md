# structuredClone() : La nouvelle ère de la copie d'objets en JavaScript

Avant de plonger dans les détails de cette fonction, il est crucial de comprendre les concepts fondamentaux qui sous-tendent la manipulation des objets en JavaScript. Dans cet article, nous explorerons :
La mutabilité des objets : comment et pourquoi les objets peuvent être modifiés après leur création.
Le passage par référence : les implications de travailler avec des références plutôt que des valeurs.
La différence entre une copie superficielle (shallow copy) et une copie profonde (deep copy).
Les techniques traditionnelles de copie d'objets et leurs limites.

Ces notions sont essentielles pour apprécier pleinement la puissance et l'utilité de structuredClone(). Cette fonction, introduite récemment dans le langage, offre une solution élégante à de nombreux problèmes qui ont longtemps frustré les développeurs JavaScript.
Que vous soyez un développeur débutant cherchant à consolider vos bases ou un programmeur expérimenté désireux d'optimiser vos pratiques, cet article vous fournira des insights précieux sur la gestion des objets en JavaScript. Embarquons ensemble dans ce voyage au cœur de la copie d'objets, avec structuredClone() comme destination finale !

## La mutabilité des objets

Définition : Un objet mutable est un objet dont l'état peut être modifié après sa création.
Contrairement aux types primitifs (comme les nombres ou les chaînes de caractères), les objets peuvent être altérés sans créer une nouvelle instance.

```JavaScript
let obj = { a: 1 };
obj.a = 2; // L'objet est modifié
```

## Implications de la mutabilité

- Effets de bord : La mutabilité peut conduire à des effets de bord inattendus, surtout lorsque des objets sont partagés entre différentes parties d'un programme.

- Débogage complexe : Les bugs liés à la mutabilité peuvent être difficiles à tracer, car l'état d'un objet peut changer à plusieurs endroits du code.

- Performance : La mutabilité permet souvent des opérations plus rapides car il n'est pas nécessaire de créer de nouveaux objets pour chaque modification.

## Le passage par référence

Lorsqu'on assigne un objet à une variable ou qu'on le passe en argument à une fonction, c'est la référence de l'objet qui est transmise, pas une copie de ses valeurs. Cela signifie que si l'on modifie cette objet dans une sous fonction, il sera modifié partout ailleurs.

```JavaScript
let obj1 = { x: 10 };
let obj2 = obj1; // obj2 référence le même objet que obj1

obj2.x = 20;
console.log(obj1.x); // 20 - obj1 est également modifiés
```

## Shallow copy vs Deep copy

Pour éviter de modifier accidentellement un objet original, on peut créer des copies. Il existe deux types de copies :
Shallow copy (copie superficielle) : Crée un nouvel objet avec les mêmes propriétés de premier niveau que l'original, mais les objets imbriqués restent référencés.
Deep copy (copie profonde) : Crée un nouvel objet en copiant récursivement toutes les propriétés, y compris les objets imbriqués.

## Techniques de copie d'objets

### Shallow copy

#### Shallow copy avec Object.assign()

```JavaScript
let original = { a: 1, b: { c: 2 } };
let copie = Object.assign({}, original);

copie.a = 3;
copie.b.c = 4;

console.log(original); // { a: 1, b: { c: 4 } }
console.log(copie);    // { a: 3, b: { c: 4 } }
```

#### Shallow copy avec l'opérateur spread

```JavaScript
Copylet original = { a: 1, b: { c: 2 } };
let copie = { ...original };
// Même résultat qu'avec Object.assign()
```

#### Array.slice() pour les tableaux

```JavaScript
let tableauOriginal = [1, 2, [3, 4]];
let copieTablau = tableauOriginal.slice();
```

### Deep copy

#### La récursion manuelle

```JavaScript
function deepCopy(obj) {
    if (typeof obj !== 'object' || obj === null) return obj;

    let copie = Array.isArray(obj) ? [] : {};

    for (let key in obj) {
        if (obj.hasOwnProperty(key)) {
            copie[key] = deepCopy(obj[key]);
        }
    }

    return copie;
}
```

#### avec JSON.parse(JSON.stringify())

Cette méthode était couramment utilisée, mais elle a des limitations (perte des méthodes, des undefined, etc.).

```JavaScript
let original = { a: 1, b: { c: 2 } };
let copie = JSON.parse(JSON.stringify(original));

copie.b.c = 4;
console.log(original); // { a: 1, b: { c: 2 } }
console.log(copie);    // { a: 1, b: { c: 4 } }
```

#### Problème avec JSON.parse(JSON.stringify())

Lorsqu'on utilise JSON.parse(JSON.stringify()) pour effectuer une copie profonde d'un objet contenant des références circulaires, une erreur est générée :

```JavaScript
let copie = JSON.parse(JSON.stringify(objA));
// Uncaught TypeError: Converting circular structure to JSON
```

#### Limites de JSON.parse(JSON.stringify())

Au-delà des références circulaires, JSON.parse(JSON.stringify()) présente plusieurs limitations :

##### Perte de types de données spécifiques à JavaScript :

- undefined est converti en null ou simplement omis
- NaN, Infinity, et -Infinity sont convertis en null
- Les dates sont converties en chaînes de caractères
- Les Map, Set, WeakMap, et WeakSet sont convertis en objets vides {}

```JavaScript
let obj = {
  a: undefined,
  b: NaN,
  c: new Date(),
  d: new Map([['key', 'value']])
};
let copie = JSON.parse(JSON.stringify(obj));
console.log(copie);
// { b: null, c: "2024-07-29T12:34:56.789Z" }
// 'a' est omis, 'd' est un objet vide {}
```

- Perte des méthodes et prototypes : Les fonctions et méthodes sont complètement ignorées.

```JavaScript
class MaClasse {
  constructor(valeur) {
    this.valeur = valeur;
  }
  maMethode() {
    console.log(this.valeur);
  }
}

let instance = new MaClasse(42);
let copie = JSON.parse(JSON.stringify(instance));
console.log(copie); // { valeur: 42 }
// copie.maMethode() // Erreur : maMethode n'est pas une fonction
```

- Symboles ignorés : Les propriétés définies avec des symboles sont ignorées.

```JavaScript
let sym = Symbol('monSymbole');
let obj = { [sym]: "valeur symbolique" };
let copie = JSON.parse(JSON.stringify(obj));
console.log(copie); // {}
```

- Performances : Pour de grands objets, la sérialisation en chaîne JSON puis la désérialisation peut être coûteuse en termes de performances.

## Méthode moderne : structuredClone()

Introduite récemment, cette méthode offre une copie profonde plus robuste et performante.

```JavaScript
let original = { a: 1, b: { c: 2 } };
let copie = structuredClone(original);

copie.b.c = 4;
console.log(original); // { a: 1, b: { c: 2 } }
console.log(copie);    // { a: 1, b: { c: 4 } }
```

structuredClone() gère correctement les références circulaires et supporte un plus large éventail de types de données que JSON.parse(JSON.stringify()).

### Solution avec structuredClone()

```JavaScript
structuredClone() gère correctement les références circulaires :
let copie = structuredClone(objA);
console.log(copie.ref.ref === copie); // true
```

## Conclusion

La compréhension de la mutabilité des objets et des différentes techniques de copie est cruciale pour éviter les bugs subtils dans vos applications JavaScript. La méthode structuredClone() représente une avancée significative pour réaliser des copies profondes de manière fiable et efficace.
En tant que développeurs, il est important de choisir la méthode appropriée selon vos besoins spécifiques, en gardant à l'esprit les performances et les particularités de chaque approche.
