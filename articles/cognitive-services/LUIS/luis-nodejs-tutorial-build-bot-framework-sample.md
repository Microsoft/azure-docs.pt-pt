---
title: Bot - Node.js - v3
titleSuffix: Azure Cognitive Services
description: Crie um bot integrado com um aplicativo de LUIS com o Bot Framework 3.x e bot de aplicação Web do Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/30/2019
ms.author: diberry
ms.openlocfilehash: 27ebe827e97c8361c7f93a125f09aa9e339a6a14
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58087113"
---
# <a name="luis-bot-in-nodejs-with-the-bot-framework-3x-and-the-azure-web-app-bot"></a>Bot de LUIS em node. js com o Bot Framework 3.x e bot de aplicação Web do Azure

Com Node.js, crie um chatbot integrado na compreensão de idioma (LUIS). Este chatbot utiliza o domínio de HomeAutomation criados previamente para implementar rapidamente uma solução de bot. O bot baseia-se com o Bot Framework 3.x e bot de aplicação Web do Azure.

## <a name="prerequisite"></a>Pré-requisito

Antes de criar o bot, siga os passos em [criar uma aplicação](./luis-get-started-create-app.md) para criar a aplicação do LUIS que utiliza.

O bot responde aos objetivos do domínio HomeAutomation que estão na aplicação do LUIS. Para cada um desses objetivos, a aplicação do LUIS, fornece uma intenção que mapeia para o mesmo. O bot fornece uma caixa de diálogo que lida com a intenção que Deteta o LUIS.

| Intenção | Expressão de exemplo | Funcionalidade de bot |
|:----:|:----------:|---|
| HomeAutomation.TurnOn | Ligar as luzes. | O bot invoca o `TurnOnDialog` quando o `HomeAutomation.TurnOn` é detetado. Esta caixa de diálogo é onde invocará um serviço de IoT para ativar um dispositivo e informar ao usuário que o dispositivo foi ativado. |
| HomeAutomation.TurnOff | Desative as luzes Carvalho. | O bot invoca o `TurnOffDialog` quando o `HomeAutomation.TurnOff` é detetado. Esta caixa de diálogo onde seria invocar um serviço de IoT para desativar um dispositivo e informar ao usuário que o dispositivo foi desativado. |


## <a name="create-a-language-understanding-bot-with-bot-service"></a>Criar um bot de compreensão de idiomas com o serviço de Bot

