---
title: Utilização de Twilio para Mensagens de Voz, VoIP e SMS em Azure
description: Saiba como fazer uma chamada telefónica e envie uma mensagem SMS com o serviço Twilio API no Azure. Amostras de código escritas no Node.js.
services: ''
documentationcenter: nodejs
author: georgewallace
ms.assetid: f558cbbd-13d2-416f-b9b1-33a99c426af9
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 164bedffcf9a1aca9f1fa46dea254fb928abcf04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "69637275"
---
# <a name="using-twilio-for-voice-voip-and-sms-messaging-in-azure"></a>Utilização de Twilio para Mensagens de Voz, VoIP e SMS em Azure
Este guia demonstra como construir apps que comunicam com Twilio e node.js no Azure.

<a id="whatis"/>

## <a name="what-is-twilio"></a>O que é Twilio?
A Twilio é uma plataforma API que facilita aos desenvolvedores fazer e receber chamadas telefónicas, enviar e receber mensagens de texto e incorporar o VoIP a ligar para aplicações móveis nativas e baseadas no navegador. Vamos ver brevemente como isto funciona antes de mergulhar.

### <a name="receiving-calls-and-text-messages"></a>Receber chamadas e mensagens de texto
O Twilio permite que os desenvolvedores [comprem números][purchase_phone] de telefone programáveis que podem ser usados tanto para enviar como receber chamadas e mensagens de texto. Quando um número Twilio recebe uma chamada ou texto de entrada, twilio enviará à sua aplicação web um PEDIDO HTTP POST ou GET, pedindo-lhe instruções sobre como lidar com a chamada ou texto. O seu servidor responderá ao pedido http da Twilio com o [TwiML,][twiml]um conjunto simples de tags XML que contêm instruções sobre como lidar com uma chamada ou texto. Veremos exemplos de TwiML em apenas um momento.

### <a name="making-calls-and-sending-text-messages"></a>Fazer chamadas e enviar mensagens de texto
Ao fazer pedidos http para o serviço web API do serviço Web Twilio, os desenvolvedores podem enviar mensagens de texto ou iniciar chamadas telefónicas de saída. Para chamadas de saída, o desenvolvedor também deve especificar um URL que devolve instruções do TwiML para como lidar com a chamada de saída uma vez ligado.

### <a name="embedding-voip-capabilities-in-ui-code-javascript-ios-or-android"></a>Incorporar capacidades VoIP em código UI (JavaScript, iOS ou Android)
O Twilio fornece um SDK do lado do cliente que pode transformar qualquer navegador web de desktop, aplicação iOS ou aplicativo Android em um telefone VoIP. Neste artigo, vamos focar-nos em como usar o VoIP chamando no navegador. Além do *Twilio JavaScript SDK* em execução no navegador, uma aplicação do lado do servidor (a nossa aplicação nó.js) deve ser usada para emitir um "token de capacidade" ao cliente JavaScript. Pode ler mais sobre a utilização do VoIP com nó.js [no blog Twilio dev][voipnode].

<a id="signup"/>

## <a name="sign-up-for-twilio-microsoft-discount"></a>Inscreva-se para Twilio (Desconto microsoft)
Antes de utilizar os serviços Twilio, deve [primeiro inscrever-se para uma conta][signup]. Os clientes do Microsoft Azure recebem um desconto especial - [certifique-se de se inscrever aqui][signup]!

<a id="azuresite"/>

## <a name="create-and-deploy-a-nodejs-azure-website"></a>Criar e implementar um website do node.js Azure
Em seguida, você precisará criar um site node.js em funcionamento no Azure. [A documentação oficial para o fazer está localizada aqui.][azure_new_site] A um nível elevado, fará o seguinte:

* Inscreva-se numa conta Azure, se ainda não tiver uma
* Usando a consola de administração Azure para criar um novo site
* Adicionar suporte de controlo de fonte (assumimos que usou git)
* Criar um `server.js` ficheiro com uma aplicação web simples nó.js
* Implementação desta aplicação simples para o Azure

<a id="twiliomodule"/>

## <a name="configure-the-twilio-module"></a>Configure o Módulo Twilio
Em seguida, começaremos a escrever uma aplicação simples nó.js que faz uso da API Twilio. Antes de começarmos, precisamos configurar as nossas credenciais de conta Twilio.

