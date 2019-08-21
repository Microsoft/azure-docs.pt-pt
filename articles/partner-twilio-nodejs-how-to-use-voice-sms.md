---
title: Usando o twilio para mensagens de voz, VoIP e SMS no Azure
description: Saiba como fazer uma chamada telefônica e enviar uma mensagem SMS com o serviço de API do twilio no Azure. Exemplos de código escritos em node. js.
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
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69637275"
---
# <a name="using-twilio-for-voice-voip-and-sms-messaging-in-azure"></a>Usando o twilio para mensagens de voz, VoIP e SMS no Azure
Este guia demonstra como criar aplicativos que se comunicam com o twilio e o Node. js no Azure.

<a id="whatis"/>

## <a name="what-is-twilio"></a>O que é o twilio?
O twilio é uma plataforma de API que torna mais fácil para os desenvolvedores fazer e receber chamadas telefônicas, enviar e receber mensagens de texto e inserir a chamada VoIP em aplicativos móveis nativos e baseados em navegador. Vamos examinar brevemente como isso funciona antes de mergulharmos.

### <a name="receiving-calls-and-text-messages"></a>Recebendo chamadas e mensagens de texto
O twilio permite que os desenvolvedores [comprem números de telefone programáveis][purchase_phone] que podem ser usados para enviar e receber chamadas e mensagens de texto. Quando um número twilio recebe uma chamada ou texto de entrada, o twilio envia ao aplicativo Web uma solicitação HTTP POST ou GET, solicitando instruções sobre como lidar com a chamada ou o texto. O servidor responderá à solicitação HTTP do twilio com [TwiML][twiml], um conjunto simples de marcas XML que contém instruções sobre como lidar com uma chamada ou texto. Veremos exemplos de TwiML em apenas um momento.

### <a name="making-calls-and-sending-text-messages"></a>Fazendo chamadas e enviando mensagens de texto
Fazendo solicitações HTTP para a API do serviço Web twilio, os desenvolvedores podem enviar mensagens de texto ou iniciar chamadas telefônicas de saída. Para chamadas de saída, o desenvolvedor também deve especificar uma URL que retorne instruções de TwiML sobre como tratar a chamada de saída quando ela estiver conectada.

### <a name="embedding-voip-capabilities-in-ui-code-javascript-ios-or-android"></a>Incorporando recursos de VoIP no código da interface do usuário (JavaScript, iOS ou Android)
O twilio fornece um SDK do lado do cliente que pode transformar qualquer navegador da Web da área de trabalho, aplicativo iOS ou aplicativo Android em um telefone VoIP. Neste artigo, vamos nos concentrar em como usar a chamada VoIP no navegador. Além do SDK do *JavaScript do twilio* em execução no navegador, um aplicativo do lado do servidor (nosso aplicativo node. js) deve ser usado para emitir um "token de recurso" para o cliente JavaScript. Você pode ler mais sobre como usar VoIP com node. js [no blog do twilio dev][voipnode].

<a id="signup"/>

## <a name="sign-up-for-twilio-microsoft-discount"></a>Inscrever-se no twilio (desconto da Microsoft)
Antes de usar os serviços Twilios, você deve primeiro [inscrever-se para uma conta][signup]. Microsoft Azure clientes recebem um desconto especial- [certifique-se de se inscrever aqui][signup]!

<a id="azuresite"/>

## <a name="create-and-deploy-a-nodejs-azure-website"></a>Criar e implantar um site do Azure do node. js
Em seguida, será necessário criar um site do node. js em execução no Azure. [A documentação oficial para fazer isso está localizada aqui][azure_new_site]. Em um alto nível, você fará o seguinte:

* Inscrever-se em uma conta do Azure, se você ainda não tiver uma
* Usando o console de administração do Azure para criar um novo site
* Adicionando suporte ao controle do código-fonte (vamos pressupor que você usou o git)
* Criando um arquivo `server.js` com um aplicativo Web node. js simples
* Implantando este aplicativo simples no Azure

<a id="twiliomodule"/>