1. Na [portal do Azure](https://portal.azure.com), selecione **criar novo recurso** no painel de menu e selecione **ver tudo**.

    ![Ver todos os recursos no portal do Azure](./media/luis-tutorial-node-bot/bot-service-creation.png)

2. Na caixa de pesquisa, procure **Web App Bot**. 

    ![Selecione o bot de funções para iniciar o processo de criação de recursos](./media/luis-tutorial-node-bot/bot-service-selection.png)

3. Na **Bot Service** painel, forneça as informações necessárias e selecione **criar**. Esta ação cria e implementa o serviço de bot e aplicação LUIS para o Azure. Se quiser usar [priming de voz](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming), reveja [requisitos de região](troubleshooting.md#what-luis-regions-support-bot-framework-speech-priming) antes de criar o seu bot. 
   * Definir **nome da aplicação** ao nome do seu bot. O nome é utilizado como o subdomínio ao seu bot é implementado na cloud (por exemplo, mynotesbot.azurewebsites.net). <!-- This name is also used as the name of the LUIS app associated with your bot. Copy it to use later, to find the LUIS app associated with the bot. -->
   * Selecione a subscrição [grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), o plano de serviço de aplicações, e [localização](https://azure.microsoft.com/regions/).
   * Para **modelo de Bot**, selecione:
       * **SDK v3**
       * **Node.js**
       * **Compreensão de idiomas**
   * Selecione o **localização da aplicação LUIS**. Este é o processo de criação [região] [ LUIS] a aplicação é criada no.
   * Selecione a caixa de verificação confirmação para o aviso legal. São os termos do aviso legal a caixa de verificação abaixo.

     ![Painel do serviço de bot](./media/luis-tutorial-node-bot/bot-service-setting-callout-template.png)


4. Confirme que o serviço de bot foi implementado.
    * Selecione as notificações (o ícone de sino que esteja localizado ao longo da margem superior do portal do Azure). A notificação será alterado de **implementação iniciada** ao **implementação concluída com êxito**.
    * Depois da notificação muda para **implementação concluída com êxito**, selecione **Ir para recurso** em que a notificação.

## <a name="try-the-default-bot"></a>Experimente o bot de predefinição

Confirmar que foi implementado o bot, verificando a **notificações**. As notificações serão alterados de **implementação em curso...**  para **implementação concluída com êxito**. Selecione **Ir para recurso** botão para abrir o painel de recursos do bot.

<!-- this step isn't supposed to be necessary -->
## <a name="install-npm-resources"></a>Instalar os recursos NPM
Instale pacotes NPM com os seguintes passos:

1. Selecione **crie** da **gestão de Bot** secção do Bot de aplicação Web. 

2. Um novo, segundo a é apresentada a janela de browser. Selecione **editor de código online aberto**.

3. Na barra de navegação superior, selecione o nome do web app bot `homeautomationluisbot`. 

4. Na lista pendente, selecione **abra a consola Kudu**.

5. É aberta uma nova janela do browser. Na consola, introduza o seguinte comando:

    ```console
    cd site\wwwroot && npm install
    ```

    Aguarde o conclusão do processo de instalação. Regresse à janela de browser da primeira. 

## <a name="test-in-web-chat"></a>Teste na Web Chat
Depois do bot é registrado, selecione **teste na Web Chat** para abrir o painel de Web Chat. Escreva "Olá" na Web Chat.

  ![Testar o bot em Web Chat](./media/luis-tutorial-node-bot/bot-service-web-chat.png)

O bot responde, dizendo "atingiu uma saudação. Disse: hello ". Isto confirma que o bot tem recebido sua mensagem e passou-lo a uma aplicação do LUIS que ele criou a predefinição. Este padrão de aplicação LUIS detetou uma intenção de saudação. No próximo passo, vai ligar o bot para a aplicação do LUIS que criou anteriormente em vez do padrão de aplicação LUIS.

## <a name="connect-your-luis-app-to-the-bot"></a>Ligar a sua aplicação LUIS para o bot

Open **as configurações do aplicativo** na janela de browser da primeira e editar a **LuisAppId** campo para conter o ID da sua aplicação LUIS.

  ![Atualizar o ID da aplicação LUIS no Azure](./media/luis-tutorial-node-bot/bot-service-app-id.png)

Se não tiver o ID de aplicação do LUIS, inicie sessão para o [LUIS](luis-reference-regions.md) Web site com a mesma conta que utiliza para iniciar sessão no Azure. Selecione no **as minhas aplicações**. 

1. Localize a aplicação do LUIS que criou anteriormente, que contém as intenções e entidades do domínio HomeAutomation.

2. Na **definições** página da aplicação do LUIS, localize e copie o ID da aplicação.

3. Se ainda não tiver preparado a aplicação, selecione o **treinar** botão no canto superior direito para preparar a sua aplicação.

4. Se ainda não tiver publicado a aplicação, selecione **PUBLISH** na barra de navegação superior para abrir o **Publish** página. Selecione o bloco Production (Produção) e o botão **Publish** (Publicar).

## <a name="modify-the-bot-code"></a>Modificar o código de bot

Vá para a segunda janela de browser, se estiver ainda abrir ou na primeira janela de browser, selecione **crie** e, em seguida, selecione **editor de código online aberto**.

   ![Editor de código online aberto](./media/luis-tutorial-node-bot/bot-service-build.png)

No editor de código, abra `app.js`. Contém o código a seguir:

```javascript
/*-----------------------------------------------------------------------------
A simple Language Understanding (LUIS) bot for the Microsoft Bot Framework. 
-----------------------------------------------------------------------------*/

var restify = require('restify');
var builder = require('botbuilder');
var botbuilder_azure = require("botbuilder-azure");

// Setup Restify Server
var server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function () {
   console.log('%s listening to %s', server.name, server.url); 
});
  
// Create chat connector for communicating with the Bot Framework Service
var connector = new builder.ChatConnector({
    appId: process.env.MicrosoftAppId,
    appPassword: process.env.MicrosoftAppPassword,
    openIdMetadata: process.env.BotOpenIdMetadata 
});

// Listen for messages from users 
server.post('/api/messages', connector.listen());

/*----------------------------------------------------------------------------------------
* Bot Storage: This is a great spot to register the private state storage for your bot. 
* We provide adapters for Azure Table, CosmosDb, SQL Azure, or you can implement your own!
* For samples and documentation, see: https://github.com/Microsoft/BotBuilder-Azure
* ---------------------------------------------------------------------------------------- */

var tableName = 'botdata';
var azureTableClient = new botbuilder_azure.AzureTableClient(tableName, process.env['AzureWebJobsStorage']);
var tableStorage = new botbuilder_azure.AzureBotStorage({ gzipData: false }, azureTableClient);

// Create your bot with a function to receive messages from the user
// This default message handler is invoked if the user's utterance doesn't
// match any intents handled by other dialogs.
var bot = new builder.UniversalBot(connector, function (session, args) {
    session.send('You reached the default message handler. You said \'%s\'.', session.message.text);
});

bot.set('storage', tableStorage);

// Make sure you add code to validate these fields
var luisAppId = process.env.LuisAppId;
var luisAPIKey = process.env.LuisAPIKey;
var luisAPIHostName = process.env.LuisAPIHostName || 'westus.api.cognitive.microsoft.com';

const LuisModelUrl = 'https://' + luisAPIHostName + '/luis/v2.0/apps/' + luisAppId + '?subscription-key=' + luisAPIKey;

// Create a recognizer that gets intents from LUIS, and add it to the bot
var recognizer = new builder.LuisRecognizer(LuisModelUrl);
bot.recognizer(recognizer);

// Add a dialog for each intent that the LUIS app recognizes.
// See https://docs.microsoft.com/bot-framework/nodejs/bot-builder-nodejs-recognize-intent-luis 
bot.dialog('GreetingDialog',
    (session) => {
        session.send('You reached the Greeting intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Greeting'
})

bot.dialog('HelpDialog',
    (session) => {
        session.send('You reached the Help intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Help'
})

bot.dialog('CancelDialog',
    (session) => {
        session.send('You reached the Cancel intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Cancel'
})
```

Os objetivos existentes no App. js são ignorados. Pode deixá-los. 

## <a name="add-a-dialog-that-matches-homeautomationturnon"></a>Adicionar uma caixa de diálogo que corresponde ao HomeAutomation.TurnOn

Copie o código seguinte e adicione-o a `app.js`.

```javascript
bot.dialog('TurnOn',
    (session) => {
        session.send('You reached the TurnOn intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'HomeAutomation.TurnOn'
})
```

O [corresponde ao] [ matches] opção o [triggerAction] [ triggerAction] anexados a caixa de diálogo Especifica o nome da intenção. O reconhecedor é executado sempre que o bot recebe uma expressão do usuário. Se corresponder a intenção de classificação mais elevada que Deteta um `triggerAction` ligado a uma caixa de diálogo, o bot invoca essa caixa de diálogo.

## <a name="add-a-dialog-that-matches-homeautomationturnoff"></a>Adicionar uma caixa de diálogo que corresponde ao HomeAutomation.TurnOff

Copie o código seguinte e adicione-o a `app.js`.

```javascript
bot.dialog('TurnOff',
    (session) => {
        session.send('You reached the TurnOff intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'HomeAutomation.TurnOff'
})
```
## <a name="test-the-bot"></a>Testar o bot

No Portal do Azure, selecione no **teste na Web Chat** para testar o bot. Experimente as mensagens do tipo like "ligar as luzes" e "desativar a minha heater" invocar dos objetivos que adicionou à mesma.
   ![Testar HomeAutomation bot em Web Chat](./media/luis-tutorial-node-bot/bot-service-chat-results.png)

> [!TIP]
> Se achar que o seu bot sempre não reconhece a intenção correta ou entidades, melhore o desempenho da sua aplicação LUIS ao dar a ele mais expressões de exemplo formá-lo. Pode voltar a preparar a aplicação do LUIS sem quaisquer modificações ao código de seu bot. Ver [adicionar expressões de exemplo](https://docs.microsoft.com/azure/cognitive-services/LUIS/add-example-utterances) e [treinar e testar a aplicação do LUIS](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-interactive-test).

## <a name="learn-more-about-bot-framework"></a>Saiba mais sobre o Bot Framework
Saiba mais sobre [Bot Framework](https://dev.botframework.com/) e o [3.x](https://github.com/Microsoft/BotBuilder) e [4.x](https://github.com/Microsoft/botbuilder-js) SDKs.

## <a name="next-steps"></a>Passos Seguintes

<!-- From trying the bot, you can see that the recognizer can trigger interruption of the currently active dialog. Allowing and handling interruptions is a flexible design that accounts for what users really do. Learn more about the various actions you can associate with a recognized intent.-->
Pode tentar adicionar outras intenções, como o ajuda, Cancelar e saudação, para a aplicação do LUIS. Em seguida, adicione caixas de diálogo para as novo intenções e testá-los com o bot. 

<!-- 
> [!NOTE] 
> The default LUIS app that the template created contains example utterances for Cancel, Greeting, and Help intents. In the list of apps, find the app that begins with the name specified in **App name** in the **Bot Service** blade when you created the Bot Service. -->

> [!div class="nextstepaction"]
> [Adicionar intenções](./luis-how-to-add-intents.md)
> [priming de voz](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming)


[triggerAction]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html#triggeraction

[matches]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.itriggeractionoptions.html#matches


[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions

