# ViewPay-AdblockWall

Ce guide a pour objectif de vous guider dans la mise en place du widget JS Adblock wall de ViewPay dans votre site web.

Pour rappel, Viewpay est une solution de micro-paiement par l'attention publicitaire, qui permet à l'utilisateur de débloquer un contenu ou un service premium en regardant volontairement une publicité.
L'Adblock wall de ViewPay a été pensé pour répondre aux besoins d'éditeurs ne disposant pas de solution d'adblock wall, mais souhaitant bloquer l'accès au contenu de leur site aux visiteurs ayant un Adblocker activé : le visiteur a le choix de désactiver son adblock ou bien d’accéder au site pendant un temps défini en visualisant une pub ViewPay.

## Chargement du Javascript
```html
<script type="text/javascript" src="https://cdn.jokerly.com/scripts/adblockWall.js?[function_name]&[duration]"></script> 
```
Le fichier adblockWall.js est le seul fichier nécessaire à appeler, celui-ci appelera les éléments nécessaires au moment voulu pour gérer le wall.
Ces fichiers doivent être installés sur vos serveurs afin d'éviter qu'il soit facilement bloqué par les solutions d’adblock et que ViewPay soit identifié comme acteur anti-adblock.
On peut également l’intégrer dans d'autres fichiers JS indispensables afin qu'il soit plus difficilement bloquable.

NB: Il faut placer le script le plus haut possible dans la page afin d’optimiser son temps de chargement.

## Création d’un div pour accueillir la publicité
Ajoutez un div avec l’id "cadreJokerlyADS" dans votre page. 
```html
<div id="cadreJokerlyADS"></div>
```
## Paramètres du JS

Dans l'URL ci dessus, vous devez renseigner 2 paramètres qui permettront de configurer correctement l'adblock wall : 

#### 1. function_name

Ici remplacez la macro ```[function_name]``` par le nom que vous donnerez à la fonction d'initialisation de l'adblock wall. Dans la suite de la documentation, nous utiliserons le nom ```ABWinit```

#### 2. duration

Ici vous pouvez définir la durée en minutes que vous offrez au lecteur en contrepartie de la visualisation d'une vidéo ViewPay. Par exemple si vous renseignez 15, l'Adblock Wall ne s'affichera plus pendant 15 minutes, pour laisser le lecteur profiter du contenu qu'il a obtenu en l'échange de la visualisation de la vidéo publicitaire. Si vous souhaitez débloquer le site pour la session de l'utilisateur, laissez simplement ce champ vide.

## Fonctions Javascript et lancement du Paywall

Les fonctions et callbacks fournis par Viewpay sont les suivants : 

Fonctions : 
ABWinit() - appelée typiquement au onload d’une page pour initialiser l'Adblock Wall. Si le code JS détecte un Adblocker, alors il affichera une popup modale qui empêche la navigation sur le site. Si l'utilisateur n'a pas d'Adblocker, alors le JS ne fait rien. 

Callbacks :
- VPnoAds() - callback appelé si Viewpay n’a aucune publicité à servir. Vous pouvez décider de masquer totalement l'Adblock Wall si aucune publicité n'a été trouvée.
- VPplayAds() - callback de notification appelé lorsque l’utilisateur démarre la vidéo.
- VPcompleteAds() - appelé quand l’utilisateur a fini le parcours.
- VPcloseAds() - appelé si l’utilisateur ferme le parcours.

Les fonctions JS vont permettre au paywall de s'afficher correctement et de faire le parcours nécessaire à celui-ci.
```javascript
<script>
	function ABWinit(){
	var wall = new ABW({
		site_id: 'd0c656ec794afc3e',
		load_callback:existAds,
		noads_callback: noAds,
		complete_callback:completeAds,
		close_callback:closeAds,
		play_callback : playAds
		});
	}
function existAds(){
	}
function noAds(){
	$("#btnShowCadre").css("display","none");
	$("#btnTestCadre").css("display","block");
	console.log("noAds");
	}	
function completeAds(){
	console.log("complete video");
	}	
function closeAds(){
	console.log("close");
	}	
function playAds(){
		}		
	function loadAds(){	
JKFBASQ.loadAds();	
}

</script>
```
