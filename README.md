# AWS Summit 2022 - Modern Application Demo

L'objectif de ce projet est de vous montrer comment vous pouvez simplement et rapidement démarrer le développement d'une application moderne sur AWS en utilisant [AWS Amplify](https://aws.amazon.com/fr/amplify/) & [AWS Serverless Application Model](https://aws.amazon.com/fr/serverless/sam/).

## Prérequis

Pour pouvoir suivre ce guide, vous devez disposer de :
* [Node.js](https://nodejs.org/en/)
* [npx](https://www.npmjs.com/package/npx)
* [Git](https://git-scm.com/) & [Compte GitHub](https://github.com/)
* [Compte AWS](https://aws.amazon.com/fr/?nc2=h_lg)
* [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) & [Configuration de la CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html)

## Création du frontend avec React

* Commencer par créer votre application web React avec la commande suivante :

```
npx create-react-app demo-amplify
```

* Démarrez votre application en local :

```
npm start
```

* [Créez votre repository Github](https://github.com/new) et publiez votre code sur votre repository :

```
git remote add origin <repository>
git branch -M main
git push -u origin main
```

* Dans la console de management AWS, accédez au service **AWS Amplify** puis créez une nouvelle application avec **Héberger une application Web**.

* Sélectionnez **Github** comme référentiel de code, puis sélectionnez le **repository** et enfin la **branche**.

* Confirmer les paramètres de construction (build).

* Passez en revue tous vos paramètres pour vous assurer que tout est correctement configuré. Choisissez **Enregistrer** et déployer pour déployer votre application Web.

> La construction peut prendre généralement 1 à 2 minutes mais peut varier en fonction de la taille de l'application.

## Création du backend avec AWS Serverless Application Model (AWS SAM)

* Dans votre répertoire de projet, créez un sous-répertoire. Vous pouvez l'appeler `backend` ou autrement puis initialiser votre projet AWS SAM avec la commande suivante :

```
sam init
```

* Vous entrez alors dans le mode intéractif, sélectionnez ensuite `1 - AWS Quick Start Templates`.

* Puis `1 - Hello World Example`.

* Ensuite, sélectionnez le langage, dans notre cas `8 - node.js14.x`.

* Puis `1 - Hello World Example`.

* Ensuite, lancez le packaging de votre application AWS SAM avec la commande suivante :

```
sam build
```

* Une fois le packaging de votre application terminé, vous pouvez lancer le déploiement de votre application avec la commande suivante :

```
sam deploy —guided
```

> Notez que lorsqu'il s'agit d'un premier déploiement, vous devez utiliser la commande `sam deploy --guided` (déploiement guidé) pour renseigner les différents paramètres de votre application (le nom, la région AWS, etc...). Vous pouvez choisir de sauvegarder ces paramètres dans un fichier de configuration. Lors des déploiements suivants, vous pourrez utiliser la commande `sam deploy` (déploiement non guidé) et le fichier de configuration sera automatiquement utilisé pour déterminer les paramètres.

* Une fois ce premier déploiement effectué, vous pourrez retrouver l'URL de votre API Gateway dans les logs de votre console, plus précisement dans la partie `Outputs` des logs Cloudformation.

## L'intégration entre le frontend et le backend

Pour cette partie, nous allons connecter notre frontend avec notre backend. Nous allons faire en sorte que le message renvoyé par notre fonction AWS Lambda via Amazon API Gateway s'affiche directement au niveau de notre application web React au chargement de la page.

### Modifications de l'application web React

Nous allons commencer par installer le module Node.js **axios** qui va nous permettre de faire un appel `GET` depuis notre application web React vers notre API Gateway.

> Pour plus d'informations concernant le fonctionnement de `axios`, vous pouvez consulter la documentation ici : https://axios-http.com/docs/intro

* Commencez par installer le module `axios` avec la commande suivante :

```
npm install axios
```

* Modifiez le code React de votre page `App.js` pour y ajouter un appel `GET` vers votre API Gateway avec `axios`.

> Si besoin, inspirez vous de la démo pour vos modifications -> [React - App.js](https://github.com/MickaelBaye/aws-summit-2022-demo/blob/main/src/App.js)

### Modifications de l'API Gateway

Pour permettre à notre application web React d'appeler notre API Gateway, nous allons devoir activer le **Cross-origin resource sharing (CORS)**.

#### Qu'est-ce que le [Cross-origin resource sharing (CORS)](https://developer.mozilla.org/fr/docs/Web/HTTP/CORS) ?

Le «  Cross-origin resource sharing » (CORS) ou « partage des ressources entre origines multiples » (en français, moins usité) est un mécanisme qui consiste à ajouter des en-têtes HTTP afin de permettre à un agent utilisateur d'accéder à des ressources d'un serveur situé sur une autre origine que le site courant. Un agent utilisateur réalise une requête HTTP multi-origine (cross-origin) lorsqu'il demande une ressource provenant d'un domaine, d'un protocole ou d'un port différent de ceux utilisés pour la page courante.

* Dans la console de management AWS, commencez par accéder au service **API Gateway** puis sélectionnez votre API Gateway.

* Dans le menu **Actions** de votre API, sélectionner **Activer le CORS**.

Suite à cette activation, vous retrouverez la checklist des mises à jour nécessaires pour finaliser la configuration ainsi que la création automatique de la méthode `OPTIONS` pour votre ressource.

* Dans la méthode `OPTIONS`, sélectionnez le type d'intégration **Mock**.

* Puis dans la **Réponse de méthode**, ajoutez une réponse avec un code HTTP `200`.

* Ensuite dans la **Réponse de méthode**, ajoutez l'entête `Access-Control-Allow-Origin`.

* Puis retournez dans la méthode `OPTIONS` pour sélectionner la **Réponse d’intégration** correspondant à la **Réponse de méthode** créée précedemment.

* Dans cette **Réponse d'intégration**, ajoutez l'entête `Access-Control-Allow-Origin` avec comme valeur le nom de domaine de votre application web React.

* Au niveau de la fonction AWS Lambda, modifiez le code pour rajouter dans la réponse l’entête `Access-Control-Allow-Origin` avec comme valeur le nom de domaine de votre application web React.

> Si besoin, inspirez vous de la démo pour vos modifications -> [nodejs - app.js](https://github.com/MickaelBaye/aws-summit-2022-demo/blob/main/sam-demo-backend/hello-world/app.js)

* Une fois toutes les modifications effectuées, redéployez le backend avec la commande suivante :

```
sam build && sam deploy
```

* Publiez le code de votre projet sur votre repository Github afin de déclencher un déploiement automatique par AWS Amplify.

* Une fois le déploiement par AWS Amplify terminé, vous pouvez accéder à votre application pour voir le message renvoyé par votre fonction AWS Lambda via votre API Gateway.

## Pour aller plus loin

Si vous souhaitez aller plus loin, vous trouverez ci-dessous quelques propositions avec la documentation associée. A vous de jouer !

* Mettre en place de l'authentification avec Amazon Cognito : https://docs.aws.amazon.com/fr_fr/cognito/latest/developerguide/cognito-integrate-apps.html

* Mettre en place un nom de domaine personalisé avec Amazon Route53 : https://docs.aws.amazon.com/fr_fr/amplify/latest/ug/custom-domains.html

* Mettre en place une table Amazon DynamoDB et mettre en place l'intégration avec AWS Lambda : https://docs.aws.amazon.com/fr_fr/lambda/latest/dg/with-ddb.html
## Documentation

* AWS Amplify 
  * Framework : https://docs.amplify.aws/
  * Hosting : https://docs.aws.amazon.com/amplify/latest/userguide/welcome.html
* AWS Serverless Application Model (AWS SAM): https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html
* Amazon API Gateway - Activation de CORS pour une ressource de l'API REST: https://docs.aws.amazon.com/fr_fr/apigateway/latest/developerguide/how-to-cors.html
* AWS Lambda : https://docs.aws.amazon.com/lambda/index.html
