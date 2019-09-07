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
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: 63a0717e615ff85dbc5cfc06567f83cb9aa83a30
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735044"
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
|GetWeather|`what's the weather like?`|
|Nenhum|Tudo o que estiver fora do domínio da aplicação.|

## <a name="test-the-bot-in-web-chat"></a>Testar o bot em Web Chat

1. Ainda na portal do Azure para o novo bot, selecione **testar no Web Chat**. 
1. Na caixa de texto **Digite sua mensagem** , insira o `Book a flight from Seattle to Berlin tomorrow`texto. O bot responde com a verificação de que você deseja reservar um vôo. 

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

1. Para enviar o usuário expressão para o ponto de extremidade de previsão do LUIS, abra o arquivo de **diálogos-> flightBookingRecognizer. js** . É aqui que a expressão do utilizador introduzida no bot é enviada para o LUIS. A resposta de LUIS é retornada do método **executeLuisQuery** .  

    ````javascript
    class FlightBookingRecognizer {

        ...

        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {TurnContext} context
         */
        async executeLuisQuery(context) {
            return await this.recognizer.recognize(context);
        }

        ...

    }
    ````

1. As **caixas de diálogo-> mainDialog** capturam o expressão e os envia para o executeLuisQuery no método actStep.


    ````javascript
    class MainDialog extends ComponentDialog {

        ...

        /**
         * Second step in the waterfall.  This will use LUIS to attempt to extract the origin, destination and travel dates.
         * Then, it hands off to the bookingDialog child dialog to collect any remaining details.
         */
        async actStep(stepContext) {

            ...

            const luisResult = await this.luisRecognizer.executeLuisQuery(stepContext.context);

            switch (LuisRecognizer.topIntent(luisResult)) {
                    case 'BookFlight':
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
            
                    case 'GetWeather':
                        // We haven't implemented the GetWeatherDialog so we just display a TODO message.
                        const getWeatherMessageText = 'TODO: get weather flow here';
                        await stepContext.context.sendActivity(getWeatherMessageText, getWeatherMessageText, InputHints.IgnoringInput);
                        break;
            
                    default:
                        // Catch all for unhandled intents
                        const didntUnderstandMessageText = `Sorry, I didn't get that. Please try asking in a different way (intent was ${ LuisRecognizer.topIntent(luisResult) })`;
                        await stepContext.context.sendActivity(didntUnderstandMessageText, didntUnderstandMessageText, InputHints.IgnoringInput);
                    }
            
                    return await stepContext.next();

        }

        ...

    }
    ````


## <a name="install-dependencies-and-start-the-bot-code-in-visual-studio"></a>Instalar dependências e iniciar o código de bot no Visual Studio

1. No VSCode, no terminal integrado, instale as dependências com o `npm install`comando.
1. Além disso, do terminal integrado, inicie o bot com o `npm start`comando. Isso inicia um aplicativo Web para o bot com um ponto de extremidade HTTP. O console do fornece a URL e o número da porta para acessar o site em execução. Você precisa do número da porta na próxima seção deste tutorial.

    ```console
    > core-bot@1.0.0 start C:\Users\diberry\repos\bots\2019-bot-nodejs-basic
    > node ./index.js
    
    
    restify listening to http://[::]:3978
    
    Get Bot Framework Emulator: https://aka.ms/botframework-emulator
    ```

## <a name="create-an-environment-file-and-add-luis-values"></a>Criar um arquivo de ambiente e adicionar valores de LUIS

O emulador de bot precisa de acesso ao recurso LUIS para fornecer resultados LUIS detalhados.

1. Na raiz do projeto, crie um arquivo chamado `.env` e adicione as seguintes variáveis de ambiente:

    ```console
    LuisAppId= 
    LuisAPIKey=
    LuisAPIHostName=
    ```

1. No portal do Azure, para o recurso bot, abra as definições de configuração do serviço de aplicativo para o aplicativo.
1. Abra **edição avançada**para ver o valor de cada configuração.

    ![Abra * * edição avançada * * para ver o valor de cada configuração.](./media/bfv4-nodejs/environment-settings-for-luis-app.png)

<a name="ask-bot-a-question-for-the-book-flight-intent"></a>

## <a name="use-the-bot-emulator-to-test-the-bot"></a>Usar o emulador de bot para testar o bot

Peça ao bot uma pergunta para a tentativa de vôo do livro.

1. Inicie o emulador de bot e selecione **abrir bot**.
1. Na caixa de diálogo pop-up **abrir um bot** , insira a URL do bot, `http://localhost:3978/api/messages`como. A `/api/messages` rota é o endereço Web para o bot.

1. No emulador de bot, `Book a flight from Seattle to Berlin tomorrow` Insira e obtenha a mesma resposta para o bot básico como você recebeu no **teste no Web Chat**.

    [![Resposta básica de bot no emulador](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. Selecione **Sim**. O bot responde com um resumo de suas ações. 
1. No log do emulador de bot, selecione a linha que inclui `Luis Trace`. Isso exibe a resposta JSON de LUIS para a intenção e entidades do expressão.

    [![Resposta básica de bot no emulador](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Passos Seguintes

Veja mais [exemplos](https://github.com/microsoft/botframework-solutions) com bots de conversação. 

> [!div class="nextstepaction"]
> [Criar um aplicativo Reconhecimento vocal com um domínio de assunto personalizado](luis-quickstart-intents-only.md)