---
title: 'Tutorial: Application Insights, C# -Luis'
titleSuffix: Azure Cognitive Services
description: Este tutorial adiciona informações de bot e Reconhecimento vocal para Application Insights armazenamento de dados de telemetria.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 09/06/2019
ms.author: diberry
ms.openlocfilehash: 51860efdcc440d6b8a4ea57777ad31fa718657b5
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772811"
---
# <a name="tutorial-add-luis-results-to-application-insights-from-a-bot-in-c"></a>Tutorial: Adicionar resultados de LUIS a Application Insights de um bot emC#

Este tutorial adiciona informações de bot e Reconhecimento vocal para [Application insights](https://azure.microsoft.com/services/application-insights/) armazenamento de dados de telemetria. Depois de ter esses dados, você pode consultá-los com a linguagem Kusto ou Power BI para analisar, agregar e relatar tentativas e entidades de expressão em tempo real. Isto ajuda a análise é determinar se deve utilizar para adicionar ou editar as intenções e entidades da sua aplicação LUIS.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Capturar dados de reconhecimento de linguagem e bot no Application Insights
> * Application Insights de consulta para dados de Reconhecimento vocal

## <a name="prerequisites"></a>Pré-requisitos

* Um bot do serviço de bot do Azure, criado com Application Insights habilitado.
* Código de bot baixado do **[tutorial](luis-csharp-tutorial-bf-v4.md)** anterior do bot. 
* [Emulador de bot](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

Todo o código deste tutorial está disponível no [repositório Azure-samples reconhecimento vocal GitHub](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/v4/luis-csharp-bot-johnsmith-src-telemetry). 

## <a name="add-application-insights-to-web-app-bot-project"></a>Adicionar Application Insights ao projeto de bot do aplicativo Web

Atualmente, o serviço do Application Insights, usado este bot de funções, recolhe telemetria do Estado geral para o bot. Ele não coleta informações de LUIS. 

Para capturar as informações do LUIS, o bot do aplicativo Web precisa do pacote NuGet **[Microsoft. ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/)** instalado e configurado.  

1. No Visual Studio, adicione a dependência à solução. Na **Gerenciador de soluções**, clique com o botão direito do mouse no nome do projeto e selecione **gerenciar pacotes NuGet...** . O Gestor de pacotes de NuGet mostra uma lista de pacotes instalados. 
1. Selecione **procurar** e, em seguida, procure **Microsoft. ApplicationInsights**.
1. Instale o pacote. 

## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Capturar e enviar os resultados da consulta de LUIS para o Application Insights

1. Abra o `LuisHelper.cs` arquivo e substitua o conteúdo pelo código a seguir. O método **LogToApplicationInsights** captura os dados bot e Luis e os envia para Application insights como um evento de rastreamento `LUIS`chamado.

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    using System;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.AI.Luis;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Logging;
    using Microsoft.ApplicationInsights;
    using System.Collections.Generic;
    
    namespace Microsoft.BotBuilderSamples
    {
        public static class LuisHelper
        {
            public static async Task<BookingDetails> ExecuteLuisQuery(IConfiguration configuration, ILogger logger, ITurnContext turnContext, CancellationToken cancellationToken)
            {
                var bookingDetails = new BookingDetails();
    
                try
                {
                    // Create the LUIS settings from configuration.
                    var luisApplication = new LuisApplication(
                        configuration["LuisAppId"],
                        configuration["LuisAPIKey"],
                        "https://" + configuration["LuisAPIHostName"]
                    );
    
                    var recognizer = new LuisRecognizer(luisApplication);
    
                    // The actual call to LUIS
                    var recognizerResult = await recognizer.RecognizeAsync(turnContext, cancellationToken);
    
                    LuisHelper.LogToApplicationInsights(configuration, turnContext, recognizerResult);
    
                    var (intent, score) = recognizerResult.GetTopScoringIntent();
                    if (intent == "Book_flight")
                    {
                        // We need to get the result from the LUIS JSON which at every level returns an array.
                        bookingDetails.Destination = recognizerResult.Entities["To"]?.FirstOrDefault()?["Airport"]?.FirstOrDefault()?.FirstOrDefault()?.ToString();
                        bookingDetails.Origin = recognizerResult.Entities["From"]?.FirstOrDefault()?["Airport"]?.FirstOrDefault()?.FirstOrDefault()?.ToString();
    
                        // This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
                        // TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
                        bookingDetails.TravelDate = recognizerResult.Entities["datetime"]?.FirstOrDefault()?["timex"]?.FirstOrDefault()?.ToString().Split('T')[0];
                    }
                }
                catch (Exception e)
                {
                    logger.LogWarning($"LUIS Exception: {e.Message} Check your LUIS configuration.");
                }
    
                return bookingDetails;
            }
            public static void LogToApplicationInsights(IConfiguration configuration, ITurnContext turnContext, RecognizerResult result)
            {
                // Create Application Insights object
                TelemetryClient telemetry = new TelemetryClient();
    
                // Set Application Insights Instrumentation Key from App Settings
                telemetry.InstrumentationKey = configuration["BotDevAppInsightsKey"];
    
                // Collect information to send to Application Insights
                Dictionary<string, string> logProperties = new Dictionary<string, string>();
    
                logProperties.Add("BotConversation", turnContext.Activity.Conversation.Name);
                logProperties.Add("Bot_userId", turnContext.Activity.Conversation.Id);
    
                logProperties.Add("LUIS_query", result.Text);
                logProperties.Add("LUIS_topScoringIntent_Name", result.GetTopScoringIntent().intent);
                logProperties.Add("LUIS_topScoringIntentScore", result.GetTopScoringIntent().score.ToString());
    
    
                // Add entities to collected information
                int i = 1;
                if (result.Entities.Count > 0)
                {
                    foreach (var item in result.Entities)
                    {
                        logProperties.Add("LUIS_entities_" + i++ + "_" + item.Key, item.Value.ToString());
                    }
                }
    
                // Send to Application Insights
                telemetry.TrackTrace("LUIS", ApplicationInsights.DataContracts.SeverityLevel.Information, logProperties);
    
            }
        }
    }
    ```

## <a name="add-application-insights-instrumentation-key"></a>Adicionar chave de instrumentação de Application Insights 

Para adicionar dados ao Application insights, você precisa da chave de instrumentação.

1. Em um navegador, na [portal do Azure](https://portal.azure.com), localize o recurso de **Application insights** do bot. Seu nome terá a maior parte do nome do bot e, em seguida, os caracteres aleatórios no final do nome `luis-csharp-bot-johnsmithxqowom`, como. 
1. No recurso Application Insights, na página **visão geral** , copie a **chave de instrumentação**.
1. No Visual Studio, abra o arquivo **appSettings. JSON** na raiz do projeto bot. Esse arquivo contém todas as suas variáveis de ambiente.
1. Adicione uma nova variável, `BotDevAppInsightsKey` com o valor de sua chave de instrumentação. O valor em deve estar entre aspas. 

## <a name="build-and-start-the-bot"></a>Criar e iniciar o bot

1. No Visual Studio, compile e execute o bot. 
1. Inicie o emulador de bot e abra o bot. Esta [etapa](luis-csharp-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot) é fornecida no tutorial anterior.

1. Faça uma pergunta ao bot. Esta [etapa](luis-csharp-tutorial-bf-v4.md##use-the-bot-emulator-to-test-the-bot) é fornecida no tutorial anterior.

## <a name="view-luis-entries-in-application-insights"></a>Vista LUIS entradas no Application Insights

Abrir o Application Insights para ver as entradas de LUIS. Pode levar alguns minutos para que os dados apareçam em Application Insights.

1. No [portal do Azure](https://portal.azure.com), abra o recurso de Application insights do bot. 
1. Quando o recurso for aberto, selecione **Pesquisar** e pesquise todos os dados nos últimos **30 minutos** com o tipo de evento **rastreamento**. Selecione o rastreamento chamado **Luis**. 
1. As informações de bot e LUIS estão disponíveis em **Propriedades personalizadas**. 

    ![Examine as propriedades personalizadas do LUIS armazenadas no Application Insights](./media/luis-tutorial-appinsights/application-insights-luis-trace-custom-properties-csharp.png)

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Application Insights para intenção, a pontuação e a expressão de consulta
Application Insights dá a você o poder de consultar os dados com o idioma [Kusto](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview#what-language-do-log-queries-use) , bem como exportá-los para [Power bi](https://powerbi.microsoft.com). 

1. Selecione **log (análise)** . É aberta uma janela nova com uma janela de consulta na parte superior e uma janela da tabela de dados abaixo dele. Se tiver utilizado a bases de dados antes, esse esquema é familiar. A consulta representa os dados filtrados anteriormente. A coluna **CustomDimensions** tem as informações de bot e Luis.
1. Para extrair a principal intenção, a pontuação e a expressão, adicione o seguinte logo acima da última linha (a `|top...` linha) na janela de consulta:

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_topScoringIntent_Name)
    | extend score = todouble(customDimensions.LUIS_topScoringIntentScore)
    | extend utterance = tostring(customDimensions.LUIS_query)
    ```

1. Execute a consulta. As novas colunas de expressão, pontuação e topIntent estão disponíveis. Selecione a coluna topIntent para classificar.

Saiba mais sobre a [linguagem de consulta Kusto](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) ou [exporte os dados para Power bi](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 


## <a name="learn-more-about-bot-framework"></a>Saiba mais sobre o Bot Framework

Saiba mais sobre [Bot Framework](https://dev.botframework.com/).

## <a name="next-steps"></a>Passos Seguintes

Outras informações que pretende adicionar aos dados do application insights incluem o ID da aplicação, ID de versão, data da última alteração modelo, data da última train, a última data de publicação. Esses valores podem ser recuperados da URL do ponto de extremidade (ID do aplicativo e ID da versão) ou de uma chamada à API de criação e, em seguida, definidos nas configurações de bot do aplicativo Web e extraídos de lá.  

Se estiver a utilizar a mesma subscrição do ponto final para a mais do que uma aplicação do LUIS, também deve incluir o ID de subscrição e uma propriedade que diz que é uma chave partilhada.

> [!div class="nextstepaction"]
> [Saiba mais sobre expressões de exemplo](luis-how-to-add-example-utterances.md)
