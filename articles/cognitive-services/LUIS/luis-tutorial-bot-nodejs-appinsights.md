---
title: O Application Insights, node. js
titleSuffix: Azure Cognitive Services
description: Este tutorial adiciona bot e informações de compreensão de idiomas para o armazenamento de dados de telemetria do Application Insights.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/16/2019
ms.author: diberry
ms.openlocfilehash: cfed5477df75350f24e77786117e85b9c728c49a
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657768"
---
# <a name="add-luis-results-to-application-insights-from-a-bot-in-nodejs"></a>Adicionar o LUIS resultados para o Application Insights a partir de um Bot em node. js
Este tutorial adiciona informações de compreensão de idiomas para e de bot [Application Insights](https://azure.microsoft.com/services/application-insights/) armazenamento de dados de telemetria. Depois de ter esses dados, pode consultá-lo com o idioma de Kusto ou o Power BI para analisar, Agregar, o relatório de em objetivos e entidades de expressão em tempo real. Isto ajuda a análise é determinar se deve utilizar para adicionar ou editar as intenções e entidades da sua aplicação LUIS.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Capturar bot e os dados de compreensão de idiomas no Application Insights
> * Consultar o Application Insights para os dados de compreensão de idiomas

## <a name="prerequisites"></a>Pré-requisitos

* Um bot do Azure bot do serviço, criado com o Application Insights ativado.
* Transferido o código de bot do bot anterior  **[tutorial](luis-nodejs-tutorial-bf-v4.md)** . 
* [Emulador de bot](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

Todo o código neste tutorial está disponível na [repositório do GitHub de compreensão de linguagem do Azure-Samples](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/v4/luis-nodejs-bot-johnsmith-src-telemetry). 

## <a name="add-application-insights-to-web-app-bot-project"></a>Adicionar o Application Insights ao projeto de bot de aplicação web
Atualmente, o serviço do Application Insights, usado este bot de funções, recolhe telemetria do Estado geral para o bot. Não recolhe informações de LUIS. 

Para capturar as informações de LUIS, o bot de aplicação web tem o **[Application Insights](https://www.npmjs.com/package/applicationinsights)** pacote NPM, instalado e configurado.  

1. No terminal integrado VSCode, na raiz do projeto de bot, adicione os seguintes pacotes NPM com o comando mostrado: 

    ```console
    npm install applicationinsights && npm install underscore
    ```
    
    O **sublinhado** pacote é usado para nivelamento a estrutura de JSON do LUIS, portanto, é mais fácil ver e utilizar no Application Insights.
    


## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Capturar e enviar os resultados da consulta de LUIS para o Application Insights

1. No VSCode, crie um novo ficheiro **appInsightsLog.js** e adicione o seguinte código:

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

    Este ficheiro usa o bot, contexto e a resposta do luis, nivela ambos os objetos e insere-los para um **rastreio** eventos no application insights. O nome do evento é **LUIS**. 

1. Abra o **caixas de diálogo** pasta, o **luisHelper.js** ficheiro. Incluir o novo **appInsightsLog.js** como um ficheiro necessário e capturar o contexto de bot e a resposta de LUIS. O código completo para este ficheiro é: 

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

## <a name="add-application-insights-instrumentation-key"></a>Adicionar chave de instrumentação do Application Insights 

Para adicionar dados para o application insights, terá da chave de instrumentação.

1. Num browser, na [portal do Azure](https://portal.azure.com), localize o seu bot **Application Insights** recursos. Seu nome terá a maior parte do nome do bot, em seguida, aleatório carateres no final do nome, como `luis-nodejs-bot-johnsmithxqowom`. 
1. No recurso do Application Insights, sobre o **descrição geral** página, copie a **chave de instrumentação**.
1. No VSCode, abra a **. env** ficheiro na raiz do projeto de bot. Este ficheiro contém todas as variáveis de ambiente.  
1. Adicionar uma nova variável, `MicrosoftApplicationInsightsInstrumentationKey` com o valor da sua chave de instrumentação. Não fazer nenhuma put o valor de aspas. 

## <a name="start-the-bot"></a>Iniciar o bot

1. A partir do terminal integrado VSCode, inicie o bot:
    
    ```console
    npm start
    ```

1. Inicie o emulador de bot e abra o bot. Isso [passo](luis-nodejs-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot) é fornecido no tutorial anterior.

1. Faça uma pergunta, o bot. Isso [passo](luis-nodejs-tutorial-bf-v4.md#ask-bot-a-question-for-the-book-flight-intent) é fornecido no tutorial anterior.

## <a name="view-luis-entries-in-application-insights"></a>Vista LUIS entradas no Application Insights

Abrir o Application Insights para ver as entradas de LUIS. Pode demorar alguns minutos para que os dados sejam apresentados no Application Insights.

1. Na [portal do Azure](https://portal.azure.com), abra o recurso do Application Insights o bot. 
1. Quando o recurso de abre, selecione **pesquisa** e procure todos os dados nos últimos **30 minutos** com o tipo de evento de **rastreio**. Selecione o rastreio com o nome **LUIS**. 
1. O bot e o LUIS informações estão disponíveis em **propriedades personalizadas**. 

    ![Reveja as propriedades personalizadas do LUIS armazenadas no Application Insights](./media/luis-tutorial-appinsights/application-insights-luis-trace-custom-properties-nodejs.png)

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Application Insights para intenção, a pontuação e a expressão de consulta
O Application Insights dá-lhe o poder para consultar os dados com o [Kusto](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview#what-language-do-log-queries-use) linguagem, assim como as export-lo para [Power BI](https://powerbi.microsoft.com). 

1. Selecione **Log (análise)** . É aberta uma janela nova com uma janela de consulta na parte superior e uma janela da tabela de dados abaixo dele. Se tiver utilizado a bases de dados antes, esse esquema é familiar. A consulta representa os dados filtrados anteriores. O **CustomDimensions** coluna tem o bot e as informações de LUIS.
1. Para extrair a intenção de principal, a pontuação e a expressão, adicionar imediatamente o seguinte acima da última linha (o `|top...` linha) na janela de consulta:

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_luisResponse_luisResult_topScoringIntent_intent)
    | extend score = todouble(customDimensions.LUIS_luisResponse_luisResult_topScoringIntent_score)
    | extend utterance = tostring(customDimensions.LUIS_luisResponse_text)
    ```

1. Execute a consulta. As novas colunas de expressão, pontuação e topIntent estão disponíveis. Selecione topIntent coluna para ordenar.

Saiba mais sobre o [linguagem de consulta de Kusto](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) ou [exportar os dados para o Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 

## <a name="next-steps"></a>Passos Seguintes

Outras informações que pretende adicionar aos dados do application insights incluem o ID da aplicação, ID de versão, data da última alteração modelo, data da última train, a última data de publicação. Estes valores podem ser obtidos a partir do URL de ponto final (app ID e o ID de versão) ou a partir de uma chamada de API de criação, em seguida, definidos nas definições de bot da aplicação web e obtidos a partir daí.  

Se estiver a utilizar a mesma subscrição do ponto final para a mais do que uma aplicação do LUIS, também deve incluir o ID de subscrição e uma propriedade que diz que é uma chave partilhada. 

> [!div class="nextstepaction"]
> [Saiba mais sobre expressões de exemplo](luis-how-to-add-example-utterances.md)
