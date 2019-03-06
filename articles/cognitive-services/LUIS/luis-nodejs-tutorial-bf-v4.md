---
title: Bot - Node.js - v4
titleSuffix: Azure Cognitive Services
description: Com Node.js, crie um chatbot integrado na compreensão de idioma (LUIS). Este chatbot utiliza a aplicação Human Resources para implementar rapidamente uma solução de bot. O bot é criado com a versão 4 do Bot Framework e o bot de aplicação Web do Azure.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 01/30/2019
ms.author: diberry
ms.openlocfilehash: 54bae5548764ed1f89a2ffb7992eb222a058c706
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57403656"
---
# <a name="tutorial-luis-bot-in-nodejs-with-the-bot-framework-4x-and-the-azure-web-app-bot"></a>Tutorial: Bot de LUIS em node. js com o Bot Framework 4.x e bot de aplicação Web do Azure
Com Node.js, pode criar um chatbot integrado com compreensão de idiomas (LUIS). Este bot utiliza a aplicação HomeAutomation para implementar uma solução de bot. O bot é criado com o [bot de aplicação Web](https://docs.microsoft.com/azure/bot-service/) do Azure com a versão v4 do [Bot Framework](https://github.com/Microsoft/botbuilder-js).

**Neste tutorial, vai aprender a:**

> [!div class="checklist"]
> * Criar um bot de aplicação Web. Este processo cria uma aplicação LUIS nova.
> * Adicionar um domínio pré-criado ao novo modelo do LUIS
> * Transferir o projeto criado pelo serviço de bot Web
> * Iniciar o bot e o emulador localmente no seu computador
> * Modificar o código do bot para as novas intenções do LUIS
> * Ver os resultados de expressão no bot

## <a name="prerequisites"></a>Pré-requisitos

* [Emulador de bot](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)


## <a name="create-web-app-bot"></a>Criar bot de aplicação Web

1. No [portal do Azure](https://portal.azure.com), selecione **Criar novo recurso**.

2. Na caixa de pesquisa, procure e selecione **Bot de Aplicação Web**. Selecione **Criar**.

3. Em **Bot Service** (Serviço de Bot), indique as informações necessárias:

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

4. Em **Definições de modelos de bot**, selecione o seguinte e, em seguida, escolha o botão **Selecionar** nestas definições:

    |Definição|Objetivo|Seleção|
    |--|--|--|
    |Versão do SDK|Versão do Bot Framework|**SDK v4**|
    |Idioma do SDK|Linguagem de programação do bot|**Node.js**|
    |Bot Eco/Básico|Tipo de bot|**Bot básico**|
    
5. Selecione **Criar**. Esta ação cria e implementa o serviço de bot no Azure. Parte deste processo cria uma aplicação LUIS com o nome `luis-nodejs-bot-XXXX`. Este nome baseia-se no nome do bot e da aplicação na secção anterior.

    [![Criar bot de funções](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

6. Deixe este separador do browser aberto. Em qualquer passo no portal do LUIS, abra um novo separador do browser. Avance para a secção seguinte quando o novo serviço de bot for implementado.

## <a name="add-prebuilt-domain-to-model"></a>Adicionar um domínio pré-criado ao modelo
Parte da implementação do serviço de bot cria uma aplicação LUIS nova com intenções e expressões de exemplo. O bot fornece o mapeamento das intenções à nova aplicação LUIS para as intenções seguintes: 

|Intenções do LUIS de bot básico|expressão de exemplo|
|--|--|
|Cancelar|`stop`|
|Saudação|`hello`|
|Ajuda|`help`|
|Nenhuma|Tudo o que estiver fora do domínio da aplicação.|

Adicione a aplicação HomeAutomation pré-criada ao modelo para lidar com expressões como: `Turn off the living room lights`

1. Aceda ao portal do [LUIS](https://www.luis.ai) e inicie sessão.
2. Na página **As Minhas Aplicações**, selecione a coluna **Data de criação** para ordenar pela data de criação da aplicação. O Azure Bot Service criou uma aplicação nova na secção anterior. O nome é `luis-nodejs-bot-` + `<your-name>` + quatro carateres aleatórios.
3. Abra a aplicação e selecione a secção **Build** (Compilar), no painel de navegação superior.
4. No painel de navegação esquerdo, selecione **Domínios Pré-criados**.
5. Selecione **Add domain** (Adicionar domínio) no cartão do domínio **HomeAutomation** para o selecionar.
6. Selecione **Train** (Preparar), no menu do canto superior direito.
7. Selecione **Publish** (Publicar), no menu do canto superior direito. 

    A aplicação criada pelo Azure Bot Service tem agora novas intenções:

    |Novas intenções de bot básico|expressão de exemplo|
    |--|--|
    |HomeAutomation.TurnOn|`turn the fan to high`
    |HomeAutomation.TurnOff|`turn off ac please`|

## <a name="download-the-web-app-bot"></a>Transferir o bot de aplicação Web 
Para poder desenvolver o código de bot de aplicação Web, transfira o código e utilize-o no seu computador local. 

1. No portal do Azure, ainda no recurso de bot de aplicação Web, selecione as **Definições da Aplicação** e copie os valores de **botFilePath** e **botFileSecret**. Tem de adicioná-los a um ficheiro de ambiente mais tarde. 

2. No portal do Azure, selecione **Compilar**, na secção **Gestão de bot**. 

3. Selecione **Transferir o código-fonte de Bot**. 

    [![Baixe o código-fonte web app bot de bot básico](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

4. Quando o código de origem estiver zipado, é disponibilizada uma ligação numa mensagem para o transferir. Selecione a ligação. 

5. Guarde o ficheiro zip no computador local e extraia os ficheiros. Abra o projeto. 

6. Abra o ficheiro bot.cs e procure `const results = await this.luisRecognizer.recognize(context);`. É aqui que a expressão do utilizador introduzida no bot é enviada para o LUIS.

   ```javascript
    /**
     * Driver code that does one of the following:
     * 1. Display a welcome card upon startup
     * 2. Use LUIS to recognize intents
     * 3. Start a greeting dialog
     * 4. Optionally handle Cancel or Help interruptions
     *
     * @param {Context} context turn context from the adapter
     */
    async onTurn(context) {
        // Create a dialog context
        const dc = await this.dialogs.createContext(context);

        if(context.activity.type === ActivityTypes.Message) {
            // Perform a call to LUIS to retrieve results for the current activity message.
            const results = await this.luisRecognizer.recognize(context);
            
            const topIntent = LuisRecognizer.topIntent(results);

            // handle conversation interrupts first
            const interrupted = await this.isTurnInterrupted(dc, results);
            if(interrupted) {
                return;
            }

            // Continue the current dialog
            const dialogResult = await dc.continue();

            switch(dialogResult.status) {
                case DialogTurnStatus.empty:
                    switch (topIntent) {
                        case GREETING_INTENT:
                            await dc.begin(GREETING_DIALOG);
                            break;

                        case NONE_INTENT:
                        default:
                            // help or no intent identified, either way, let's provide some help
                            // to the user
                            await dc.context.sendActivity(`I didn't understand what you just said to me. topIntent ${topIntent}`);
                            break;
                    }

                case DialogTurnStatus.waiting:
                    // The active dialog is waiting for a response from the user, so do nothing
                break;

                case DialogTurnStatus.complete:
                    await dc.end();
                    break;

                default:
                    await dc.cancelAll();
                    break;

            }

        } else if (context.activity.type === 'conversationUpdate' && context.activity.membersAdded[0].name === 'Bot') {
            // When activity type is "conversationUpdate" and the member joining the conversation is the bot
            // we will send our Welcome Adaptive Card.  This will only be sent once, when the Bot joins conversation
            // To learn more about Adaptive Cards, see https://aka.ms/msbot-adaptivecards for more details.
            const welcomeCard = CardFactory.adaptiveCard(WelcomeCard);
            await context.sendActivity({ attachments: [welcomeCard] });
        }
    }
    ```

    O bot envia a expressão do utilizador ao LUIS e obtém os resultados. A intenção principal determina o fluxo de conversa. 


## <a name="start-the-bot"></a>Iniciar o bot
Antes de alterar qualquer código ou as definições, certifique-se de que o bot funciona. 

1. No Visual Studio Code, abra uma janela de terminal. 

2. Instale as dependências npm deste bot. 

    ```bash
    npm install
    ```
3. Crie um ficheiro para conter as variáveis que o código do bot procura. Dê o nome `.env` ao ficheiro. Adicione as variáveis de ambiente seguintes:

    <!--there is no code language that represents an .env file correctly-->
    ```env
    botFilePath=
    botFileSecret=
    ```

    Defina os valores das variáveis de ambiente para os valores que copiou a partir das Definições da Aplicação do serviço de bot do Azure no Passo 1 da secção **[Transferir o bot de aplicação Web](#download-the-web-app-bot)**.

4. Inicie o bot no modo de observação. Qualquer alteração que faça ao código após o início farão com que a aplicação seja reiniciada automaticamente.

    ```bash
    npm run watch
    ```

5. Quando o bot arrancar, a janela de terminal apresenta a porta local na qual o bot está a ser executado:

    ```console
    > basic-bot@0.1.0 start C:\Users\pattiowens\repos\BFv4\luis-nodejs-bot-src
    > node ./index.js NODE_ENV=development

    restify listening to http://[::]:3978
    
    Get the Emulator: https://aka.ms/botframework-emulator
    
    To talk to your bot, open the luis-nodejs-bot-pattiowens.bot file in the Emulator
    ```

## <a name="start-the-emulator"></a>Iniciar o emulador

1. Inicie o Emulador do Bot. 

2. No emulador do bot, selecione o ficheiro *.bot na raiz do projeto. Este ficheiro `.bot` inclui o ponto final do URL do bot para mensagens:

    [![Bot emulador v4](../../../includes/media/cognitive-services-luis/bfv4/bot-emulator-v4.png)](../../../includes/media/cognitive-services-luis/bfv4/bot-emulator-v4.png#lightbox)

3. Introduza o segredo do bot que copiou a partir das Definições da Aplicação do serviço de bot do Azure no Passo 1 da secção **[Transferir o bot de aplicação Web](#download-the-web-app-bot)**. Desta forma, o emulador pode aceder a todos os campos encriptados no ficheiro .bot.

    ![Segredo do emulador do bot v4](../../../includes/media/cognitive-services-luis/bfv4/bot-secret.png)


4. No emulador do bot, introduza `Hello` e obtenha a resposta adequada para o bot básico.

    [![Resposta de bot básico no emulador](../../../includes/media/cognitive-services-luis/bfv4/emulator-test.png)](../../../includes/media/cognitive-services-luis/bfv4/emulator-test.png#lightbox)

## <a name="modify-bot-code"></a>Modificar o código do bot 

No ficheiro `bot.js`, adicione o código para processar as novas intenções. 

1. Na parte superior do ficheiro, encontre a secção **Supported LUIS Intents** (Intenções de LUIS Suportadas) e adicione constantes para as intenções HomeAutomation:

   ```javascript
    // Supported LUIS Intents
    const GREETING_INTENT = 'Greeting';
    const CANCEL_INTENT = 'Cancel';
    const HELP_INTENT = 'Help';
    const NONE_INTENT = 'None';
    const TURNON_INTENT = 'HomeAutomation_TurnOn'; // new intent
    const TURNOFF_INTENT = 'HomeAutomation_TurnOff'; // new intent
    ```

    Tenha em atenção que o ponto final, `.`, entre o domínio e a intenção na aplicação do portal do LUIS é substituído por um caráter de sublinhado, `_`. 

2. Encontre o **isTurnInterrupted** que recebe a predição do LUIS da expressão e adicione uma linha para imprimir o resultado da consola.

   ```javascript
    /**
     * Look at the LUIS results and determine if we need to handle
     * an interruptions due to a Help or Cancel intent
     *
     * @param {DialogContext} dc - dialog context
     * @param {LuisResults} luisResults - LUIS recognizer results
     */
    async isTurnInterrupted(dc, luisResults) {
        console.log(JSON.stringify(luisResults));
    ...
    ```

    O bot não tem a mesma resposta exata de um pedido de API REST do LUIS, pelo que é importante observar a resposta JSON para saber as diferenças. O texto e as propriedades de intenções são os mesmos, mas os valores das propriedades das entidades foram modificados. 

    ```json
    {
        "$instance": {
            "HomeAutomation_Device": [
                {
                    "startIndex": 23,
                    "endIndex": 29,
                    "score": 0.9776345,
                    "text": "lights",
                    "type": "HomeAutomation.Device"
                }
            ],
            "HomeAutomation_Room": [
                {
                    "startIndex": 12,
                    "endIndex": 22,
                    "score": 0.9079433,
                    "text": "livingroom",
                    "type": "HomeAutomation.Room"
                }
            ]
        },
        "HomeAutomation_Device": [
            "lights"
        ],
        "HomeAutomation_Room": [
            "livingroom"
        ]
    }
    ```

3. Adicione as intenções à declaração de comutador onTurn do método para o caso `DialogTurnStatus.empty`:

   ```javascript
    switch (topIntent) {
        case GREETING_INTENT:
            await dc.begin(GREETING_DIALOG);
            break;

        // New HomeAutomation.TurnOn intent
        case TURNON_INTENT: 

            await dc.context.sendActivity(`TurnOn intent found, entities included: ${JSON.stringify(results.entities)}`);
            break;

        // New HomeAutomation.TurnOff intent
        case TURNOFF_INTENT: 

            await dc.context.sendActivity(`TurnOff intent found, entities included: ${JSON.stringify(results.entities)}`);
            break;

        case NONE_INTENT:
        default:
            // help or no intent identified, either way, let's provide some help
            // to the user
            await dc.context.sendActivity(`I didn't understand what you just said to me. topIntent ${topIntent}`);
            break;
    }
    ```

## <a name="view-results-in-bot"></a>Ver os resultados no bot

1. No emulador do bot, introduza a expressão: `Turn on the livingroom lights to 50%`

2. O bot responde com:

    ```json
    TurnOn intent found, entities included: {"$instance":{“HomeAutomation_Device”:[{“startIndex”:23,“endIndex”:29,“score”:0.9776345,“text”:“lights”,“type”:“HomeAutomation.Device”}],“HomeAutomation_Room”:[{“startIndex”:12,“endIndex”:22,“score”:0.9079433,“text”:“livingroom”,“type”:“HomeAutomation.Room”}]},“HomeAutomation_Device”:[“lights”],“HomeAutomation_Room”:[“livingroom”]}
    ```

## <a name="learn-more-about-bot-framework"></a>Saiba mais sobre o Bot Framework
O serviço Azure Bot utiliza o Bot Framework SDK. Saiba mais sobre o SDK e o Bot Framework:

* Documentação do [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0) v4
* [Bot Builder Samples](https://github.com/Microsoft/botbuilder-samples) (Exemplos do Bot Builder)
* [Bot Builder SDK](https://docs.microsoft.com/javascript/api/botbuilder-core/?view=botbuilder-ts-latest)
* [Bot Builder tools](https://github.com/Microsoft/botbuilder-tools) (Ferramentas do Bot Builder):

## <a name="next-steps"></a>Passos Seguintes

Criou um serviço Azure Bot Service, copiou o segredo do bot e o caminho do ficheiro .bot e transferiu o ficheiro zip do código. Adicionou o domínio HomeAutomation pré-criado à aplicação LUIS criada como parte do serviço Azure Bot Service novo e, depois, preparou e publicou a aplicação novamente. Extraiu o projeto de código, criou um ficheiro de ambiente (`.env`) e definiu o segredo do bot e o caminho do ficheiro .bot. No ficheiro bot.js, adicionou código para processar as duas intenções novas. Depois, testou o bot no emulador de bot para ver a resposta do LUIS a uma expressão de uma das intenções novas. 


> [!div class="nextstepaction"]
> [Build a custom domain in LUIS](luis-quickstart-intents-only.md) (Criar um domínio personalizado no LUIS)