### <a name="configuring-twilio-credentials-in-system-environment-variables"></a>Configurar credenciais de Twilio em variáveis ambientais do sistema
Para fazer pedidos autenticados contra a parte de trás do Twilio, precisamos da nossa conta SID e auth token, que funcionam como o nome de utilizador e o conjunto de palavra-passe para a nossa conta Twilio. A forma mais segura de configurá-las para utilização com o módulo de nó em Azure é através de variáveis ambientais do sistema, que pode definir diretamente na consola de administração Azure.

Selecione o seu website node.js e clique no link "CONFIGURE".  Se você rolar um pouco para baixo, você verá uma área onde você pode definir propriedades de configuração para a sua aplicação.  Insira as credenciais da sua conta Twilio[(encontradas na sua Consola Twilio][twilio_console]) como mostrado - certifique-se de nomeá-las `TWILIO_ACCOUNT_SID` e, `TWILIO_AUTH_TOKEN`respectivamente:

![Consola de administração Azure][azure-admin-console]

Depois de configurar estas variáveis, reinicie a sua aplicação na consola Azure.

### <a name="declaring-the-twilio-module-in-packagejson"></a>Declarando o módulo Twilio em pacote.json
Em seguida, precisamos criar um pacote.json para gerir as dependências do nosso módulo de nó através [do npm]. Ao mesmo nível `server.js` do ficheiro que criou no tutorial *Azure/node.js,* crie um ficheiro chamado `package.json`.  Dentro deste ficheiro, coloque o seguinte:

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

Isto declara o módulo twilio como uma dependência, bem como o popular [quadro web express][express] e o motor de modelo EJS.  Ok, agora estamos prontos - vamos escrever um código!

<a id="makecall"/>

## <a name="make-an-outbound-call"></a>Faça uma chamada de saída
Vamos criar uma forma simples que fará uma chamada para um número que escolhemos. Abra `server.js`e introduza o seguinte código. Note onde diz "CHANGE_ME" - coloque o nome do seu site azure lá:

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

Em seguida, crie `views` um diretório chamado - `index.ejs` dentro deste diretório, crie um ficheiro com o seguinte conteúdo:

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

Agora, implemente o seu site para O Azure e abra a sua casa. Deverá poder introduzir o seu número de telefone no campo de texto e receber uma chamada do seu número Twilio!

<a id="sendmessage"/>

## <a name="send-an-sms-message"></a>Enviar uma Mensagem SMS
Agora, vamos configurar uma interface de utilizador e formar lógica de manipulação para enviar uma mensagem de texto. Abra `server.js`e adicione o seguinte código após `app.post`a última chamada para:

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

Em, `views/index.ejs`adicione outro formulário sob o primeiro para submeter um número e uma mensagem de texto:

```html
<form action="/sms" method="POST">
  <input placeholder="Enter a phone number" name="number"/>
  <br/>
  <input placeholder="Enter a message to send" name="message"/>
  <br/>
  <input type="submit" value="Send text to the number above"/>
</form>
```

Reimplemente a sua aplicação para o Azure, e agora deverá ser capaz de submeter esse formulário e enviar-se (ou qualquer um dos seus amigos mais próximos) uma mensagem de texto!

<a id="nextsteps"/>

## <a name="next-steps"></a>Passos Seguintes
Agora aprendeu o básico de usar o nó.js e twilio para construir aplicações que comunicam. Mas estes exemplos mal arranham a superfície do que é possível com Twilio e nó.js. Para mais informações utilizando Twilio com nó.js, consulte os seguintes recursos:

* [Docs oficiais do módulo][docs]
* [Tutorial no VoIP com aplicações node.js][voipnode]
* [Votr - um pedido de voto em tempo real com sms em tempo real com nó.js e CouchDB (três partes)][votr]
* [Programação de pares no navegador com nó.js][pair]

Esperamos que adore saquear o nó.js e o Twilio no Azure!

[purchase_phone]: https://www.twilio.com/console/phone-numbers/search
[twiml]: https://www.twilio.com/docs/api/twiml
[signup]: https://ahoy.twilio.com/azure
[azure_new_site]: app-service/app-service-web-get-started-nodejs.md
[twilio_console]: https://www.twilio.com/console
[npm]: https://npmjs.org
[express]: https://expressjs.com
[voipnode]: https://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html
[docs]: https://www.twilio.com/docs/libraries/reference/twilio-node/
[votr]: https://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html
[pair]: https://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html
[azure-admin-console]: ./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png
