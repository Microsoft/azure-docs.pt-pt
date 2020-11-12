---
title: 'Tutorial: Language Understanding Bot Node.js v4'
description: Utilizando Node.js, construa um chat bot integrado com a compreensão da linguagem (LUIS) neste tutorial. Este chatbot utiliza a aplicação Recursos Humanos para implementar rapidamente uma solução de bot. O bot é criado com a versão 4 do Bot Framework e o bot de aplicação Web do Azure.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: 4d399dc9298c51906d62dabbc36650bf21f94af8
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542835"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-nodejs"></a>Tutorial: Use um Bot de aplicação web ativado com compreensão de linguagem em Node.js

Use Node.js para construir um chat bot integrado com a compreensão da linguagem (LUIS). O bot é construído com o recurso [bot da app](https://docs.microsoft.com/azure/bot-service/) Azure Web e a versão Bot [Framework](https://github.com/Microsoft/botbuilder-dotnet) V4.

**Neste tutorial, ficará a saber como:**

> [!div class="checklist"]
> * Criar um bot de aplicação Web. Este processo cria uma aplicação LUIS nova.
> * Descarregue o projeto bot criado pelo serviço Web bot
> * Iniciar o bot e o emulador localmente no seu computador
> * Ver os resultados de expressão no bot

## <a name="prerequisites"></a>Pré-requisitos

* [Emulador de estrutura de bot](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

## <a name="create-a-web-app-bot-resource"></a>Criar um recurso bot de aplicativo web

1. No [portal do Azure](https://portal.azure.com), selecione **Criar novo recurso**.

1. Na caixa de pesquisa, procure e selecione **Bot de Aplicação Web**. Selecione **Criar**.

1. Em **Bot Service** (Serviço de Bot), indique as informações necessárias:

    |Definição|Objetivo|Definição sugerida|
    |--|--|--|
    |Cabo bot|Nome do recurso|`luis-nodejs-bot-` + `<your-name>`, por exemplo, `luis-nodejs-bot-johnsmith`|
    |Subscrição|Subscrição na qual vai criar o bot.|A sua subscrição principal.
    |Grupo de recursos|Grupo lógico de recursos do Azure|Crie um grupo novo para armazenar todos os recursos utilizados com este bot e dê ao grupo o nome `luis-nodejs-bot-resource-group`.|
    |Localização|Região do Azure - não tem de ser a mesma da região de criação ou publicação do LUIS.|`westus`|
    |Escalão de preço|Utilizado para limites de pedidos de serviço e faturação.|`F0` é o escalão gratuito.
    |Nome da aplicação|O nome é utilizado como o subdomínio quando o bot é implementado na cloud (por exemplo, humanresourcesbot.azurewebsites.net).|`luis-nodejs-bot-` + `<your-name>`, por exemplo, `luis-nodejs-bot-johnsmith`|
    |Modelo de bot|Definições do Bot Framework - veja a tabela seguinte|
    |Localização da Aplicação LUIS|Tem de ser a mesma da região de recursos do LUIS|`westus`|
    |Plano de serviço de aplicações/localização|Não altere do valor predefinido fornecido.|
    |Application Insights|Não altere do valor predefinido fornecido.|
    |ID da Aplicação microsoft e senha|Não altere do valor predefinido fornecido.|

1. No **modelo Bot,** selecione o seguinte e, em seguida, escolha o botão **Selecione** nestas definições:

    |Definição|Objetivo|Seleção|
    |--|--|--|
    |Idioma do SDK|Linguagem de programação do bot|**Node.js**|
    |Bot|Tipo de bot|**Bot básico**|

1. Selecione **Criar**. Esta ação cria e implementa o serviço de bot no Azure. Parte deste processo cria uma aplicação LUIS com o nome `luis-nodejs-bot-XXXX`. Este nome baseia-se no nome da aplicação /Azure Bot Service.

    > [!div class="mx-imgBorder"]
    > [![Criar bot de aplicação Web](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

    Aguarde até que o serviço de bot seja criado antes de continuar.

1. Selecione `Go to resource` na notificação para ir à página bot da sua aplicação web.

## <a name="the-bot-has-a-language-understanding-model"></a>O bot tem um modelo de compreensão de linguagem

O processo de criação de serviços bot também cria uma nova app LUIS com intenções e palavras de exemplo. O bot fornece o mapeamento das intenções à nova aplicação LUIS para as intenções seguintes:

|Intenções do LUIS de bot básico|expressão de exemplo|
|--|--|
|Voo do livro|`Travel to Paris`|
|Cancelar|`bye`|
|GetWeather|`what's the weather like?`|
|Nenhum|Tudo o que estiver fora do domínio da aplicação.|

## <a name="test-the-bot-in-web-chat"></a>Teste o bot no Web Chat

1. Enquanto ainda está no portal Azure para o novo bot, selecione **Teste no Web Chat**.
1. No Tipo da sua caixa de texto de **mensagem,** introduza o texto `Book a flight from Seattle to Berlin tomorrow` . O bot responde com a verificação de que quer reservar um voo.

    ![Screenshot do portal Azure, insira o texto 'olá'.](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    Pode utilizar a funcionalidade de teste para testar rapidamente o seu bot. Para testes mais completos, incluindo depurar, descarregue o código bot e use o Código do Estúdio Visual.

## <a name="download-the-web-app-bot-source-code"></a>Descarregue o código fonte bot da aplicação web
Para poder desenvolver o código de bot de aplicação Web, transfira o código e utilize-o no seu computador local.

1. No portal do Azure, selecione **Compilar** , na secção **Gestão de bot**.

1. Selecione **Transferir o código-fonte de Bot**.

    [![Baixe o código fonte de bot de aplicação web para bot básico](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. Quando o diálogo pop-up perguntar **Yes** **Inclua as definições da aplicação no ficheiro zip descarregado?** Isto fornece as definições LUIS.

1. Quando o código de origem estiver zipado, é disponibilizada uma ligação numa mensagem para o transferir. Selecione a ligação.

1. Guarde o ficheiro zip no computador local e extraia os ficheiros. Abra a pasta do projeto com o Código do Estúdio Visual.

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Rever código para enviar expressão para LUIS e obter resposta

1. Para enviar a expressão do utilizador para o ponto final de previsão LUIS, abra o ficheiro **dialogação -> flightBookingRecognizer.js.** É aqui que a expressão do utilizador introduzida no bot é enviada para o LUIS. A resposta de LUIS é devolvida do método **executarLuisQuery.**

    ```javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.

    const { LuisRecognizer } = require('botbuilder-ai');

    class FlightBookingRecognizer {
        constructor(config) {
            const luisIsConfigured = config && config.applicationId && config.endpointKey && config.endpoint;
            if (luisIsConfigured) {
                // Set the recognizer options depending on which endpoint version you want to use e.g v2 or v3.
                // More details can be found in https://docs.microsoft.com/en-gb/azure/cognitive-services/luis/luis-migration-api-v3
                const recognizerOptions = {
                    apiVersion: 'v3'
                };

                this.recognizer = new LuisRecognizer(config, recognizerOptions);
            }
        }

        get isConfigured() {
            return (this.recognizer !== undefined);
        }

        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {TurnContext} context
         */
        async executeLuisQuery(context) {
            return await this.recognizer.recognize(context);
        }

        getFromEntities(result) {
            let fromValue, fromAirportValue;
            if (result.entities.$instance.From) {
                fromValue = result.entities.$instance.From[0].text;
            }
            if (fromValue && result.entities.From[0].Airport) {
                fromAirportValue = result.entities.From[0].Airport[0][0];
            }

            return { from: fromValue, airport: fromAirportValue };
        }

        getToEntities(result) {
            let toValue, toAirportValue;
            if (result.entities.$instance.To) {
                toValue = result.entities.$instance.To[0].text;
            }
            if (toValue && result.entities.To[0].Airport) {
                toAirportValue = result.entities.To[0].Airport[0][0];
            }

            return { to: toValue, airport: toAirportValue };
        }

        /**
         * This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
         * TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
         */
        getTravelDate(result) {
            const datetimeEntity = result.entities.datetime;
            if (!datetimeEntity || !datetimeEntity[0]) return undefined;

            const timex = datetimeEntity[0].timex;
            if (!timex || !timex[0]) return undefined;

            const datetime = timex[0].split('T')[0];
            return datetime;
        }
    }

    module.exports.FlightBookingRecognizer = FlightBookingRecognizer;
    ```

1. Os **diálogos -> mainDialog** captura a expressão e envia-a para a executeLuisQuery no método actStep.

    ```javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.

    const { TimexProperty } = require('@microsoft/recognizers-text-data-types-timex-expression');
    const { MessageFactory, InputHints } = require('botbuilder');
    const { LuisRecognizer } = require('botbuilder-ai');
    const { ComponentDialog, DialogSet, DialogTurnStatus, TextPrompt, WaterfallDialog } = require('botbuilder-dialogs');

    const MAIN_WATERFALL_DIALOG = 'mainWaterfallDialog';

    class MainDialog extends ComponentDialog {
        constructor(luisRecognizer, bookingDialog) {
            super('MainDialog');

            if (!luisRecognizer) throw new Error('[MainDialog]: Missing parameter \'luisRecognizer\' is required');
            this.luisRecognizer = luisRecognizer;

            if (!bookingDialog) throw new Error('[MainDialog]: Missing parameter \'bookingDialog\' is required');

            // Define the main dialog and its related components.
            // This is a sample "book a flight" dialog.
            this.addDialog(new TextPrompt('TextPrompt'))
                .addDialog(bookingDialog)
                .addDialog(new WaterfallDialog(MAIN_WATERFALL_DIALOG, [
                    this.introStep.bind(this),
                    this.actStep.bind(this),
                    this.finalStep.bind(this)
                ]));

            this.initialDialogId = MAIN_WATERFALL_DIALOG;
        }

        /**
         * The run method handles the incoming activity (in the form of a TurnContext) and passes it through the dialog system.
         * If no dialog is active, it will start the default dialog.
         * @param {*} turnContext
         * @param {*} accessor
         */
        async run(turnContext, accessor) {
            const dialogSet = new DialogSet(accessor);
            dialogSet.add(this);

            const dialogContext = await dialogSet.createContext(turnContext);
            const results = await dialogContext.continueDialog();
            if (results.status === DialogTurnStatus.empty) {
                await dialogContext.beginDialog(this.id);
            }
        }

        /**
         * First step in the waterfall dialog. Prompts the user for a command.
         * Currently, this expects a booking request, like "book me a flight from Paris to Berlin on march 22"
         * Note that the sample LUIS model will only recognize Paris, Berlin, New York and London as airport cities.
         */
        async introStep(stepContext) {
            if (!this.luisRecognizer.isConfigured) {
                const messageText = 'NOTE: LUIS is not configured. To enable all capabilities, add `LuisAppId`, `LuisAPIKey` and `LuisAPIHostName` to the .env file.';
                await stepContext.context.sendActivity(messageText, null, InputHints.IgnoringInput);
                return await stepContext.next();
            }

            const messageText = stepContext.options.restartMsg ? stepContext.options.restartMsg : 'What can I help you with today?\nSay something like "Book a flight from Paris to Berlin on March 22, 2020"';
            const promptMessage = MessageFactory.text(messageText, messageText, InputHints.ExpectingInput);
            return await stepContext.prompt('TextPrompt', { prompt: promptMessage });
        }

        /**
         * Second step in the waterfall.  This will use LUIS to attempt to extract the origin, destination and travel dates.
         * Then, it hands off to the bookingDialog child dialog to collect any remaining details.
         */
        async actStep(stepContext) {
            const bookingDetails = {};

            if (!this.luisRecognizer.isConfigured) {
                // LUIS is not configured, we just run the BookingDialog path.
                return await stepContext.beginDialog('bookingDialog', bookingDetails);
            }

            // Call LUIS and gather any potential booking details. (Note the TurnContext has the response to the prompt)
            const luisResult = await this.luisRecognizer.executeLuisQuery(stepContext.context);
            switch (LuisRecognizer.topIntent(luisResult)) {
            case 'BookFlight': {
                // Extract the values for the composite entities from the LUIS result.
                const fromEntities = this.luisRecognizer.getFromEntities(luisResult);
                const toEntities = this.luisRecognizer.getToEntities(luisResult);

                // Show a warning for Origin and Destination if we can't resolve them.
                await this.showWarningForUnsupportedCities(stepContext.context, fromEntities, toEntities);

                // Initialize BookingDetails with any entities we may have found in the response.
                bookingDetails.destination = toEntities.airport;
                bookingDetails.origin = fromEntities.airport;
                bookingDetails.travelDate = this.luisRecognizer.getTravelDate(luisResult);
                console.log('LUIS extracted these booking details:', JSON.stringify(bookingDetails));

                // Run the BookingDialog passing in whatever details we have from the LUIS call, it will fill out the remainder.
                return await stepContext.beginDialog('bookingDialog', bookingDetails);
            }

            case 'GetWeather': {
                // We haven't implemented the GetWeatherDialog so we just display a TODO message.
                const getWeatherMessageText = 'TODO: get weather flow here';
                await stepContext.context.sendActivity(getWeatherMessageText, getWeatherMessageText, InputHints.IgnoringInput);
                break;
            }

            default: {
                // Catch all for unhandled intents
                const didntUnderstandMessageText = `Sorry, I didn't get that. Please try asking in a different way (intent was ${ LuisRecognizer.topIntent(luisResult) })`;
                await stepContext.context.sendActivity(didntUnderstandMessageText, didntUnderstandMessageText, InputHints.IgnoringInput);
            }
            }

            return await stepContext.next();
        }

        /**
         * Shows a warning if the requested From or To cities are recognized as entities but they are not in the Airport entity list.
         * In some cases LUIS will recognize the From and To composite entities as a valid cities but the From and To Airport values
         * will be empty if those entity values can't be mapped to a canonical item in the Airport.
         */
        async showWarningForUnsupportedCities(context, fromEntities, toEntities) {
            const unsupportedCities = [];
            if (fromEntities.from && !fromEntities.airport) {
                unsupportedCities.push(fromEntities.from);
            }

            if (toEntities.to && !toEntities.airport) {
                unsupportedCities.push(toEntities.to);
            }

            if (unsupportedCities.length) {
                const messageText = `Sorry but the following airports are not supported: ${ unsupportedCities.join(', ') }`;
                await context.sendActivity(messageText, messageText, InputHints.IgnoringInput);
            }
        }

        /**
         * This is the final step in the main waterfall dialog.
         * It wraps up the sample "book a flight" interaction with a simple confirmation.
         */
        async finalStep(stepContext) {
            // If the child dialog ("bookingDialog") was cancelled or the user failed to confirm, the Result here will be null.
            if (stepContext.result) {
                const result = stepContext.result;
                // Now we have all the booking details.

                // This is where calls to the booking AOU service or database would go.

                // If the call to the booking service was successful tell the user.
                const timeProperty = new TimexProperty(result.travelDate);
                const travelDateMsg = timeProperty.toNaturalLanguage(new Date(Date.now()));
                const msg = `I have you booked to ${ result.destination } from ${ result.origin } on ${ travelDateMsg }.`;
                await stepContext.context.sendActivity(msg, msg, InputHints.IgnoringInput);
            }

            // Restart the main dialog with a different message the second time around
            return await stepContext.replaceDialog(this.initialDialogId, { restartMsg: 'What else can I do for you?' });
        }
    }

    module.exports.MainDialog = MainDialog;
    ```

<a name="ask-bot-a-question-for-the-book-flight-intent"></a>

## <a name="start-the-bot-code"></a>Inicie o código bot

1. Abra uma consola Windows ou Linux ou terminal macOS.

1. Altere para o diretório com o código bot e introduza o seguinte comando para instalar a dependência do dotenv:

    ```console
    npm install dotenv --save
    ```

1. Introduza o seguinte comando para iniciar o bot:

    ```console
    node index.js
    ```

É aberta uma janela de browser com o site do bot de aplicação Web em `http://localhost:3978/`. Uma página inicial exibe informações sobre o seu bot.

![Uma página inicial exibe informações sobre o seu bot.](./media/bfv4-csharp/running-bot-web-home-page-success.png)

## <a name="use-the-bot-framework-emulator-to-test-the-bot"></a>Use o emulador de estrutura bot para testar o bot

Faça uma pergunta ao Bot para a intenção do Voo do Livro.

1. Inicie o emulador De Estrutura bot e selecione **Open Bot**.
1. No **Open um diálogo** pop-up bot, insira o seu URL bot, tal como `http://localhost:3978/api/messages` . A `/api/messages` rota é o endereço web para o bot.
1. Introduza a **palavra-passe** **do Microsoft App ID** e da Microsoft App , encontrada no ficheiro **.env** na raiz do código bot que descarregou.

1. No emulador Bot Framework, insira `Book a flight from Seattle to Berlin tomorrow` e obtenha a mesma resposta para o bot básico que recebeu no Teste em Web **Chat.**

    [![A screenshot mostra o Emulador De Estrutura Bot.](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. Selecione **Yes** (Sim). O bot responde com um resumo das suas ações.
1. A partir do registo do emulador Desaquisador do Quadro bot, selecione a linha que inclui `<- trace LuisV3 Trace` . Isto mostra a resposta JSON da LUIS para a intenção e entidades da expressão.

    [![A screenshot mostra o Emulador De Estrutura bot com o painel no código json direito.](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)

[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Passos seguintes

Veja mais [amostras](https://github.com/microsoft/botframework-solutions) com bots de conversação.

> [!div class="nextstepaction"]
> [Construa uma app de compreensão de linguagem com um domínio de assunto personalizado](luis-quickstart-intents-only.md)
