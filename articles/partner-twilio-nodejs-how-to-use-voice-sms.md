---
title: Usando Twilio para Voz, VoIP e Sms mensagens em Azure
description: Saiba como fazer uma chamada telefónica e envie uma mensagem SMS com o serviço API Twilio em Azure. Amostras de código escritas em Node.js.
documentationcenter: nodejs
author: georgewallace
ms.assetid: f558cbbd-13d2-416f-b9b1-33a99c426af9
ms.service: multiple
ms.devlang: nodejs
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: c4b1c506ff0e78e5e304ceb1682c73f9cd784c95
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88080546"
---
# <a name="using-twilio-for-voice-voip-and-sms-messaging-in-azure"></a>Usando Twilio para Voz, VoIP e Sms mensagens em Azure
Este guia demonstra como construir apps que comunicam com Twilio e node.js em Azure.

<a name="whatis"></a>

## <a name="what-is-twilio"></a>O que é Twilio?
O Twilio é uma plataforma API que facilita a edição de chamadas telefónicas, o envio e a receção de mensagens de texto e a incorporação de VoIP em aplicações móveis baseadas no navegador e nativas. Vamos ver como isto funciona antes de mergulhar.

### <a name="receiving-calls-and-text-messages"></a>Receber chamadas e mensagens de texto
O Twilio permite que os desenvolvedores [comprem números de telefone programáveis][purchase_phone] que podem ser usados tanto para enviar como para receber chamadas e mensagens de texto. Quando um número Twilio recebe uma chamada ou texto de entrada, twilio enviará à sua aplicação web um pedido HTTP POST ou GET, pedindo-lhe instruções sobre como lidar com a chamada ou texto. O seu servidor responderá ao pedido HTTP da Twilio com [o TwiML,][twiml]um conjunto simples de tags XML que contêm instruções sobre como lidar com uma chamada ou texto. Veremos exemplos de TwiML em apenas um momento.

### <a name="making-calls-and-sending-text-messages"></a>Fazer chamadas e enviar mensagens de texto
Ao escamar pedidos HTTP para a API do serviço web Twilio, os desenvolvedores podem enviar mensagens de texto ou iniciar chamadas telefónicas de saída. Para chamadas de saída, o desenvolvedor também deve especificar um URL que retorne as instruções TwiML para como lidar com a chamada de saída uma vez que esteja ligado.

### <a name="embedding-voip-capabilities-in-ui-code-javascript-ios-or-android"></a>Incorporação de capacidades VoIP em código UI (JavaScript, iOS ou Android)
Twilio fornece um SDK do lado do cliente que pode transformar qualquer navegador web de desktop, aplicação iOS ou aplicativo Android em um telefone VoIP. Neste artigo, vamos focar-nos em como usar o VoIP chamando no navegador. Além do *Twilio JavaScript SDK* em execução no navegador, uma aplicação do lado do servidor (a nossa aplicação node.js) deve ser usada para emitir um "token de capacidade" para o cliente JavaScript. Pode ler mais sobre a utilização do VoIP com node.js [no blog Twilio dev][voipnode].

<a name="signup"></a>

## <a name="sign-up-for-twilio-microsoft-discount"></a>Inscreva-se para twilio (desconto microsoft)
Antes de utilizar os serviços Twilio, tem primeiro de [se inscrever para uma conta.][signup] Os clientes da Microsoft Azure recebem um desconto especial - [certifique-se de se inscrever aqui][signup]!

<a name="azuresite"></a>

## <a name="create-and-deploy-a-nodejs-azure-website"></a>Criar e implementar um website node.js Azure
Em seguida, você precisará criar um site node.js em execução no Azure. [A documentação oficial para o fazer está localizada aqui.][azure_new_site] A um nível elevado, estará a fazer o seguinte:

* Inscreva-se para uma conta Azure, se ainda não tiver uma
* Usando a consola de administração Azure para criar um novo site
* Adicionar suporte de controlo de origem (assumimos que usou git)
* Criar um ficheiro `server.js` com uma simples aplicação web node.js
* Implementando esta simples aplicação para a Azure

