---
title: 'Tutorial: Reconhecimento vocal bot node. js v4'
description: Usando o Node. js, crie um bot de chat integrado ao LUIS (reconhecimento de linguagem) neste tutorial. Este chatbot utiliza a aplicação Recursos Humanos para implementar rapidamente uma solução de bot. O bot é criado com a versão 4 do Bot Framework e o bot de aplicação Web do Azure.
ms.topic: tutorial
ms.date: 02/03/2020
ms.openlocfilehash: 3ce12176957412a5599ced8b043f553969194efb
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987850"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-nodejs"></a>Tutorial: usar um bot de aplicativo Web habilitado com Reconhecimento vocal no node. js

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

    |Definição|Finalidade|Definição sugerida|
    |--|--|--|
    |Nome do bot|Nome do recurso|`luis-nodejs-bot-` + `<your-name>`, por exemplo, `luis-nodejs-bot-johnsmith`|
    |Subscrição|Subscrição na qual vai criar o bot.|A sua subscrição principal.
    |Grupo de recursos|Grupo lógico de recursos do Azure|Crie um grupo novo para armazenar todos os recursos utilizados com este bot e dê ao grupo o nome `luis-nodejs-bot-resource-group`.|
    |Localização|Região do Azure - não tem de ser a mesma da região de criação ou publicação do LUIS.|`westus`|
    |Escalão de preço|Utilizado para limites de pedidos de serviço e faturação.|`F0` é o escalão gratuito.
    |Nome da aplicação|O nome é utilizado como o subdomínio quando o bot é implementado na cloud (por exemplo, humanresourcesbot.azurewebsites.net).|`luis-nodejs-bot-` + `<your-name>`, por exemplo, `luis-nodejs-bot-johnsmith`|
    |Modelo de bot|Definições do Bot Framework - veja a tabela seguinte|
    |Localização da Aplicação LUIS|Tem de ser a mesma da região de recursos do LUIS|`westus`|
    |Plano de serviço de aplicativos/Localização|Não altere o valor padrão fornecido.|
    |Estatísticas das Aplicações|Não altere o valor padrão fornecido.|
    |Id e senha da Microsoft App|Não altere o valor padrão fornecido.|

1. No **modelo de bot**, selecione o seguinte e, em seguida, escolha o botão **selecionar** nessas configurações:

    |Definição|Finalidade|Seleção|
    |--|--|--|
    |Versão do SDK|Versão do Bot Framework|**SDK v4**|
    |Idioma do SDK|Linguagem de programação do bot|**Node.js**|
    |Bot|Tipo de bot|**Bot básico**|

1. Selecione **Criar**. Esta ação cria e implementa o serviço de bot no Azure. Parte deste processo cria uma aplicação LUIS com o nome `luis-nodejs-bot-XXXX`. Esse nome se baseia no nome do aplicativo de serviço de bot/Azure.

    > [!div class="mx-imgBorder"]
    > [![Criar web app bot](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

    Aguarde até que o serviço de bot seja criado antes de continuar.

## <a name="the-bot-has-a-language-understanding-model"></a>O bot tem um modelo de Reconhecimento vocal

O processo de criação do serviço bot também cria um novo aplicativo LUIS com tentativas e declarações de exemplo. O bot fornece o mapeamento das intenções à nova aplicação LUIS para as intenções seguintes:

|Intenções do LUIS de bot básico|expressão de exemplo|
|--|--|
|Vôo do livro|`Travel to Paris`|
|Cancelar|`bye`|
|GetWeather|`what's the weather like?`|
|Nenhuma|Tudo o que estiver fora do domínio da aplicação.|

## <a name="test-the-bot-in-web-chat"></a>Testar o bot em Web Chat

1. Ainda na portal do Azure para o novo bot, selecione **testar no Web Chat**.
1. Na caixa de texto **Digite sua mensagem** , insira o texto `Book a flight from Seattle to Berlin tomorrow`. O bot responde com a verificação de que você deseja reservar um vôo.

    ![Captura de tela de portal do Azure, insira o texto ' Olá '.](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    Você pode usar a funcionalidade de teste para testar rapidamente o bot. Para um teste mais completo, incluindo depuração, baixe o código do bot e use o Visual Studio.

## <a name="download-the-web-app-bot-source-code"></a>Baixar o código-fonte do bot do aplicativo Web
Para poder desenvolver o código de bot de aplicação Web, transfira o código e utilize-o no seu computador local.

1. No portal do Azure, selecione **Compilar**, na secção **Gestão de bot**.

1. Selecione **Transferir o código-fonte de Bot**.

    [![descarregue o código fonte de fonte do bot da aplicação web para bot básico](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. Quando a caixa de diálogo pop-up perguntar **inclui configurações de aplicativo no arquivo zip baixado?** , selecione **Sim**. Isso fornece as configurações de LUIS.

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
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.

    const { LuisRecognizer } = require('botbuilder-ai');

    class FlightBookingRecognizer {
        constructor(config) {
            const luisIsConfigured = config && config.applicationId && config.endpointKey && config.endpoint;
            if (luisIsConfigured) {
                this.recognizer = new LuisRecognizer(config, {}, true);
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
    ````
<a name="ask-bot-a-question-for-the-book-flight-intent"></a>

## <a name="use-the-bot-emulator-to-test-the-bot"></a>Usar o emulador de bot para testar o bot

Peça ao bot uma pergunta para a tentativa de vôo do livro.

1. Inicie o emulador de bot e selecione **abrir bot**.
1. Na caixa de diálogo pop-up **abrir um bot** , insira a URL do bot, como `http://localhost:3978/api/messages`. A rota de `/api/messages` é o endereço da Web para o bot.
1. Insira a **ID do aplicativo** da Microsoft e a **senha do aplicativo Microsoft**, encontradas no arquivo **. env** na raiz do código do bot que você baixou.

1. No emulador de bot, insira `Book a flight from Seattle to Berlin tomorrow` e obtenha a mesma resposta para o bot básico como você recebeu no **teste no Web Chat**.

    [![resposta básica do bot no emulador](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. Selecione **Sim**. O bot responde com um resumo de suas ações.
1. No log do emulador de bot, selecione a linha que inclui `Luis Trace`. Isso exibe a resposta JSON de LUIS para a intenção e entidades do expressão.

    [![resposta básica do bot no emulador](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Passos seguintes

Veja mais [exemplos](https://github.com/microsoft/botframework-solutions) com bots de conversação.

> [!div class="nextstepaction"]
> [Criar um aplicativo Reconhecimento vocal com um domínio de assunto personalizado](luis-quickstart-intents-only.md)