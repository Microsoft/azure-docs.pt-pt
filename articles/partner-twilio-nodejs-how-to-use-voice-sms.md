---
title: Utilizar o Twilio para voz, VoIP e mensagens de SMS no Azure
description: Saiba como fazer uma chamada telefónica e enviar uma mensagem SMS com o serviço de Twilio API no Azure. Exemplos de códigos escritos em node. js.
services: ''
documentationcenter: nodejs
author: devinrader
manager: wpickett
editor: ''
ms.assetid: f558cbbd-13d2-416f-b9b1-33a99c426af9
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 11/25/2014
ms.author: wpickett
ms.openlocfilehash: d9f419c48f64ba697e031dfc680bc9cb12bba5c4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60422921"
---
# <a name="using-twilio-for-voice-voip-and-sms-messaging-in-azure"></a>Utilizar o Twilio para voz, VoIP e mensagens de SMS no Azure
Este guia demonstra como criar aplicações que se comunicam com o Twilio e node. js no Azure.

<a id="whatis"/>

## <a name="what-is-twilio"></a>O que é o Twilio?
Twilio é uma plataforma de API que torna mais fácil para os desenvolvedores a fazer e receber chamadas telefónicas, enviar e receber mensagens de texto e incorporar VoIP chamar aplicações móveis nativas e baseada no browser. Vamos vá resumidamente sobre como isto funciona antes de começar.

### <a name="receiving-calls-and-text-messages"></a>Receber chamadas e mensagens de texto
Twilio permite aos desenvolvedores [comprar a números de telefone programável] [ purchase_phone] que podem ser utilizadas para enviar e receber chamadas e mensagens de texto. Quando um número do Twilio recebe uma chamada de entrada ou de texto, Twilio irá enviar seu aplicativo web um HTTP POST ou um pedido GET, que lhe pede para obter instruções sobre como lidar com a chamada ou texto. O servidor irá responder ao pedido HTTP do Twilio com [TwiML][twiml], um conjunto simples de marcas XML que contêm instruções sobre como lidar com uma chamada ou texto. Veremos os exemplos de TwiML daqui a pouco.

### <a name="making-calls-and-sending-text-messages"></a>Efetuar chamadas e enviar mensagens de texto
Ao fazer pedidos de HTTP para a API de serviço web do Twilio, os desenvolvedores podem enviar mensagens de texto ou iniciar chamadas de telefone de saída. Para chamadas de saída, o desenvolvedor também tem de especificar um URL que devolve TwiML instruções sobre como lidar com a chamada de saída, assim que estiver ligado.

### <a name="embedding-voip-capabilities-in-ui-code-javascript-ios-or-android"></a>Capacidades de VoIP de incorporação no código da interface do Usuário (JavaScript, iOS ou Android)
Twilio fornece um SDK do lado do cliente que pode transformar qualquer navegador da web de área de trabalho, a aplicação iOS ou a aplicação Android num telefone VoIP. Neste artigo, nos concentraremos sobre como usar o VoIP chamar no browser. Para além da *Twilio JavaScript SDK* em execução no browser, uma aplicação do lado do servidor (nossa aplicação node. js) deve ser utilizada para emitir um token"capacidade" para o cliente JavaScript. Pode ler mais sobre como utilizar o VoIP com node. js [no blogue de desenvolvimento do Twilio][voipnode].

<a id="signup"/>

## <a name="sign-up-for-twilio-microsoft-discount"></a>Inscreva-se do Twilio (Microsoft de desconto)
Antes de utilizar os serviços do Twilio, primeiro tem [Inscreva-se uma conta][signup]. Os clientes do Microsoft Azure recebem um desconto especial - [Certifique-se inscrever-se aqui][signup]!

<a id="azuresite"/>

## <a name="create-and-deploy-a-nodejs-azure-website"></a>Criar e implementar um Web site do Azure de node. js
Em seguida, terá de criar um Web site em node. js em execução no Azure. [A documentação oficial para efetuar esta ação está localizada aqui][azure_new_site]. Num alto nível, fará o seguinte:

* Inscrever-se numa conta do Azure, se ainda não tiver uma
* Utilizar a consola de administrador do Azure para criar um novo Web site
* Adicionar suporte de controlo de origem (vamos supor que usou o git)
* Criar um ficheiro `server.js` com uma aplicação web do node. js simples
* Implementar esta aplicação simple no Azure