<a name="twiliomodule"></a>

## <a name="configure-the-twilio-module"></a>Configure o Módulo Twilio
Em seguida, começaremos a escrever uma aplicação de node.js simples que faz uso da API de Twilio. Antes de começarmos, precisamos configurar as nossas credenciais de conta Twilio.

### <a name="configuring-twilio-credentials-in-system-environment-variables"></a>Configurar credenciais de Twilio em variáveis ambientais do sistema
Para fazer pedidos autenticados contra a parte de trás do Twilio, precisamos da nossa conta SID e auth token, que funcionam como o nome de utilizador e senha definidos para a nossa conta Twilio. A forma mais segura de os configurar para utilização com o módulo de nó em Azure é através de variáveis ambientais do sistema, que pode ser definida diretamente na consola de administração Azure.

Selecione o seu website node.js e clique no link "CONFIGURE".  Se deslocar um pouco para baixo, verá uma área onde pode definir propriedades de configuração para a sua aplicação.  Introduza as suas credenciais de conta Twilio[(encontradas na sua Consola Twilio)][twilio_console]como mostrado - certifique-se de nomeá-las `TWILIO_ACCOUNT_SID` `TWILIO_AUTH_TOKEN` e, respectivamente:

![Consola administradora Azure][azure-admin-console]

Depois de configurar estas variáveis, reinicie a sua aplicação na consola Azure.

### <a name="declaring-the-twilio-module-in-packagejson"></a>Declarando o módulo Twilio em package.jsem
Em seguida, precisamos criar uma package.jspara gerir as nossas dependências de módulos de nó através [da npm]. Ao mesmo nível do `server.js` ficheiro que criou no tutorial *Azure/node.js, * crie um ficheiro chamado `package.json` .  Dentro deste ficheiro, coloque o seguinte:

```json
{
  "name": "application-name",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "start": "node server"
  },
  "dependencies": {
    "body-parser": "^1.16.1",
    "ejs": "^2.5.5",
    "errorhandler": "^1.5.0",
    "express": "^4.14.1",
    "morgan": "^1.8.1",
    "twilio": "^2.11.1"
  }
}
```

Isto declara o módulo twilio como uma dependência, bem como a popular [estrutura web Express][express] e o motor do modelo EJS.  Ok, agora estamos prontos - vamos escrever um código!

<a name="makecall"></a>

## <a name="make-an-outbound-call"></a>Fazer uma chamada de saída
Vamos criar uma forma simples que fará uma chamada para um número que escolhemos. Abra `server.js` e introduza o seguinte código. Note onde diz "CHANGE_ME" - coloque o nome do seu site azul lá:

```javascript
// Module dependencies
const express = require('express');
const path = require('path');
const http = require('http');
const twilio = require('twilio');
const logger = require('morgan');
const bodyParser = require('body-parser');
const errorHandler = require('errorhandler');
const accountSid = process.env.TWILIO_ACCOUNT_SID;
const authToken = process.env.TWILIO_AUTH_TOKEN;
// Create Express web application
const app = express();

// Express configuration
app.set('port', process.env.PORT || 3000);
app.set('views', __dirname + '/views');
app.set('view engine', 'ejs');
app.use(logger('tiny'));
app.use(bodyParser.urlencoded({ extended: false }))
app.use(bodyParser.json())
app.use(express.static(path.join(__dirname, 'public')));

if (app.get('env') !== 'production') {
  app.use(errorHandler());
}

// Render an HTML user interface for the application's home page
app.get('/', (request, response) => response.render('index'));

// Handle the form POST to place a call
app.post('/call', (request, response) => {
  var client = twilio(accountSid, authToken);

  client.makeCall({
    // make a call to this number
    to:request.body.number,

    // Change to a Twilio number you bought - see:
    // https://www.twilio.com/console/phone-numbers/incoming
    from:'+15558675309',

    // A URL in our app which generates TwiML
    // Change "CHANGE_ME" to your app's name
    url:'https://CHANGE_ME.azurewebsites.net/outbound_call'
  }, () => {
      // Go back to the home page
      response.redirect('/');
  });
});

// Generate TwiML to handle an outbound call
app.post('/outbound_call', (request, response) => {
  var twiml = new twilio.TwimlResponse();

  // Say a message to the call's receiver
  twiml.say('hello - thanks for checking out Twilio and Azure', {
      voice:'woman'
  });

  response.set('Content-Type', 'text/xml');
  response.send(twiml.toString());
});

// Start server
app.listen(app.get('port'), function(){
  console.log(`Express server listening on port ${app.get('port')}`);
});
```

