---
title: Bot de compreensão de linguagem v4 node. js
titleSuffix: Azure Cognitive Services
description: Com Node.js, crie um chatbot integrado na compreensão de idioma (LUIS). Este chatbot utiliza a aplicação Human Resources para implementar rapidamente uma solução de bot. O bot é criado com a versão 4 do Bot Framework e o bot de aplicação Web do Azure.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/15/2019
ms.author: diberry
ms.openlocfilehash: 832a62c5cc5440d81f4b92d2463a563f5bb884a3
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67150844"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-nodejs"></a>Tutorial: Utilize um bot de funções ativada com compreensão de idiomas em node. js 

Utilize o node. js para criar um chatbot integrado (LUIS) de compreensão de idiomas. O bot baseia-se com o Azure [bot de funções da Web](https://docs.microsoft.com/azure/bot-service/) recursos e [versão de Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) V4.

**Neste tutorial, vai aprender a:**

> [!div class="checklist"]
> * Criar um bot de aplicação Web. Este processo cria uma aplicação LUIS nova.
> * Transfira o projeto de bot criado pelo serviço de bot do Web
> * Iniciar o bot e o emulador localmente no seu computador
> * Ver os resultados de expressão no bot

## <a name="prerequisites"></a>Pré-requisitos

* [Emulador de bot](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)


## <a name="create-a-web-app-bot-resource"></a>Criar recurso de um bot de aplicação web

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
    |Plano de serviço de aplicações/localização|Não é alterado de valor predefinido fornecido.|
    |Application Insights|Não é alterado de valor predefinido fornecido.|
    |ID da aplicação Microsoft e a palavra-passe|Não é alterado de valor predefinido fornecido.|

1. Na **modelo de Bot**, selecione o seguinte, em seguida, escolha a **selecione** botão sob estas definições:

    |Definição|Objetivo|Seleção|
    |--|--|--|
    |Versão do SDK|Versão do Bot Framework|**SDK v4**|
    |Idioma do SDK|Linguagem de programação do bot|**Node.js**|
    |Bot|Tipo de bot|**Bot básico**|
    
1. Selecione **Criar**. Esta ação cria e implementa o serviço de bot no Azure. Parte deste processo cria uma aplicação LUIS com o nome `luis-nodejs-bot-XXXX`. Este nome é baseado no nome de aplicação do serviço de Bot de /Azure.

    [![Criar bot de funções](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

    Aguarde até que o serviço de bot é criado antes de continuar.

## <a name="the-bot-has-a-language-understanding-model"></a>O bot tem um modelo de compreensão de idiomas

O processo de criação do bot service também cria uma nova aplicação LUIS com intenções e expressões de exemplo. O bot fornece o mapeamento das intenções à nova aplicação LUIS para as intenções seguintes: 

|Intenções do LUIS de bot básico|expressão de exemplo|
|--|--|
|Reservar vôo|`Travel to Paris`|
|Cancelar|`bye`|
|Nenhuma|Tudo o que estiver fora do domínio da aplicação.|

## <a name="test-the-bot-in-web-chat"></a>Testar o bot em Web Chat

1. Ainda no portal do Azure para o novo bot, selecione **teste na Web Chat**. 
1. Na **escreva a mensagem** caixa de texto, introduza o texto `hello`. O bot responde com informações sobre a arquitetura de bot, bem como consultas de exemplo para o modelo do LUIS específico, como reserva um voo para Paris. 

    ![Captura de ecrã do portal do Azure, introduza o texto "Olá".](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    Pode usar a funcionalidade de teste para testar rapidamente o seu bot. Para obter mais informações concluir teste, incluindo a depuração, Baixe o código de bot e utilizar o Visual Studio. 

## <a name="download-the-web-app-bot-source-code"></a>Baixe o código de origem do bot de aplicação web
Para poder desenvolver o código de bot de aplicação Web, transfira o código e utilize-o no seu computador local. 

1. No portal do Azure, selecione **Compilar**, na secção **Gestão de bot**. 

1. Selecione **Transferir o código-fonte de Bot**. 

    [![Baixe o código-fonte web app bot de bot básico](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. Quando a caixa de diálogo pop-up pede-lhe **incluem definições da aplicação no ficheiro zip transferido?** , selecione **Sim**.

1. Quando o código de origem estiver zipado, é disponibilizada uma ligação numa mensagem para o transferir. Selecione a ligação. 

1. Guarde o ficheiro zip no computador local e extraia os ficheiros. Abra o projeto com o Visual Studio. 

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Reveja o código para enviar a expressão para LUIS e obter resposta

1. Abra o **caixas de diálogo -> luisHelper.js** ficheiro. É aqui que a expressão do utilizador introduzida no bot é enviada para o LUIS. A resposta do LUIS é retornada do método como um **bookDetails** objeto JSON. Ao criar o seu bot, também deve criar seu próprio objeto para devolver os detalhes do LUIS. 

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

1. Open **caixas de diálogo -> bookingDialog.js** para compreender como o objeto de BookingDetails é usado para gerenciar o fluxo de conversação. Detalhes de viagens perguntados nos passos, em seguida, as reservas toda é confirmada e finalmente repetida para o utilizador. 

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


## <a name="install-dependencies-and-start-the-bot-code-in-visual-studio"></a>Instalar as dependências e iniciar o código de bot no Visual Studio

1. No VSCode, a partir do terminal integrado, instale as dependências com o comando `npm install`.
1. Também a partir do terminal integrado, inicie o bot com o comando `npm start`. 


## <a name="use-the-bot-emulator-to-test-the-bot"></a>Utilizar o emulador de bot para testar o bot

1. Iniciar o emulador de Bot e selecione **Bot aberto**.
1. Na **abra um bot** caixa de diálogo pop-up, introduza o URL de bot, tais como `http://localhost:3978/api/messages`. O `/api/messages` rota é o endereço da web para o bot.
1. Introduza o **ID da aplicação Microsoft** e **palavra-passe do Microsoft App**, foi encontrado no **. env** ficheiro na raiz do código de bot que transferiu.

    Opcionalmente, pode criar um bot de nova configuração e copie o `MicrosoftAppId` e `MicrosoftAppPassword` partir o **. env** ficheiro no projeto do Visual Studio para o bot. O nome do ficheiro de configuração do bot deve ser igual ao nome do bot. 

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

1. No emulador do bot, introduza `Hello` e obter a mesma resposta para o bot básico que recebeu na **teste na Web Chat**.

    [![Resposta de bot básico no emulador](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)


## <a name="ask-bot-a-question-for-the-book-flight-intent"></a>Faça uma pergunta para a intenção de reservar vôo de bot

1. No emulador do bot, programar um vôo inserindo a seguinte expressão: 

    ```bot
    Book a flight from Paris to Berlin on March 22, 2020
    ```

    O emulador de bot pede confirmar. 

1. Selecione **Sim**. O bot responde com um resumo das suas ações. 
1. O registo do emulador de bot, selecione a linha que inclui `Luis Trace`. Esta ação apresenta a resposta JSON do LUIS para a intenção e as entidades da expressão.

    [![Resposta de bot básico no emulador](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)

## <a name="learn-more-about-the-web-app-bot-and-framework"></a>Saiba mais sobre a estrutura e a Web App Bot

O serviço Azure Bot utiliza o Bot Framework SDK. Saiba mais sobre o SDK e o Bot Framework:

* Documentação do [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0) v4
* [Bot Builder Samples](https://github.com/Microsoft/botbuilder-samples) (Exemplos do Bot Builder)
* [SDK de node. js do construtor de bot](https://github.com/Microsoft/botbuilder-js)
* [Bot Builder tools](https://github.com/Microsoft/botbuilder-tools) (Ferramentas do Bot Builder):

## <a name="next-steps"></a>Passos Seguintes

Ver mais [amostras](https://github.com/microsoft/botframework-solutions) com conversacionais bots. 

> [!div class="nextstepaction"]
> [Criar uma aplicação de compreensão de idiomas com um domínio de requerente personalizado](luis-quickstart-intents-only.md)