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

## Fonctions Javascript et lancement du Paywall

Les fonctions et callbacks fournis par Viewpay sont les suivants : 

Fonctions : 
VPinitVideo() - appelée typiquement au onload d’une page pour vérifier la disponibilité d’une publicité. Cette fonction appelle le callback VPexistAds() si une ou plusieurs vidéos ont été trouvées, et appelle le callback VPnoAds() si aucune n’a été trouvée pour cet utilisateur.
VPloadAds() - appelée une fois que vous avez eu la confirmation qu’une pub existe (via le callback VPexistAds()). Permet d’ouvrir le système Viewpay.

Callbacks :
VPexistAds() - callback appelé si Viewpay a au moins une publicité à servir. Cette fonction vous permet d’afficher le bouton pour lancer Viewpay si vous recevez un OK. Si KO le callback VPnoAds() est appelé.
VPnoAds() - callback appelé si Viewpay n’a aucune publicité à servir.
VPplayAds() - callback de notification appelé lorsque l’utilisateur démarre la vidéo.
VPcompleteAds() - appelé quand l’utilisateur a fini le parcours.
VPcloseAds() - appelé si l’utilisateur ferme le parcours.

ViewPay est une solution qui répond aux appels reçus avec agilité et rapidité, il est donc avantageux pour vous de situer chacun des appels au début du chargement de la page Html.

Les fonctions JS vont permettre au paywall de s'afficher correctement et de faire le parcours nécessaire à celui-ci.
```javascript
<script>
	function AdblockWallinit(){
	var wall = new ViewPayWall({
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

## Sécurisation du callback VPcompleteAds
L’ajout de cette fonctionnalité n’est pas obligatoire mais fortement conseillé afin de sécuriser l’utilisation du système Viewpay dans votre site.
Lorsqu’une transaction est validée par le serveur Viewpay, une requête HTTP va être  effectuée par notre serveur vers une URL que vous nous aurez fournie, qui vous prévient qu’une transaction a été effectuée. Vous pouvez ainsi vous assurer que l’utilisateur a bien vu la publicité et est éligible au bien que vous voulez lui offrir. 

Format de la requête
Lorsque le parcours Viewpay s’est déroulé avec succès, notre serveur appellera une URL de callback. (configurée dans votre compte Viewpay : voir avec votre contact chez Viewpay)

L’URL aura la forme suivante :
```html
http:///your.callback.url/callback_page.html?ref=&editor_id=&challenge=&shared_secret=
```
Votre URL de callback est l’URL qui est renseignée dans votre compte Viewpay.
Les paramètres ref, et challenge sont des paramètres spécifiques à des éditeurs anciens de Viewpay, et ne sont pas renseignés pour une nouvelle intégration. 
Le paramètre editor_id correspond au site_id qui vous a été communiqué avec la documentation d’intégration.
Le paramètre shared_secret est une clé cryptée définie dans votre compte Viewpay qui peut vous être envoyée afin de sécuriser la transaction (à voir avec votre contact chez Viewpay). (non obligatoire)
Vous pouvez éventuellement nous envoyer une clé spécifique à chaque utilisateur en renseignant dans l’initialisation de Viewpay un nouveau paramètre : secureId={yourSecureId}. Cette clé vous sera renvoyée avec le callback dans un paramètre avec un nom différent : dynamicParameter={yourSecureId}

Exemple : 
```javascript
// Dans l’init :
JKFBASQ.init({
	site_id: 'your_id',
	load_callback: VPexistAds,
	noads_callback: VPnoAds,
	complete_callback: VPcompleteAds,
	close_callback: VPcloseAds,
	play_callback : VPplay,
	cover:true,
	display_paywall  : true //True : Affiche le paywall, False : Pas de Paywall
	codeCategory:1,
	secureId : '012345aze',
	userInfo : {
		age:35,
		gender:'M',
		country:'FR',
		language:'fr',
		postcode:75018
	}
});

// Ce que vous recevrez dans le callback : 
http://your.callback.url/callback_page.html?...&editor_id={your_editorID}&dynamicParameter=012345aze
```
IMPORTANT : Afin que la transaction ait bien lieu, notre serveur attend de recevoir une réponse HTTP valide avec le statut 200 (OK).

## Informations de ciblage
Il est possible (et très souhaitable) de transmettre à ViewPay des informations qui nous permettront de mieux cibler les publicités pour chaque utilisateur. Si vous disposez d’informations non nominatives sur l’utilisateur, telles que son sexe et son âge, où encore la catégorie de l’article à débloquer (Economie, International, sport…), ces informations peuvent être renseignées dans la fonction Init().

Discutons ensemble du champ des possibles au moment de démarrer l’intégration!

```javascript
function VPinitVideo(){
	JKFBASQ.init({
		site_id: 'b23d3f0235ae89e4', // id de démo	
		load_callback : VPexistAds,
		noads_callback : VPnoAds,
		complete_callback : VPcompleteAds,
		close_callback : VPcloseAds,
		play_callback : VPplayAds,
		codeCategory:1
		display_paywall  : true //True : Affiche le paywall, False : Pas de Paywall
		userInfo : {
			age:35,
			gender:'M',
			country:'FR',
			language:'fr',
			postcode:75018,
		}
	});
}
```

Tous les champs ne sont pas obligatoires. Les valeurs par défaut de ces champs sont donc vides. 

| Paramètres |      Exemple      |  Description |
|----------|:-------------:|-------|
| codeCategory (int) | 1 | Représente la catégorie d’articles <br>que l’utilisateur souhaite débloquer:<br> 0 : News,<br> 1 : Sports,<br> 2 : Economy,<br> 3 : Politics,<br> 4 : Environment,<br> 5 : Science,<br> 6 : Technology,<br> 7 : Others |
| age (int) | 35 | Age de l'utilisateur |
| gender (char) | M | Sexe de l'utilisateur. Valeurs possibles 'M' ou 'F' |
| country (char) | FR | Code pays de l'utilisateur |
| language (char) | fr | Code langue de l'utilisateur |
| postcode (int) | 75001 | Code postal de l'utilisateur |


Nous avons la possibilité d’ajouter des codeCategory selon vos besoins.
Ainsi si vous ne trouvez pas les catégories qui vous correspondent, l’équipe de ViewPay est à votre disposition pour en discuter.
