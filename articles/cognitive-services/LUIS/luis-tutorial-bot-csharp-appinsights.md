---
title: O Application Insights,C#
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
ms.openlocfilehash: 720352403fd5f5937669f9838f3974cb0d3f8797
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657790"
---
# <a name="add-luis-results-to-application-insights-from-a-bot-in-c"></a>Adicionar o LUIS resultados para o Application Insights a partir de um Bot emC#

Este tutorial adiciona informações de compreensão de idiomas para e de bot [Application Insights](https://azure.microsoft.com/services/application-insights/) armazenamento de dados de telemetria. Depois de ter esses dados, pode consultá-lo com o idioma de Kusto ou o Power BI para analisar, Agregar, o relatório de em objetivos e entidades de expressão em tempo real. Isto ajuda a análise é determinar se deve utilizar para adicionar ou editar as intenções e entidades da sua aplicação LUIS.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Capturar bot e os dados de compreensão de idiomas no Application Insights
> * Consultar o Application Insights para os dados de compreensão de idiomas

## <a name="prerequisites"></a>Pré-requisitos

* Um bot do Azure bot do serviço, criado com o Application Insights ativado.
* Transferido o código de bot do bot anterior  **[tutorial](luis-csharp-tutorial-bf-v4.md)** . 
* [Emulador de bot](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

Todo o código neste tutorial está disponível na [repositório do GitHub de compreensão de linguagem do Azure-Samples](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/v4/luis-csharp-bot-johnsmith-src-telemetry). 

## <a name="add-application-insights-to-web-app-bot-project"></a>Adicionar o Application Insights ao projeto de bot de aplicação web

Atualmente, o serviço do Application Insights, usado este bot de funções, recolhe telemetria do Estado geral para o bot. Não recolhe informações de LUIS. 

Para capturar as informações de LUIS, o bot de aplicação web tem o **[applicationinsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/)** pacote NuGet instalado e configurado.  

1. No Visual Studio, adicione a dependência à solução. Na **Explorador de soluções**, clique com o botão direito no nome do projeto e selecione **gerir pacotes NuGet...** . O Gestor de pacotes de NuGet mostra uma lista de pacotes instalados. 
1. Selecione **navegue** , em seguida, procure **applicationinsights**.
1. Instale o pacote. 

## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Capturar e enviar os resultados da consulta de LUIS para o Application Insights

1. Abra o `LuisHelper.cs` de ficheiro e substitua os conteúdos com o código a seguir. O **LogToApplicationInsights** método capturar o bot e os dados de LUIS e envia-os para o Application Insights como um evento de rastreio com o nome `LUIS`.

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

## <a name="add-application-insights-instrumentation-key"></a>Adicionar chave de instrumentação do Application Insights 

Para adicionar dados para o application insights, terá da chave de instrumentação.

1. Num browser, na [portal do Azure](https://portal.azure.com), localize o seu bot **Application Insights** recursos. Seu nome terá a maior parte do nome do bot, em seguida, aleatório carateres no final do nome, como `luis-csharp-bot-johnsmithxqowom`. 
1. No recurso do Application Insights, sobre o **descrição geral** página, copie a **chave de instrumentação**.
1. No Visual Studio, abra a **appSettings** ficheiro na raiz do projeto de bot. Este ficheiro contém todas as variáveis de ambiente.
1. Adicionar uma nova variável, `BotDevAppInsightsKey` com o valor da sua chave de instrumentação. O valor deve ser aspas. 

## <a name="build-and-start-the-bot"></a>Criar e iniciar o bot

1. No Visual Studio, crie e execute o bot. 
1. Inicie o emulador de bot e abra o bot. Isso [passo](luis-csharp-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot) é fornecido no tutorial anterior.

1. Faça uma pergunta, o bot. Isso [passo](luis-csharp-tutorial-bf-v4.md#ask-bot-a-question-for-the-book-flight-intent) é fornecido no tutorial anterior.

## <a name="view-luis-entries-in-application-insights"></a>Vista LUIS entradas no Application Insights

Abrir o Application Insights para ver as entradas de LUIS. Pode demorar alguns minutos para que os dados sejam apresentados no Application Insights.

1. Na [portal do Azure](https://portal.azure.com), abra o recurso do Application Insights o bot. 
1. Quando o recurso de abre, selecione **pesquisa** e procure todos os dados nos últimos **30 minutos** com o tipo de evento de **rastreio**. Selecione o rastreio com o nome **LUIS**. 
1. O bot e o LUIS informações estão disponíveis em **propriedades personalizadas**. 

    ![Reveja as propriedades personalizadas do LUIS armazenadas no Application Insights](./media/luis-tutorial-appinsights/application-insights-luis-trace-custom-properties-csharp.png)

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Application Insights para intenção, a pontuação e a expressão de consulta
O Application Insights dá-lhe o poder para consultar os dados com o [Kusto](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview#what-language-do-log-queries-use) linguagem, assim como as export-lo para [Power BI](https://powerbi.microsoft.com). 

1. Selecione **Log (análise)** . É aberta uma janela nova com uma janela de consulta na parte superior e uma janela da tabela de dados abaixo dele. Se tiver utilizado a bases de dados antes, esse esquema é familiar. A consulta representa os dados filtrados anteriores. O **CustomDimensions** coluna tem o bot e as informações de LUIS.
1. Para extrair a intenção de principal, a pontuação e a expressão, adicionar imediatamente o seguinte acima da última linha (o `|top...` linha) na janela de consulta:

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_topScoringIntent_Name)
    | extend score = todouble(customDimensions.LUIS_topScoringIntentScore)
    | extend utterance = tostring(customDimensions.LUIS_query)
    ```

1. Execute a consulta. As novas colunas de expressão, pontuação e topIntent estão disponíveis. Selecione topIntent coluna para ordenar.

Saiba mais sobre o [linguagem de consulta de Kusto](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) ou [exportar os dados para o Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 


## <a name="learn-more-about-bot-framework"></a>Saiba mais sobre o Bot Framework

Saiba mais sobre [Bot Framework](https://dev.botframework.com/).

## <a name="next-steps"></a>Passos Seguintes

Outras informações que pretende adicionar aos dados do application insights incluem o ID da aplicação, ID de versão, data da última alteração modelo, data da última train, a última data de publicação. Estes valores podem ser obtidos a partir do URL de ponto final (app ID e o ID de versão) ou a partir de uma chamada de API de criação, em seguida, definidos nas definições de bot da aplicação web e obtidos a partir daí.  

Se estiver a utilizar a mesma subscrição do ponto final para a mais do que uma aplicação do LUIS, também deve incluir o ID de subscrição e uma propriedade que diz que é uma chave partilhada.

> [!div class="nextstepaction"]
> [Saiba mais sobre expressões de exemplo](luis-how-to-add-example-utterances.md)
