# ViewPay-AdblockWall

Ce guide a pour objectif de vous guider dans la mise en place du widget JS Adblock wall de ViewPay dans votre site web.

Pour rappel, Viewpay est une solution de micro-paiement par l'attention publicitaire, qui permet à l'utilisateur de débloquer un contenu premium en regardant une publicité. L'Adblock wall de ViewPay a été fait pour répondre aux besoins d'éditeurs n'ayant pas de solution existante mais souhaitant bloquer le contenu de leur site aux visiteurs possédant un Adblocker, ou en leur permettant de débloquer une courte session grâce à la visualisation d'une pub ViewPay.

## Chargement du Javascript
```html
<script type="text/javascript" src="https://cdn.jokerly.com/scripts/adblockWall.js?[function_name]&[duration]"></script> 
```
Le fichier adblockWall.js est le seul fichier nécessaire à appeler, celui-ci fera ensuite le travail afin d'appeler les éléments nécessaires pour un moment donné.
Ces fichiers sont installés sur notre CDN afin de vous garantir d'avoir toujours la dernière version. Cependant, pour éviter que notre fichier soit facilement bloqué, il serait préférable de l'héberger sur votre plateforme voir intégré dans d'autres fichiers JS indispensables afin qu'il soit plus difficilement bloquable. 

NB: Il faut placer le script le plus haut possible dans la page afin d’optimiser son temps de chargement.

## Création d’un div pour accueillir la publicité
Ajoutez un div avec l’id "cadreJokerlyADS" dans votre page. 
```html
<div id="cadreJokerlyADS"></div>
```
## Paramètres du JS

Dans l'URL ci dessus, vous devez renseigner 2 paramètres qui permettront de configurer correctement l'adblock wall : 

#### 1. function_name

Ici remplacez la macro ```[function_name]``` par le nom que vous donnerez à la fonction d'initialisation de l'adblock wall. Dans la suite de la documentation, nous utiliserons le nom ```AdblockWallinit```

#### 2. duration

Ici vous définissez la durée en minutes que vous offrez au lecteur en contrepartie de la visualisation d'une vidéo ViewPay. Par exemple si vous renseignez 15, l'Adblock Wall ne s'affichera plus pendant 15 minutes, pour laisser le lecteur profiter du contenu qu'il a obtenu en l'échange de la visualisation de la vidéo publicitaire.

## Fonctions Javascript et lancement du Paywall

Les fonctions et callbacks fournis par Viewpay sont les suivants : 

Fonctions : 
AdblockWallinit() - appelée typiquement au onload d’une page pour initialiser l'Adblock Wall. Si le code JS détecte un Adblocker, alors il affichera une popup modale qui empêche la navigation sur le site. Si l'utilisateur n'a pas d'Adblocker, alors le JS ne fait rien. 

Callbacks :
VPnoAds() - callback appelé si Viewpay n’a aucune publicité à servir. Vous pouvez décider de masquer totalement l'Adblock Wall si aucune publicité n'a été trouvée.
VPplayAds() - callback de notification appelé lorsque l’utilisateur démarre la vidéo.
VPcompleteAds() - appelé quand l’utilisateur a fini le parcours.
VPcloseAds() - appelé si l’utilisateur ferme le parcours.

Les fonctions JS vont permettre au paywall de s'afficher correctement et de faire le parcours nécessaire à celui-ci.
```javascript
<script>
	function AdblockWallinit(){
	var wall = new ViewPayWall({              // faire changer le nom en AdblockWall
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