Em seguida, crie um diretório chamado `views` - dentro deste diretório, crie um ficheiro `index.ejs` com o seguinte conteúdo:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Twilio Test</title>
  <style>
    input { height:20px; width:300px; font-size:18px; margin:5px; padding:5px; }
  </style>
</head>
<body>
  <h1>Twilio Test</h1>
  <form action="/call" method="POST">
      <input placeholder="Enter a phone number" name="number"/>
      <br/>
      <input type="submit" value="Call the number above"/>
  </form>
</body>
</html>
```

Agora, desloque o seu site para Azure e abra a sua casa. Deverá poder inserir o seu número de telefone no campo de texto e receber uma chamada do seu número Twilio!

<a name="sendmessage"></a>

## <a name="send-an-sms-message"></a>Enviar uma mensagem SMS
Agora, vamos configurar uma interface de utilizador e formar lógica de manuseamento para enviar uma mensagem de texto. Abra- `server.js` e adicione o seguinte código após a última chamada `app.post` para:

```javascript
app.post('/sms', (request, response) => {
  const client = twilio(accountSid, authToken);

  client.sendSms({
      // send a text to this number
      to:request.body.number,

      // A Twilio number you bought - see:
      // https://www.twilio.com/console/phone-numbers/incoming
      from:'+15558675309',

      // The body of the text message
      body: request.body.message

  }, () => {
      // Go back to the home page
      response.redirect('/');
  });
});
```

Em `views/index.ejs` , adicionar outro formulário no primeiro para enviar um número e uma mensagem de texto:

```html
<form action="/sms" method="POST">
  <input placeholder="Enter a phone number" name="number"/>
  <br/>
  <input placeholder="Enter a message to send" name="message"/>
  <br/>
  <input type="submit" value="Send text to the number above"/>
</form>
```

Reloque a sua candidatura para Azure, e agora deverá poder submeter esse formulário e enviar a si (ou a qualquer um dos seus amigos mais próximos) uma mensagem de texto!

<a name="nextsteps"></a>

## <a name="next-steps"></a>Passos Seguintes
Aprendeu agora o básico de usar node.js e Twilio para construir apps que comunicam. Mas estes exemplos mal arranham a superfície do que é possível com Twilio e node.js. Para obter mais informações sobre a utilização do Twilio com node.js, confira os seguintes recursos:

* [Docs oficiais do módulo][docs]
* [Tutorial em VoIP com aplicações node.js][voipnode]
* [Votr - um pedido de voto por SMS em tempo real com node.js e CouchDB (três partes)][votr]
* [Emparelhar programação no navegador com node.js][pair]

Esperamos que goste de hackear node.js e Twilio no Azure!

[purchase_phone]: https://www.twilio.com/console/phone-numbers/search
[twiml]: https://www.twilio.com/docs/api/twiml
[signup]: https://ahoy.twilio.com/azure
[azure_new_site]: app-service/quickstart-nodejs.md
[twilio_console]: https://www.twilio.com/console
[npm]: https://npmjs.org
[express]: https://expressjs.com
[voipnode]: https://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html
[docs]: https://www.twilio.com/docs/libraries/reference/twilio-node/
[votr]: https://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html
[pair]: https://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html
[azure-admin-console]: ./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png
