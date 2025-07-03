
# Voir PackML document: 
Admin.Alarm[#].AckDateTime
The Timestamp for when the alarm was first triggered.
The Timestamp for when the alarm was acknowledged by the operator or cleared.

Il faut quand même garder l'alarme 0 pour le diagnostique.




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


## Enumération IEC 61131-3 pour la machine d'états

```iecst
TYPE E_EventStates :
(
    eIdle                   := 99,
    eAlarmActive            := 10,
    eAuto_Acknowledge       := 20,
    eManualAcknowledge      := 30,
    eSetAckDateTime         := 40,
    eAckDateTime            := 50,
    eWaitForAckAlarmTrig    := 60,
    eAlarmCleared           := 70
) DINT := eIdle;
END_TYPE
```


### Exemple d'utilisation avec une instruction `CASE OF` qualifiée

```iecst
CASE EventState OF
    E_EventStates.eIdle:
        // Actions pour l'état Idle
    E_EventStates.eAlarmActive:
        // Actions pour l'état AlarmActive
    E_EventStates.eAuto_Acknowledge:
        // Actions pour l'état Auto_Acknowledge
    E_EventStates.eManualAcknowledge:
        // Actions pour l'état ManualAcknowledge
    E_EventStates.eSetAckDateTime:
        // Actions pour l'état SetAckDateTime
    E_EventStates.eAckDateTime:
        // Actions pour l'état AckDateTime
    E_EventStates.eWaitForAckAlarmTrig:
        // Actions pour l'état WaitForAckAlarmTrig
    E_EventStates.eAlarmCleared:
        // Actions pour l'état AlarmCleared
END_CASE
```




Pour formater une date en Suisse (français) avec `toLocaleString` en JavaScript :

```js
const date = new Date();
const chDateString = date.toLocaleString('fr-CH');
console.log(chDateString); // Exemple : "02.07.2024, 00:00:00"
```

- `'fr-CH'` : code de langue pour le français (Suisse).
- Les formats changent selon la région : points comme séparateurs de date, virgule pour séparer la date et l'heure.
