
Pour convertir un nombre de secondes depuis le 1.1.1970 (timestamp Unix) en une date lisible en texte avec JavaScript :

```js
const seconds = 1719936000; // exemple de timestamp en secondes
const date = new Date(seconds * 1000); // convertir en millisecondes
const dateString = date.toLocaleString(); // format texte local
console.log(dateString);
```

- `seconds * 1000` : conversion en millisecondes.
- `toLocaleString()` : format texte selon la langue de l'utilisateur.

Exemple de sortie :  
`02/07/2024, 00:00:00`


1: object
Trigger: false
ID: 0
Value: 0
Message: ""
Category: 0
DateTime: 0
AckDateTime: 0


Pour obtenir la taille d'un objet si c'est un tableau en JavaScript :

```js
const arr = [1, 2, 3, 4];
if (Array.isArray(arr)) {
    console.log(arr.length); // Affiche 4
}
```

- `Array.isArray(arr)` : vérifie si `arr` est un tableau.
- `arr.length` : donne le nombre d'éléments du tableau.

Pour incrémenter une variable `t` tant qu'elle est supérieure à 0 **et** qu'une autre condition `x` est vraie en JavaScript :

```js
let t = 5; // valeur initiale
let x = true; // autre condition
while (t > 0 && x) {
    t++;
    // Attention : ceci crée une boucle infinie si x reste true et t ne diminue pas
    // Pour éviter cela, il faut modifier t ou x dans la boucle ou ajouter une condition d'arrêt
}
```

**Remarque :**  
Si vous souhaitez que la boucle s'arrête, il faut modifier la condition ou la façon dont `t` ou `x` évolue. Par exemple, pour décrémenter :

```js
let t = 5;
let x = true;
while (t > 0 && x) {
    t--;
    console.log(t);
    // x peut aussi être modifié ici pour arrêter la boucle selon vos besoins
}
```

// Get Base message
var newMsg = msg;

// Convert the object to an array
newMsg.payload = msg.payload.value;

// Convert date in second to string.
// It time is > 0.
let timeIndex = 0;
let newDate;
let newDateString;
while ((timeIndex < newMsg.payload.length) &&
       (newMsg.payload[timeIndex].DateTime > 0)){
    newDate = new newMsg.payload[timeIndex].DateTime;
    newDateString = newDate.ToLocaleString();
    newMsg.payload[timeIndex].DateTime = newDateString;
    timeIndex++
    }


return msg;

Pour convertir un temps en millisecondes en une date et heure courte en JavaScript :

```js
const ms = 1719936000000; // exemple en millisecondes
const date = new Date(ms);
const shortDateTime = date.toLocaleString(undefined, {
    dateStyle: 'short',
    timeStyle: 'short'
});
console.log(shortDateTime); // Exemple : "02/07/2024, 00:00"
```

- `dateStyle: 'short'` et `timeStyle: 'short'` : affichent la date et l'heure au format court selon la langue de l'utilisateur.

// Get Base message
var newMsg = msg;

// Convert the object to an array
newMsg.payload = msg.payload.value;
// Convert date in second to string.
// It time is > 0.
let timeIndex = 0;
let newDateSecond;
let newDate;
let newDateStringLocal;
while ((timeIndex < newMsg.payload.length) &&
       (newMsg.payload[timeIndex].DateTime > 0))
       {
       newDateSecond = newMsg.payload[timeIndex].DateTime;
       newDate = new Date(newDateSecond * 1000);
       newDateStringLocal = newDate.toLocaleString();

       newMsg.payload[timeIndex].DateTime = newDateStringLocal;
        timeIndex++;
        flow.set("FlowTimeIndex", timeIndex);
        flow.set("DateString", newDateStringLocal);
       }

return newMsg;