<a id="twiliomodule"/>

## <a name="configure-the-twilio-module"></a>Configurar o módulo do Twilio
Em seguida, vamos começar a escrever uma aplicação node. js simples que utiliza a API Twilio. Antes de começar, precisamos configurar nosso credenciais de conta do Twilio.

### <a name="configuring-twilio-credentials-in-system-environment-variables"></a>Configurar as credenciais do Twilio nas variáveis de ambiente de sistema
Para fazer pedidos autenticados contra o Twilio back-end, precisamos de nossos SID da conta e o token de autenticação, o qual função como o nome de utilizador e palavra-passe definida para a nossa conta do Twilio. É a forma mais segura para configurá-los para utilização com o módulo de nó no Azure através de variáveis de ambiente de sistema, que pode configurar diretamente na consola de administração do Azure.

Selecione o seu Web site do node. js e clique no link "Configurar".  Se se deslocar para baixo um pouco, verá uma área em que pode definir propriedades de configuração para a sua aplicação.  Introduza as suas credenciais de conta do Twilio ([encontrado na consola do Twilio][twilio_console]) conforme mostrado - certificar-se de que nomeá-los `TWILIO_ACCOUNT_SID` e `TWILIO_AUTH_TOKEN`, respectivamente:

![Consola de administração do Azure][azure-admin-console]

Assim que tiver configurado estas variáveis, reinicie a aplicação na consola do Azure.

### <a name="declaring-the-twilio-module-in-packagejson"></a>Declarando o módulo Twilio no Package. JSON
Em seguida, temos de criar um Package. JSON para gerenciar nossa dependências do módulo de nó através de [npm]. O mesmo nível, como o `server.js` de ficheiros que criou na *Azure/node.js* tutorial, crie um ficheiro denominado `package.json`.  Dentro desse arquivo, coloque o seguinte:

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

Isso declara o módulo do twilio, como uma dependência, bem como o popular [expressar a estrutura web] [ express] e o mecanismo de modelo EJS.  OK, agora, está tudo pronto - vamos escrever algum código!

<a id="makecall"/>

## <a name="make-an-outbound-call"></a>Efetuar uma chamada de saída
Vamos criar um formulário simples que irá colocar uma chamada para um número que escolher. Abra `server.js`e introduza o código a seguir. Tenha em atenção de onde ele diz que "CHANGE_ME" - colocar o nome do seu Web site do azure aqui:

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

Em seguida, crie um diretório chamado `views` - nesse diretório, crie um ficheiro denominado `index.ejs` com o seguinte conteúdo:

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

Agora, implemente o seu Web site no Azure e abrir sua casa. Deve ser capaz de introduzir o seu número de telefone no campo de texto e receber uma chamada a partir do seu número do Twilio!

<a id="sendmessage"/>

## <a name="send-an-sms-message"></a>Envie uma mensagem SMS
Agora, vamos configurar uma interface de utilizador e a lógica de processamento de formulário para enviar uma mensagem de texto. Abra `server.js`e adicione o seguinte código após a última chamada para `app.post`:

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

No `views/index.ejs`, adicione outro formulário abaixo daquele primeiro para submeter um número e uma mensagem de texto:

```html
<form action="/sms" method="POST">
  <input placeholder="Enter a phone number" name="number"/>
  <br/>
  <input placeholder="Enter a message to send" name="message"/>
  <br/>
  <input type="submit" value="Send text to the number above"/>
</form>
```

Voltar a implementar a aplicação no Azure, e deve agora conseguir submeter que formam e enviar si mesmo (ou qualquer um dos seus amigos mais próximos) uma mensagem de texto!

<a id="nextsteps"/>

## <a name="next-steps"></a>Próximos Passos
Agora já aprendeu as noções básicas da utilização do node. js e o Twilio para criar aplicações que se comunicam. Mas estes exemplos apenas abordam levemente do que é possível com o Twilio e node. js. Para obter mais informações a utilizar o Twilio com o node. js, consulte os seguintes recursos:

* [Docs oficial do módulo][docs]
* [Tutorial sobre VoIP com aplicações node. js][voipnode]
* [Votr - uma voto de aplicação com node. js e o CouchDB (três partes) de SMS em tempo real][votr]
* [Programação pareada no browser com o node. js][pair]

Esperamos que adora hacking node. js e o Twilio no Azure!

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
