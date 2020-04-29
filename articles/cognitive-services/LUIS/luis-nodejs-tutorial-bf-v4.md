---
title: 'Tutorial: Language Understanding Bot Node.js v4'
description: Utilizando o Node.js, construa um chat bot integrado com a compreensão linguística (LUIS) neste tutorial. Este chatbot utiliza a aplicação Recursos Humanos para implementar rapidamente uma solução de bot. O bot é criado com a versão 4 do Bot Framework e o bot de aplicação Web do Azure.
ms.topic: tutorial
ms.date: 02/03/2020
ms.openlocfilehash: 3ce12176957412a5599ced8b043f553969194efb
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76987850"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-nodejs"></a>Tutorial: Use um Bot de aplicação web habilitado com compreensão linguística em Node.js

Use node.js para construir um chat bot integrado com a compreensão da linguagem (LUIS). O bot é construído com o recurso bot da [aplicação](https://docs.microsoft.com/azure/bot-service/) Azure Web e [a versão Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) V4.

**Neste tutorial, ficará a saber como:**

> [!div class="checklist"]
> * Criar um bot de aplicação Web. Este processo cria uma aplicação LUIS nova.
> * Descarregue o projeto bot criado pelo serviço web bot
> * Iniciar o bot e o emulador localmente no seu computador
> * Ver os resultados de expressão no bot

## <a name="prerequisites"></a>Pré-requisitos

* [Emulador de bot](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)


## <a name="create-a-web-app-bot-resource"></a>Criar um recurso bot de aplicativo web

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
    |Plano de serviço de aplicativos/Localização|Não altere do valor predefinido fornecido.|
    |Application Insights|Não altere do valor predefinido fornecido.|
    |Id e senha da Microsoft App|Não altere do valor predefinido fornecido.|

1. No **modelo Bot,** selecione o seguinte e, em seguida, escolha o botão **Select** nestas definições:

    |Definição|Objetivo|Seleção|
    |--|--|--|
    |Versão do SDK|Versão do Bot Framework|**SDK v4**|
    |Idioma do SDK|Linguagem de programação do bot|**Node.js**|
    |Bot|Tipo de bot|**Bot básico**|

1. Selecione **Criar**. Esta ação cria e implementa o serviço de bot no Azure. Parte deste processo cria uma aplicação LUIS com o nome `luis-nodejs-bot-XXXX`. Este nome baseia-se no nome da aplicação /Azure Bot Service.

    > [!div class="mx-imgBorder"]
    > [![Criar bot de aplicação Web](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

    Aguarde até que o serviço de bot seja criado antes de continuar.

## <a name="the-bot-has-a-language-understanding-model"></a>O bot tem um modelo de compreensão da linguagem

O processo de criação de serviço sabotado também cria uma nova app LUIS com intenções e pronunciações de exemplo. O bot fornece o mapeamento das intenções à nova aplicação LUIS para as intenções seguintes:

|Intenções do LUIS de bot básico|expressão de exemplo|
|--|--|
|Voo do livro|`Travel to Paris`|
|Cancelar|`bye`|
|GetWeather|`what's the weather like?`|
|Nenhuma|Tudo o que estiver fora do domínio da aplicação.|

## <a name="test-the-bot-in-web-chat"></a>Teste o bot no Web Chat

1. Ainda no portal Azure para o novo bot, selecione **Test in Web Chat**.
1. Na caixa de texto **Digite a sua mensagem,** introduza o texto `Book a flight from Seattle to Berlin tomorrow`. O bot responde com a verificação de que quer reservar um voo.

    ![Screenshot do portal Azure, introduza o texto 'olá'.](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    Pode utilizar a funcionalidade de teste para testar rapidamente o seu bot. Para testes mais completos, incluindo depuração, descarregue o código bot e utilize o Visual Studio.

## <a name="download-the-web-app-bot-source-code"></a>Descarregue o código fonte do bot da aplicação web
Para poder desenvolver o código de bot de aplicação Web, transfira o código e utilize-o no seu computador local.

1. No portal do Azure, selecione **Compilar**, na secção **Gestão de bot**.

1. Selecione **Transferir o código-fonte de Bot**.

    [![Descarregue o código fonte do bot da aplicação web para o bot básico](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. Quando o diálogo pop-up perguntar **Yes** **Inclua as definições da aplicação no ficheiro zip descarregado?** Isto fornece as definições luis.

1. Quando o código de origem estiver zipado, é disponibilizada uma ligação numa mensagem para o transferir. Selecione a ligação.

1. Guarde o ficheiro zip no computador local e extraia os ficheiros. Abra o projeto com o Visual Studio.

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Rever código para enviar a palavra ao LUIS e obter resposta

1. Para enviar a expressão do utilizador para o ponto final da previsão LUIS, abra os **diálogos - > ficheiro FlightBookingRecogniser.js.** É aqui que a expressão do utilizador introduzida no bot é enviada para o LUIS. A resposta do LUIS é devolvida do método **executeLuisQuery.**

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

1. Os **diálogos -> mainDialog** captura maçar a expressão e envia-a para a execução Luisquery no método actStep.


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

## <a name="use-the-bot-emulator-to-test-the-bot"></a>Use o emulador bot para testar o bot

Faça uma pergunta ao bot para a intenção de Voo do Livro.

1. Inicie o Emulador Bot e **selecione Open Bot**.
1. No **diálogo pop-up Open,** introduza o seu `http://localhost:3978/api/messages`URL bot, como . A `/api/messages` rota é o endereço web para o bot.
1. Introduza o ID da **Aplicação microsoft** e a **palavra-passe**da Microsoft App , encontrado no ficheiro **.env** na raiz do código bot que descarregou.

1. No emulador bot, `Book a flight from Seattle to Berlin tomorrow` entre e obtenha a mesma resposta para o bot básico que recebeu no **Teste no Web Chat**.

    [![Resposta bot básica em emulador](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. Selecione **Sim**. O bot responde com um resumo das suas ações.
1. A partir do registo do emulador bot, selecione a linha que inclui `Luis Trace`. Isto exibe a resposta da JSON da LUIS para a intenção e entidades da expressão.

    [![Resposta bot básica em emulador](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Passos seguintes

Veja mais [amostras](https://github.com/microsoft/botframework-solutions) com bots de conversação.

> [!div class="nextstepaction"]
> [Construa uma aplicação de compreensão de linguagem com um domínio de assunto personalizado](luis-quickstart-intents-only.md)