# ViewPay-AdblockWall

Ce guide a pour objectif de vous guider dans la mise en place du widget JS Asblock wall de ViewPay dans votre site web.

Pour rappel, Viewpay est une solution de micro-paiement par l'attention publicitaire, qui permet à l'utilisateur de débloquer un contenu premium en regardant une publicité. L'Adblock wall de ViewPay a été fait pour répondre aux besoins d'éditeurs n'ayant pas de solution existante mais souhaitant bloquer le contenu de leur site aux visiteurs possédant un Adblocker, ou en leur permettant de débloquer une courte session grâce à la visualisation d'une pub ViewPay.

## Chargement du Javascript
```html
<script type="text/javascript" src="https://cdn.jokerly.com/scripts/adblockWall.js?[function_name]&[duration]"></script> 
```
Le fichier adblockWall.js est le seul fichier nécessaire à appeler, celui-ci fera ensuite le travail afin d'appeler les éléments nécessaires pour un moment donné.
Ces fichiers sont installés sur notre CDN afin de vous garantir d'avoir toujours la dernière version. Pour éviter que notre fichier soit facilement bloqué, nous pouvons réfléchir à vous laisser l'héberger sur votre plateforme afin qu'il soit plus difficilement bloquable. 

NB: Il faut placer le script le plus haut possible dans la page afin d’optimiser son temps de chargement.

## Création d’un div pour accueillir la publicité
Ajoutez un div avec l’id "cadreJokerlyADS" dans votre page. 
```html
<div id="cadreJokerlyADS"></div>
```

Le design CSS est généré automatiquement lors de l’initialisation de Viewpay, ainsi vous n’êtes pas obligés de rajouter du CSS. Cependant, si vous devez changer le design, votre CSS sera pris en priorité. Pour ceci, donnez à ce div les dimensions que vous souhaitez attribuer à Viewpay, en veillant à conserver un ratio largeur/hauteur de 1,44.
Nous imposons 650x450 pour du desktop et de le centrer horizontalement et verticalement.
Voici donc le CSS qui chargeons nous-même :

```css
#cadreJokerlyADS{
	margin: auto;
	top: 0;
	right: 0;
	left: 0;
	position: fixed;
	bottom: 0;
	width: 650px !important;
	height: 450px !important;
	z-index:9999999 !important;
}

//Le z-index est nécessaire à la visibilité de la publicité. 
//Il est impératif de le monter si nécessaire si une frame/bannière/autre est au dessus du nôtre.
//Ceci est obligatoire afin de garantir de CPM élevé.

```
Si vous souhaitez modifier l'une de ces données, n'hésitez pas à contacter votre contact ViewPay

La même chose se passe pour les sites Mobiles/Tablettes. 

Pour votre site mobile, on utilise tout l’espace vertical disponible en ouvrant l’iframe en 100% width et height, sauf si vous spécifiez la taille maximale que vous souhaitez nous offrir.

Les balises média sont les suivantes suivantes afin de permettre aux personnes sur mobile de profiter pleinement de l’interface ViewPay:

```css
@media screen and (max-width: 600px){
	#cadreJokerlyADS{
	width:100% !important;
	height:100% !important;
	margin-top:0;
	}
}
@media screen and ((min-width: 601px) and (max-width: 1024px)){
	#cadreJokerlyADS{
	width:100% !important;
	height:100% !important;
	margin-top:0;
	margin-left:0;
	}
}

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
	var payWall = null;
	function VPinitVideo(){
		payWall = new ViewPayWall({
			site_id : '97be55e4fd74c98a', //Remplacer cet ID par l'ID fourni par l'administrateur ViewPay
			load_callback : VPexistAds, // Voir Doc ViewPay HTML
			noads_callback : VPnoAds, // Voir Doc ViewPay HTML
			complete_callback : VPcompleteAds, // Voir Doc ViewPay HTML
			close_callback : VPcloseAds, // Voir Doc ViewPay HTML
			play_callback : VPplay, 
			display_paywall : true //True : Affiche le paywall, False : Pas de Paywall
		});
	}		
	// Voir Doc ViewPay HTML
	function VPexistAds(){
		console.log('existAds'); 
		$("#lien-voir-content").css("display","block");
	}
	// Voir Doc ViewPay HTML
	function VPnoAds(){
		console.log('noAds');
		$("#lien-error").css("display","block");
	}
    
	// Voir Doc ViewPay HTML
    	function VPcompleteAds(){
		console.log('completeAds');
		payWall.showText(); //Débloquage article
	}
    
	// Voir Doc ViewPay HTML
	function VPcloseAds(){
		console.log('closeAds');
	}
	
	function VPplay(){
		console.log('play');
	}
</script>
```
Le callback "display_paywall" permet d'afficher ou non le paywall en fonction de votre propre stratégie. 
Si c'est un article payant, j'affiche le paywall (true).
L'article est gratuit, je n'affiche pas le Paywall (false).

