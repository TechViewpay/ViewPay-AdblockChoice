# ViewPay-AdblockChoice

Ce guide a pour objectif de vous guider dans la mise en place du widget JS Adblock Choice de ViewPay dans votre site web.

Pour rappel, Viewpay est une solution de micro-paiement par l'attention publicitaire, qui permet à l'utilisateur de débloquer un contenu ou un service premium en regardant volontairement une publicité.
L'Adblock Choice de ViewPay a été pensé pour répondre aux besoins d'éditeurs ne disposant pas de solution d'adblock wall, mais souhaitant bloquer l'accès au contenu de leur site aux visiteurs ayant un Adblocker activé : le visiteur a le choix de désactiver son adblock ou bien d’accéder au site pendant un temps défini en visualisant une pub ViewPay.

## Chargement du Javascript
```html
<script defer type="text/javascript" src="https://cdn.jokerly.com/scripts/[Yourtag]_abChoice.js"></script> 
```
Le fichier [Yourtag]_abChoice.js est le seul fichier nécessaire à appeler, celui-ci appelera les éléments nécessaires au moment voulu pour gérer le wall.
Ces fichiers peuvent être installés sur vos serveurs afin d'éviter qu'il soit facilement bloqué par les solutions d’adblock et que ViewPay soit identifié comme acteur anti-adblock.
On peut également l’intégrer dans d'autres fichiers JS indispensables afin qu'il soit plus difficilement bloquable.

Pour récupérer votre tag, prenez contact avec votre référent ViewPay.


## Options
Voici une liste non exhaustive des paramètres et options que vous pouvez activer pour votre intégration de l'Adblock Choice : 

- Bouton abonnement dans le paywall + lien de connection si l'utilisateur est déjà abonné.
- Durée de déblocage du wall offerte par ViewPay.
- Retarder l'affichage du wall pour laisser l'utilisateur lire les premières lignes.
- Masquer le wall si aucune publicité payante n'a été trouvée.
