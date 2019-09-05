---
title: 'Tutorial: Reconhecimento vocal do nó. js v4 do bot'
titleSuffix: Azure Cognitive Services
description: Com Node.js, crie um chatbot integrado na compreensão de idioma (LUIS). Este chatbot utiliza a aplicação Human Resources para implementar rapidamente uma solução de bot. O bot é criado com a versão 4 do Bot Framework e o bot de aplicação Web do Azure.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: 8227a7683c1333b21537630f7f074f624926f984
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70375776"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-nodejs"></a>Tutorial: Usar um bot de aplicativo Web habilitado com Reconhecimento vocal no node. js 

Use o Node. js para criar um bot de chat integrado ao LUIS (reconhecimento de linguagem). O bot é criado com o recurso de [bot do aplicativo Web](https://docs.microsoft.com/azure/bot-service/) do Azure e a versão v4 da [estrutura de bot](https://github.com/Microsoft/botbuilder-dotnet) .

**Neste tutorial, vai aprender a:**

> [!div class="checklist"]
> * Criar um bot de aplicação Web. Este processo cria uma aplicação LUIS nova.
> * Baixar o projeto de bot criado pelo serviço de bot da Web
> * Iniciar o bot e o emulador localmente no seu computador
> * Ver os resultados de expressão no bot

## <a name="prerequisites"></a>Pré-requisitos

* [Emulador de bot](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)


## <a name="create-a-web-app-bot-resource"></a>Criar um recurso de bot de aplicativo Web

1. No [portal do Azure](https://portal.azure.com), selecione **Criar novo recurso**.

1. Na caixa de pesquisa, procure e selecione **Bot de Aplicação Web**. Selecione **Criar**.

1. Em **Bot Service** (Serviço de Bot), indique as informações necessárias:

    |Definição|Objetivo|Definição sugerida|
    |--|--|--|
    |Nome do bot|Nome do recurso|`luis-nodejs-bot-` + `<your-name>`, por exemplo, `luis-nodejs-bot-johnsmith`|
    |Subscrição|Subscrição na qual vai criar o bot.|A sua subscrição principal.
    |Grupo de recursos|Grupo lógico de recursos do Azure|Crie um grupo novo para armazenar todos os recursos utilizados com este bot e dê ao grupo o nome `luis-nodejs-bot-resource-group`.|
    |Localização|Região do Azure - não tem de ser a mesma da região de criação ou publicação do LUIS.|`westus`|
    |Escalão de preço|Utilizado para limites de pedidos de serviço e faturação.|`F0` é o escalão gratuito.
    |Nome da aplicação|O nome é utilizado como o subdomínio quando o bot é implementado na cloud (por exemplo, humanresourcesbot.azurewebsites.net).|`luis-nodejs-bot-` + `<your-name>`, por exemplo, `luis-nodejs-bot-johnsmith`|
    |Modelo de bot|Definições do Bot Framework - veja a tabela seguinte|
    |Localização da Aplicação LUIS|Tem de ser a mesma da região de recursos do LUIS|`westus`|
    |Local/plano do serviço de aplicativo|Não altere o valor padrão fornecido.|
    |Application Insights|Não altere o valor padrão fornecido.|
    |ID e senha do aplicativo Microsoft|Não altere o valor padrão fornecido.|

1. No **modelo de bot**, selecione o seguinte e, em seguida, escolha o botão **selecionar** nessas configurações:

    |Definição|Objetivo|Seleção|
    |--|--|--|
    |Versão do SDK|Versão do Bot Framework|**SDK v4**|
    |Idioma do SDK|Linguagem de programação do bot|**Node.js**|
    |Bot|Tipo de bot|**Bot básico**|
    
1. Selecione **Criar**. Esta ação cria e implementa o serviço de bot no Azure. Parte deste processo cria uma aplicação LUIS com o nome `luis-nodejs-bot-XXXX`. Esse nome se baseia no nome do aplicativo de serviço de bot/Azure.

    [![Criar bot de aplicativo Web](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

    Aguarde até que o serviço de bot seja criado antes de continuar.

## <a name="the-bot-has-a-language-understanding-model"></a>O bot tem um modelo de Reconhecimento vocal

O processo de criação do serviço bot também cria um novo aplicativo LUIS com tentativas e declarações de exemplo. O bot fornece o mapeamento das intenções à nova aplicação LUIS para as intenções seguintes: 

|Intenções do LUIS de bot básico|expressão de exemplo|
|--|--|
|Vôo do livro|`Travel to Paris`|
|Cancelar|`bye`|
|Nenhum|Tudo o que estiver fora do domínio da aplicação.|

## <a name="test-the-bot-in-web-chat"></a>Testar o bot em Web Chat

1. Ainda na portal do Azure para o novo bot, selecione **testar no Web Chat**. 
1. Na caixa de texto **Digite sua mensagem** , insira o `hello`texto. O bot responde com informações sobre o bot Framework, bem como consultas de exemplo para o modelo LUIS específico, como a reserva de um vôo para Paris. 

    ![Captura de tela de portal do Azure, insira o texto ' Olá '.](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    Você pode usar a funcionalidade de teste para testar rapidamente o bot. Para um teste mais completo, incluindo depuração, baixe o código do bot e use o Visual Studio. 

## <a name="download-the-web-app-bot-source-code"></a>Baixar o código-fonte do bot do aplicativo Web
Para poder desenvolver o código de bot de aplicação Web, transfira o código e utilize-o no seu computador local. 

1. No portal do Azure, selecione **Compilar**, na secção **Gestão de bot**. 

1. Selecione **Transferir o código-fonte de Bot**. 

    [![Baixar o código-fonte do bot do aplicativo Web para bot básico](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. Quando a caixa de diálogo pop-up perguntar **inclui configurações de aplicativo no arquivo zip baixado?** , selecione **Sim**.

1. Quando o código de origem estiver zipado, é disponibilizada uma ligação numa mensagem para o transferir. Selecione a ligação. 

1. Guarde o ficheiro zip no computador local e extraia os ficheiros. Abra o projeto com o Visual Studio. 

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Revisar o código para enviar expressão para LUIS e obter resposta

1. Abra as **caixas de diálogo-> arquivo luisHelper. js** . É aqui que a expressão do utilizador introduzida no bot é enviada para o LUIS. A resposta de LUIS é retornada do método como um objeto JSON **bookDetails** . Ao criar seu próprio bot, você também deve criar seu próprio objeto para retornar os detalhes de LUIS. 

    ```nodejs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    const { LuisRecognizer } = require('botbuilder-ai');
    
    class LuisHelper {
        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {*} logger
         * @param {TurnContext} context
         */
        static async executeLuisQuery(logger, context) {
            const bookingDetails = {};
    
            try {
                const recognizer = new LuisRecognizer({
                    applicationId: process.env.LuisAppId,
                    endpointKey: process.env.LuisAPIKey,
                    endpoint: `https://${ process.env.LuisAPIHostName }`
                }, {}, true);
    
                const recognizerResult = await recognizer.recognize(context);
    
                const intent = LuisRecognizer.topIntent(recognizerResult);
    
                bookingDetails.intent = intent;
    
                if (intent === 'Book_flight') {
                    // We need to get the result from the LUIS JSON which at every level returns an array
    
                    bookingDetails.destination = LuisHelper.parseCompositeEntity(recognizerResult, 'To', 'Airport');
                    bookingDetails.origin = LuisHelper.parseCompositeEntity(recognizerResult, 'From', 'Airport');
    
                    // This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
                    // TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
                    bookingDetails.travelDate = LuisHelper.parseDatetimeEntity(recognizerResult);
                }
            } catch (err) {
                logger.warn(`LUIS Exception: ${ err } Check your LUIS configuration`);
            }
            return bookingDetails;
        }
    
        static parseCompositeEntity(result, compositeName, entityName) {
            const compositeEntity = result.entities[compositeName];
            if (!compositeEntity || !compositeEntity[0]) return undefined;
    
            const entity = compositeEntity[0][entityName];
            if (!entity || !entity[0]) return undefined;
    
            const entityValue = entity[0][0];
            return entityValue;
        }
    
        static parseDatetimeEntity(result) {
            const datetimeEntity = result.entities['datetime'];
            if (!datetimeEntity || !datetimeEntity[0]) return undefined;
    
            const timex = datetimeEntity[0]['timex'];
            if (!timex || !timex[0]) return undefined;
    
            const datetime = timex[0].split('T')[0];
            return datetime;
        }
    }
    
    module.exports.LuisHelper = LuisHelper;
    ```

1. Abra **caixas de diálogo-> bookingDialog. js** para entender como o objeto BookingDetails é usado para gerenciar o fluxo de conversa. Os detalhes da viagem são solicitados em etapas. em seguida, toda a reserva é confirmada e, finalmente, repetida para o usuário. 

    ```nodejs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    const { TimexProperty } = require('@microsoft/recognizers-text-data-types-timex-expression');
    const { ConfirmPrompt, TextPrompt, WaterfallDialog } = require('botbuilder-dialogs');
    const { CancelAndHelpDialog } = require('./cancelAndHelpDialog');
    const { DateResolverDialog } = require('./dateResolverDialog');
    
    const CONFIRM_PROMPT = 'confirmPrompt';
    const DATE_RESOLVER_DIALOG = 'dateResolverDialog';
    const TEXT_PROMPT = 'textPrompt';
    const WATERFALL_DIALOG = 'waterfallDialog';
    
    class BookingDialog extends CancelAndHelpDialog {
        constructor(id) {
            super(id || 'bookingDialog');
    
            this.addDialog(new TextPrompt(TEXT_PROMPT))
                .addDialog(new ConfirmPrompt(CONFIRM_PROMPT))
                .addDialog(new DateResolverDialog(DATE_RESOLVER_DIALOG))
                .addDialog(new WaterfallDialog(WATERFALL_DIALOG, [
                    this.destinationStep.bind(this),
                    this.originStep.bind(this),
                    this.travelDateStep.bind(this),
                    this.confirmStep.bind(this),
                    this.finalStep.bind(this)
                ]));
    
            this.initialDialogId = WATERFALL_DIALOG;
        }
    
        /**
         * If a destination city has not been provided, prompt for one.
         */
        async destinationStep(stepContext) {
            const bookingDetails = stepContext.options;
    
            if (!bookingDetails.destination) {
                return await stepContext.prompt(TEXT_PROMPT, { prompt: 'To what city would you like to travel?' });
            } else {
                return await stepContext.next(bookingDetails.destination);
            }
        }
    
        /**
         * If an origin city has not been provided, prompt for one.
         */
        async originStep(stepContext) {
            const bookingDetails = stepContext.options;
    
            // Capture the response to the previous step's prompt
            bookingDetails.destination = stepContext.result;
            if (!bookingDetails.origin) {
                return await stepContext.prompt(TEXT_PROMPT, { prompt: 'From what city will you be travelling?' });
            } else {
                return await stepContext.next(bookingDetails.origin);
            }
        }
    
        /**
         * If a travel date has not been provided, prompt for one.
         * This will use the DATE_RESOLVER_DIALOG.
         */
        async travelDateStep(stepContext) {
            const bookingDetails = stepContext.options;
    
            // Capture the results of the previous step
            bookingDetails.origin = stepContext.result;
            if (!bookingDetails.travelDate || this.isAmbiguous(bookingDetails.travelDate)) {
                return await stepContext.beginDialog(DATE_RESOLVER_DIALOG, { date: bookingDetails.travelDate });
            } else {
                return await stepContext.next(bookingDetails.travelDate);
            }
        }
    
        /**
         * Confirm the information the user has provided.
         */
        async confirmStep(stepContext) {
            const bookingDetails = stepContext.options;
    
            // Capture the results of the previous step
            bookingDetails.travelDate = stepContext.result;
            const msg = `Please confirm, I have you traveling to: ${ bookingDetails.destination } from: ${ bookingDetails.origin } on: ${ bookingDetails.travelDate }.`;
    
            // Offer a YES/NO prompt.
            return await stepContext.prompt(CONFIRM_PROMPT, { prompt: msg });
        }
    
        /**
         * Complete the interaction and end the dialog.
         */
        async finalStep(stepContext) {
            if (stepContext.result === true) {
                const bookingDetails = stepContext.options;
    
                return await stepContext.endDialog(bookingDetails);
            } else {
                return await stepContext.endDialog();
            }
        }
    
        isAmbiguous(timex) {
            const timexPropery = new TimexProperty(timex);
            return !timexPropery.types.has('definite');
        }
    }
    
    module.exports.BookingDialog = BookingDialog;
    ```


## <a name="install-dependencies-and-start-the-bot-code-in-visual-studio"></a>Instalar dependências e iniciar o código de bot no Visual Studio

1. No VSCode, no terminal integrado, instale as dependências com o `npm install`comando.
1. Além disso, do terminal integrado, inicie o bot com o `npm start`comando. 


## <a name="use-the-bot-emulator-to-test-the-bot"></a>Usar o emulador de bot para testar o bot

1. Inicie o emulador de bot e selecione **abrir bot**.
1. Na caixa de diálogo pop-up **abrir um bot** , insira a URL do bot, `http://localhost:3978/api/messages`como. A `/api/messages` rota é o endereço Web para o bot.
1. Insira a **ID do aplicativo** da Microsoft e a **senha do aplicativo Microsoft**, encontradas no arquivo **. env** na raiz do código do bot que você baixou.

    Opcionalmente, você pode criar uma nova configuração de bot e copiar `MicrosoftAppId` e `MicrosoftAppPassword` do arquivo **. env** no projeto do Visual Studio para o bot. O nome do arquivo de configuração do bot deve ser o mesmo que o nome do bot. 

    ```json
    {
        "name": "<bot name>",
        "description": "<bot description>",
        "services": [
            {
                "type": "endpoint",
                "appId": "<appId from .env>",
                "appPassword": "<appPassword from .env>",
                "endpoint": "http://localhost:3978/api/messages",
                "id": "<don't change this value>",
                "name": "http://localhost:3978/api/messages"
            }
        ],
        "padlock": "",
        "version": "2.0",
        "overrides": null,
        "path": "<local path to .bot file>"
    }
    ```

1. No emulador de bot, `Hello` Insira e obtenha a mesma resposta para o bot básico como você recebeu no **teste no Web Chat**.

    [![Resposta básica de bot no emulador](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)


## <a name="ask-bot-a-question-for-the-book-flight-intent"></a>Perguntar ao bot uma pergunta sobre a intenção de vôo do livro

1. No emulador de bot, marque um vôo digitando o seguinte expressão: 

    ```console
    Book a flight from Paris to Berlin on March 22, 2020
    ```

    O emulador de bot pede para confirmar. 

1. Selecione **Sim**. O bot responde com um resumo de suas ações. 
1. No log do emulador de bot, selecione a linha que inclui `Luis Trace`. Isso exibe a resposta JSON de LUIS para a intenção e entidades do expressão.

    [![Resposta básica de bot no emulador](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Passos Seguintes

Veja mais [exemplos](https://github.com/microsoft/botframework-solutions) com bots de conversação. 

> [!div class="nextstepaction"]
> [Criar um aplicativo Reconhecimento vocal com um domínio de assunto personalizado](luis-quickstart-intents-only.md)