## Nom de div pour les différentes actions

Chacun des divs possèdent son propre CSS qui peut-être modifié lors de l'intégration du Paywall à l'aide de votre propre fichier de CSS, ou directement en inline

```html
<p class="ViewPay_Opacity"></p>
```
Cette classe contient l'introduction de l'article permettant à l'utilisateur de commencer sa lecture afin de lui donner envie de continuer puis de lui montrer le paywall.
Cette div permet de cacher l'introduction au fur et à mesure de l'arrivée du Paywall grâce à du CSS.
Il faudra ainsi mettre cette div sur l'introduction visible à l'utilisateur, cela donnera un effet de disparition de l'article jusqu'à l'apparition du Paywall

```html
<div class="ViewPay_ReadPC"></div>
```
Cet ID permet d'afficher le pourcentage de lecture restant par rapport à la totalité de l'article.
"Il vous reste 95% de l'article à lire"
C'est à partir de celle-ci que nous allons ensuite afficher tout le contenu du Paywall.
Nous pouvons donc hériter de CSS de div parents.
Le Paywall possède des outils dans le BO à configurer dans votre compte, nous l'étoffons de jour en jour afin de satisfaire un maximum de besoin.

```html
<div id="ViewPay_Article">Lorem ipsum </div>
```
Cet ID contiendra l'article et permettra de cacher l'article à l'aide de JS.
Si le JS est désactivé chez l'utilisateur, nous utilisons alors du CSS.

Les options suivantes du Paywall en BO sont actuellement :
- ViewPay : Débloquage de l'article par une attention publicitaire
- Abonnement : Redirection vers une page définie dans votre compte.


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

## Fond noir
Il faut ajouter un fond sombre autour du système Viewpay,  qui permet d’optimiser l’expérience utilisateur. Voici un exemple du rendu:  
![sample](https://cdn.jokerly.com/images/logosVP/exemple_fondnoir.png)

Pour ce faire, il faut faire apparaître le fond au même moment que l'AdSelector ViewPay en l’ajoutant ainsi dans la fonction VPloadAds : 

```javascript
function VPloadAds(){
	document.getElementById("modal").style.display = 'block';
	JKFBASQ.loadAds();
}
```
Avec le mot "VPmodal" représentant l'Id de la div possédant le code CSS suivant, nous conseillons de fixer l’opacité de background-color à 0.9 afin d’assurer aux annonceurs une visibilité optimale de leurs vidéos : 
```css
#VPmodal{
    width: 100%;
    height: 100%;
    display: none;
    position: fixed;
    background-color: rgba(0, 0, 0, 0.9);
    z-index: 1000;
}
```

Cette div doit aussi disparaître lorsque la personne arrive au VPcompleteAds, ce qui s’obtient ainsi :
 
```javascript 
function VPcompleteAds(){
	document.getElementById("cadre").style.display = 'none';
}
```

De plus il faut le faire disparaître si la personne décide de quitter l’adSelector :
```javascript
function VPcloseAds(){
	document.getElementById("modal").style.display = 'none';
}	
```

Pour finir, la div #cadreJokerlyAds doit être positionnée à l’intérieur de la div #modal:

```html
<div id="modal">
	<div id="cadreJokerlyADS"></div>
</div>
```

Nous avons remarqué que les modales de Bootstrap sont par défaut affichées avec un délai de 0.15 secondes. Pour corriger ce problème dans un site Bootstrap, il suffit de lancer VPLoadAds dans un setTimeout d’au minimum 0.15s.

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
