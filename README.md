# useEffect et _lazy initial state_

### document.title

Nous allons modifier `document.title` en fonction du nombre des produits sur la liste des courses.
Nous allons y mettre soit 'Préparez votre liste des courses' (si elle est vide), soit 'Vous avez .. produit(s) sur votre liste des courses'.

L'élément `title` se trouve dans la partie `head` de notre document HTML. C'est en dehors du _scope_ de notre application. Le titre devrait se mettre à jour à chaque fois où le nombre de produits sur la liste change. Nous allons dons utiliser le hook `useEffect` avec le deuxième paramètre `[shopping.length]`.

```javascript
// src/components/ShoppingApp.js
import React, { useState, useEffect } from "react"
/* comme avant */

const ShoppingApp = ({mode}) => {
  const [shopping, setShopping] = useState(["cumin", "curry"])

  /* comme avant */

  useEffect(() => {
    document.title =
      shopping.length === 0
        ? `Préparez vos courses`
        : `${shopping.length} produit(s) sur votre liste des courses`
  }, [shopping.length])

  return /* comme avant */

export default ShoppingApp
```

Maintenant à chaque fois que `ShoppingApp` _render_ le titre du document est modifié

---

### localStorage

Nous avons utiliser `localStorage` afin d'enregistrer dans la mémoire du navigateur notre liste des courses et la récuperer à la prochaine visite (après rechargement de la page).

Un petit rappel sur l'utilisation de `localStorage` :

- `localStorage.setItem("colorMode", mode)` - enregistre la valeur de mode dans l'objet `localStorage`
- `localStorage.getItem("colorMode")` - permet de recupérer la valeur enregistrée sous la clé `"colorMode"`
- localStorage enregistre tout en format de `string`
- afin d'enregistrer un objet nous utilisons le format JSON :
  - `JSON.stringify(myObjet)` transforme objet `myObjet` en string format JSON
  - `JSON.parse(myJSONString)` transforme `myJSONString` en objet JavaScript

![](https://wptemplates.pehaa.com/assets/alyra/localStorage.png)

Dans notre application, nous avons besoin d'enregister la valeur de `shopping` dans `localStorage` à chaque fois que `shopping` change :

```javascript
useEffect(() => {
  localStorage.setItem("myShoppingList", JSON.stringify(shopping))
}, [shopping])
```

Nous allons ensuite besoin de la valeur stockée dans localStorage pour la passer en tant que la valeur initiale de `shopping` :

```javascript
const [shopping, setShopping] = useState( /* ici !! */ )`
```

Alors :

```javascript
const [shopping, setShopping] = useState( JSON.parse(localStorage.getItem('myShoppingList')) || [] )
```

La valeur initiale de shopping est utilisée uniquement une fois, au moment ou le component monte. Néanmoins, l'expression `JSON.parse(localStorage.getItem('myShoppingList')) || []` sera évaluée à chaque render. Pour y rémédier et améliorer la performance (l'échange avec `localStorage` peuvent être coûteuse au niveau de la performance), nous allons passer une fonction dans `useState` :

```javascript
const [shopping, setShopping] = useState(
  () => JSON.parse(localStorage.getItem("myShoppingList")) || []
)
```

---

✅

Nous appelons cela _lazy initial state_ ou état local initial paresseux.

```javascript
const [shopping, setShopping] = useState(expensiveOperationFunction()) // pas bien 👎
```

par contre :

```javascript
const [shopping, setShopping] = useState(() => expensiveOperationFunction()) //  bien 👍
```

ou simplement

```javascript
const [shopping, setShopping] = useState(expensiveOperationFunction) //  bien 👍
```

---

## Exercice :

Utiliser la même approche et modifier le fichier `src/context/ModeContext.js` afin de profiter de localStorage pour stocker la valeur de `mode`.