## <a name="configure-the-twilio-module"></a>Configurar o módulo twilio
Em seguida, começaremos a escrever um aplicativo node. js simples, que usa a API twilio. Antes de começar, precisamos configurar nossas credenciais de conta do twilio.

### <a name="configuring-twilio-credentials-in-system-environment-variables"></a>Configurando credenciais do twilio em variáveis de ambiente do sistema
Para fazer solicitações autenticadas em relação ao back-end twilio, precisamos do nosso SID de conta e do token de autenticação, que funciona como o nome de usuário e a senha definidos para nossa conta do twilio. A maneira mais segura de configurá-los para uso com o módulo node no Azure é por meio de variáveis de ambiente do sistema, que podem ser definidas diretamente no console de administração do Azure.

Selecione o site do node. js e clique no link "configurar".  Se você rolar um pouco para baixo, verá uma área em que você pode definir as propriedades de configuração para seu aplicativo.  Insira suas credenciais de conta do twilio ([encontradas no seu console do twilio][twilio_console]), conforme mostrado-certifique `TWILIO_ACCOUNT_SID` - `TWILIO_AUTH_TOKEN`se de nomeá-las e, respectivamente:

![Console de administração do Azure][azure-admin-console]

Depois de configurar essas variáveis, reinicie o aplicativo no console do Azure.

### <a name="declaring-the-twilio-module-in-packagejson"></a>Declarando o módulo twilio no Package. JSON
Em seguida, precisamos criar um Package. JSON para gerenciar nossas dependências de módulo de nó por meio de [NPM]. No mesmo nível `server.js` do arquivo que você criou no tutorial *do Azure/node. js* , crie um arquivo chamado. `package.json`  Dentro desse arquivo, coloque o seguinte:

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

Isso declara o módulo twilio como uma dependência, bem como a popular [estrutura da Web expressa][express] e o mecanismo de modelo EJS.  Ok, agora estamos todos definidos – vamos escrever algum código!

<a id="makecall"/>

## <a name="make-an-outbound-call"></a>Fazer uma chamada de saída
Vamos criar um formulário simples que fará uma chamada para um número que escolhermos. `server.js`Abra e insira o código a seguir. Observe onde diz "CHANGE_ME"-Coloque o nome do seu site do Azure lá:

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

Em seguida, crie um diretório `views` chamado-dentro desse diretório, crie um arquivo `index.ejs` chamado com o seguinte conteúdo:

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

Agora, implante seu site no Azure e abra sua página inicial. Você deve ser capaz de inserir seu número de telefone no campo de texto e receber uma chamada do seu número twilio!

<a id="sendmessage"/>

## <a name="send-an-sms-message"></a>Enviar uma mensagem SMS
Agora, vamos configurar uma interface do usuário e uma lógica de manipulação de formulário para enviar uma mensagem de texto. Abra o e adicione o seguinte código após a última chamada para `app.post`: `server.js`

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

No `views/index.ejs`, adicione outro formulário sob o primeiro para enviar um número e uma mensagem de texto:

```html
<form action="/sms" method="POST">
  <input placeholder="Enter a phone number" name="number"/>
  <br/>
  <input placeholder="Enter a message to send" name="message"/>
  <br/>
  <input type="submit" value="Send text to the number above"/>
</form>
```

Reimplante seu aplicativo no Azure e agora você deve ser capaz de enviar esse formulário e enviar a si mesmo (ou de qualquer um dos seus amigos mais próximos) uma mensagem de texto!

<a id="nextsteps"/>

## <a name="next-steps"></a>Próximos Passos
Agora você aprendeu as noções básicas do uso do node. js e do twilio para criar aplicativos que se comunicam. Mas esses exemplos esduram superficialmente a superfície do que é possível com twilio e node. js. Para obter mais informações sobre como usar o twilio com node. js, confira os seguintes recursos:

* [Documentos oficiais do módulo][docs]
* [Tutorial sobre VoIP com aplicativos node. js][voipnode]
* [Votr-um aplicativo de votação SMS em tempo real com node. js e CouchDB (três partes)][votr]
* [Emparelhar a programação no navegador com node. js][pair]

Esperamos que você goste de invadir o Node. js e o twilio no Azure!

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
