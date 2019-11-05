---
title: 'Tutorial: Application Insights, Node. js-LUIS'
titleSuffix: Azure Cognitive Services
description: Este tutorial adiciona informações de bot e Reconhecimento vocal para Application Insights armazenamento de dados de telemetria.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: b0bc4a93df7db7145accf2b485c45256f53c324d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498958"
---
# <a name="tutorial-add-luis-results-to-application-insights-from-a-bot-in-nodejs"></a>Tutorial: Adicionar resultados de LUIS a Application Insights de um bot no node. js
Este tutorial adiciona informações de bot e Reconhecimento vocal para [Application insights](https://azure.microsoft.com/services/application-insights/) armazenamento de dados de telemetria. Depois de ter esses dados, você pode consultá-los com a linguagem Kusto ou Power BI para analisar, agregar e relatar tentativas e entidades de expressão em tempo real. Essa análise ajuda a determinar se você deve adicionar ou editar as intenções e entidades do seu aplicativo LUIS.

[!INCLUDE [Waiting for Bot refresh](./includes/wait-bot-upgrade.md)]

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Capturar dados de reconhecimento de linguagem e bot no Application Insights
> * Application Insights de consulta para dados de Reconhecimento vocal

## <a name="prerequisites"></a>Pré-requisitos

* Um bot do serviço de bot do Azure, criado com Application Insights habilitado.
* Código de bot baixado do **[tutorial](luis-nodejs-tutorial-bf-v4.md)** anterior do bot. 
* [Emulador de bot](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

Todo o código deste tutorial está disponível no [repositório Azure-samples reconhecimento vocal GitHub](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/v4/luis-nodejs-bot-johnsmith-src-telemetry). 

## <a name="add-application-insights-to-web-app-bot-project"></a>Adicionar Application Insights ao projeto de bot do aplicativo Web
Atualmente, o serviço de Application Insights, usado neste bot de aplicativo Web, coleta a telemetria de estado geral para o bot. Ele não coleta informações de LUIS. 

Para capturar as informações do LUIS, o bot do aplicativo Web precisa do pacote **[Application insights](https://www.npmjs.com/package/applicationinsights)** NPM instalado e configurado.  

1. No terminal integrado do VSCode, na raiz do projeto bot, adicione os seguintes pacotes NPM usando o comando mostrado: 

    ```console
    npm install applicationinsights && npm install underscore
    ```
    
    O pacote de **sublinhado** é usado para mesclar a estrutura JSON Luis para que seja mais fácil ver e usar em Application insights.
    


## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Capturar e enviar resultados da consulta LUIS para Application Insights

1. No VSCode, crie um novo arquivo **appInsightsLog. js** e adicione o seguinte código:

    ```javascript
    const appInsights = require('applicationinsights');
    const _ = require("underscore");
    
    // Log LUIS results to Application Insights
    // must flatten as name/value pairs
    var appInsightsLog = (botContext,luisResponse) => {

        appInsights.setup(process.env.MicrosoftApplicationInsightsInstrumentationKey).start();
        const appInsightsClient = appInsights.defaultClient;

        // put bot context and LUIS results into single object
        var data = Object.assign({}, {'botContext': botContext._activity}, {'luisResponse': luisResponse});
    
        // Flatten data into name/value pairs
        flatten = (x, result, prefix) => {
            if(_.isObject(x)) {
                _.each(x, (v, k) => {
                    flatten(v, result, prefix ? prefix + '_' + k : k)
                })
            } else {
                result["LUIS_" + prefix] = x
            }
            return result;
        }
    
        // call fn to flatten data
        var flattenedData = flatten(data, {});
    
        // ApplicationInsights Trace 
        console.log(JSON.stringify(flattenedData));
    
        // send data to Application Insights
        appInsightsClient.trackTrace({message: "LUIS", severity: appInsights.Contracts.SeverityLevel.Information, properties: flattenedData});
    }
    
    module.exports.appInsightsLog = appInsightsLog;
    ```

    Esse arquivo usa o contexto do bot e a resposta Luis, achata os dois objetos e os insere em um evento de **rastreamento** no Application insights. O nome do evento é **Luis**. 

1. Abra a pasta **caixas de diálogo** e, em seguida, o arquivo **luisHelper. js** . Inclua o novo **appInsightsLog. js** como um arquivo necessário e Capture o contexto do bot e a resposta do Luis. O código completo para esse arquivo é: 

    ```javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    const { LuisRecognizer } = require('botbuilder-ai');
    const { appInsightsLog } = require('../appInsightsLog');
    
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
    
                // APPINSIGHT: Log results to Application Insights
                appInsightsLog(context,recognizerResult);
    
    
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

## <a name="add-application-insights-instrumentation-key"></a>Adicionar chave de instrumentação de Application Insights 

Para adicionar dados ao Application insights, você precisa da chave de instrumentação.

1. Em um navegador, na [portal do Azure](https://portal.azure.com), localize o recurso de **Application insights** do bot. Seu nome terá a maior parte do nome do bot e, em seguida, os caracteres aleatórios no final do nome, como `luis-nodejs-bot-johnsmithxqowom`. 
1. No recurso Application Insights, na página **visão geral** , copie a **chave de instrumentação**.
1. No VSCode, abra o arquivo **. env** na raiz do projeto de bot. Esse arquivo contém todas as suas variáveis de ambiente.  
1. Adicione uma nova variável, `MicrosoftApplicationInsightsInstrumentationKey` com o valor da sua chave de instrumentação. Não coloque o valor entre aspas. 

## <a name="start-the-bot"></a>Iniciar o bot

1. No terminal integrado do VSCode, inicie o bot:
    
    ```console
    npm start
    ```

1. Inicie o emulador de bot e abra o bot. Esta [etapa](luis-nodejs-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot) é fornecida no tutorial anterior.

1. Faça uma pergunta ao bot. Esta [etapa](luis-nodejs-tutorial-bf-v4.md#ask-bot-a-question-for-the-book-flight-intent) é fornecida no tutorial anterior.

## <a name="view-luis-entries-in-application-insights"></a>Exibir entradas de LUIS no Application Insights

Abra Application Insights para ver as entradas LUIS. Pode levar alguns minutos para que os dados apareçam em Application Insights.

1. No [portal do Azure](https://portal.azure.com), abra o recurso de Application insights do bot. 
1. Quando o recurso for aberto, selecione **Pesquisar** e pesquise todos os dados nos últimos **30 minutos** com o tipo de evento **rastreamento**. Selecione o rastreamento chamado **Luis**. 
1. As informações de bot e LUIS estão disponíveis em **Propriedades personalizadas**. 

    ![Examine as propriedades personalizadas do LUIS armazenadas no Application Insights](./media/luis-tutorial-appinsights/application-insights-luis-trace-custom-properties-nodejs.png)

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Application Insights de consulta para intenção, pontuação e expressão
Application Insights dá a você o poder de consultar os dados com o idioma [Kusto](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview#what-language-do-log-queries-use) , bem como exportá-los para [Power bi](https://powerbi.microsoft.com). 

1. Selecione **log (análise)** . Uma nova janela é aberta com uma janela de consulta na parte superior e uma janela de tabela de dados abaixo dessa. Se você já usou bancos de dados antes, essa organização é familiar. A consulta representa os dados filtrados anteriormente. A coluna **CustomDimensions** tem as informações de bot e Luis.
1. Para extrair a principal intenção, a pontuação e a expressão, adicione o seguinte logo acima da última linha (a linha de `|top...`) na janela de consulta:

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_luisResponse_luisResult_topScoringIntent_intent)
    | extend score = todouble(customDimensions.LUIS_luisResponse_luisResult_topScoringIntent_score)
    | extend utterance = tostring(customDimensions.LUIS_luisResponse_text)
    ```

1. Execute a consulta. As novas colunas de topIntent, Score e expressão estão disponíveis. Selecione a coluna topIntent para classificar.

Saiba mais sobre a [linguagem de consulta Kusto](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) ou [exporte os dados para Power bi](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 

## <a name="next-steps"></a>Passos seguintes

Outras informações que você talvez queira adicionar aos dados do Application insights incluem ID do aplicativo, ID da versão, data da última alteração de modelo, última data de treinamento, última data de publicação. Esses valores podem ser recuperados da URL do ponto de extremidade (ID do aplicativo e ID da versão) ou de uma chamada à API de criação e, em seguida, definidos nas configurações de bot do aplicativo Web e extraídos de lá.  

Se você estiver usando a mesma assinatura de ponto de extremidade para mais de um aplicativo LUIS, também deverá incluir a ID da assinatura e uma propriedade informando que ela é uma chave compartilhada. 

> [!div class="nextstepaction"]
> [Saiba mais sobre o exemplo declarações](luis-how-to-add-example-utterances.md)
