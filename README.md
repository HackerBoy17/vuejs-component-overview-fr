# vuejs-component-overview-fr

Une récapitulation au sujet des composants Vue.JS

Un fichier .vue est composé des trois sections suivantes:
```
<template>
    <!-- comprend le HTML qui sera rendu par le moteur de Vue.JS -->
</template>

<script>
    /* comprend la déclaration du composant (options) */
</script>

<style scoped lang="scss">
    /* comprend les styles propres au composant */
</style>
```

## Déclaration du composant
La déclaration du composant se fait à l'aide d'options passées à l'aide d'un objet Javascript. Par exemple:

```
<script>
export default {
  name: 'HelloWorld',
  data() {
    return {
      compteur: 0,
    };
  },
};
</script>
```

Les options que nous avons vues à date sont:
* **data**: méthode appelée à la création du composant qui permet de déclarer les propriétés réactives (PR) de base du composant. Cette méthode doit retourner un objet dont les clés seront les noms de ces PR qui prendront les valeurs respectives de l'objet.
* **name**: nom du composant qui sera utilisé dans le template parent.
* **components**: déclaration des composants enfants utilisés par le composant parent.
* **props**: déclaration des valeurs passées d'un parent à son enfant.
* **computed**: Déclaration d'une ou plusieurs PR sous forme de méthodes dont les valeurs seront mises en cache.
* **methods**: similaire à computed, mais appellées à chaque cycle. Permettent le passage d'arguments.

## Rappel sur les propriétés réactives (PR)

* Une PR a un nom (une chaîne de caractère) et une valeur.
* Vue.JS «traque» les PR. (Big Brother is watching PRs) Quand les valeurs changent, le «template» est rendu à nouveau.
* Il existe différentes façon de définir des PR: data, props, computed, methods...
* Les PR sont propres à un composant et ne sont pas accessibles à l'extérieur de celui-ci.

## Composant parent-enfant

* Un composant (parent) qui en utilise un autre (enfant) doit être déclaré par le parent grâce à l'option `components`.
* Cette option prend un objet dont les clés seront le nom du composant et les valeurs les composants importés. Exemple:

```
<script>
import CompEnfant from '@/components/enfant';
export default {
  name: 'CompParent',
  components: {
    CompEnfant,
  },
  ...
};
</script>
```
* Dans le template du parent, l'enfant peut être appelé comme s'il s'agissait d'une balise HTML:
```
<template>
    <comp-enfant></comp-enfant>
</template>
```
* Notez la relation entre le nom du composant (CompEnfant) et son utilisation dans le template (comp-enfant)!

## Passer des valeurs du parent à l'enfant.

Pour passer des valeurs du parent à l'enfant, on utilise l'option `props` dans le composant enfant. Il s'agit d'un tableau comprenant le nom des valeurs à passer. Exemple:
```
<script> /* Composant enfant */
export default {
  name: 'CompEnfant',
  props: ['xyz', 'abc'],
  ...
};
</script>
```
Dans le composant parent, on utilise la notion d'attributs HTML pour passer les valeurs de l'enfant vers le parent:
```
<template>
    <comp-enfant xyz="123" abc="456"></comp-enfant>
</template>
```
Dans cet exemple:
* une PR nommée xyz prendra la valeur 123
* une seconde PR nommée abc prendra la valeur 456 
* Ce sont des chaînes de caractères (string)
* Ces valeurs sont statiques (quoique réactives, elles ne changeront pas car il s'agit de littéraux)

Pour passer une valeur réactive, il suffit d'utiliser le _binding_. Exemple:
```
<template>
    <comp-enfant :xyz="x + y + z" abc="456"></comp-enfant>
</template>
```
Dans cet exemple:
* on assume que le parent possède 3 PR (x, y et z).
* la PR `xyz` (de l'enfant) sera recalculée à chaque fois que x, y ou z (du parent) change.
* Il s'agit d'une expression, donc, le résultat n'est pas nécessairement un string, mais pourrait être ici un nombre.


## Passer des valeurs de l'enfant au parent

Vue.JS ne permet pas de modifier (on dit muter) les valeurs des props. À la place, on émet des événements (de l'enfant) qui seront attrapés par le parent.

Dans le composant enfant (exemple avec passage de paramètres):
```
  ...
  const param1 = 1;
  const param2 = 2;
  this.$emit('ilsepasseuntruc', param1, param2);
  ...
```

Dans le composant parent, on lie l'événement dans le template:
```
<template>
    <comp-enfant v-on:ilsepasseuntruc="uneMethodeQuiGereLEvent"
      :xyz="x + y + z" abc="456"></comp-enfant>
</template>
```

Et finalement, on déclare généralement une méthode qui prendra en charge la logique de l'événement.

```
<script>
import CompEnfant from '@/components/enfant';
export default {
  name: 'CompParent',
  components: {
    CompEnfant,
  },
  methods: {
    uneMethodeQuiGereLEvent(param1, param2) {
      /* param1 vaut 1 et param2 vaut 2 ... */
    },
  },
  ...
};
</script>
```

