---
title: O Application Insights, node. js
titleSuffix: Azure Cognitive Services
description: Crie um bot integrado com um aplicativo de LUIS e Application Insights com o node. js.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 97e29ba166d652f053f754482b6da36a33ec5bb5
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56878184"
---
# <a name="add-luis-results-to-application-insights-and-azure-functions"></a>Adicionar o LUIS resultados para o Application Insights e as funções do Azure
Este tutorial adiciona informações de solicitação e resposta de LUIS para [Application Insights](https://azure.microsoft.com/services/application-insights/) armazenamento de dados de telemetria. Depois de ter esses dados, pode consultá-lo com o idioma de Kusto ou o Power BI para analisar, Agregar, o relatório de em objetivos e entidades de expressão em tempo real. Isto ajuda a análise é determinar se deve utilizar para adicionar ou editar as intenções e entidades da sua aplicação LUIS.

O bot baseia-se com o Bot Framework 3.x e bot de aplicação Web do Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
* Adicionar a biblioteca do Application Insights para um bot de aplicação web
* Capturar e enviar os resultados da consulta de LUIS para o Application Insights
* Consultar o Application Insights para intenção superior, a pontuação e a expressão

## <a name="prerequisites"></a>Pré-requisitos

* O LUIS bot de funções do **[tutorial anterior](luis-nodejs-tutorial-build-bot-framework-sample.md)** com o Application Insights ativado. 

> [!Tip]
> Se ainda não tiver uma subscrição, pode se registrar para uma [conta gratuita](https://azure.microsoft.com/free/).

Todo o código neste tutorial está disponível na [repositório do GitHub do Azure-Samples](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/nodejs) e cada linha associada com este tutorial é Comentada com `//APPINSIGHT:`. 

## <a name="web-app-bot-with-luis"></a>Bot de aplicação Web com os LUIS
Este tutorial parte do princípio de que tem o código que tem a seguinte aparência ou que concluiu o [outro tutorial](luis-nodejs-tutorial-build-bot-framework-sample.md): 

   [!code-javascript[Web app bot with LUIS](~/samples-luis/documentation-samples/tutorial-web-app-bot/nodejs/app.js "Web app bot with LUIS")]

## <a name="add-application-insights-library-to-web-app-bot"></a>Adicionar a biblioteca do Application Insights ao bot de funções
Atualmente, o serviço do Application Insights, usado este bot de funções, recolhe telemetria do Estado geral para o bot. Não recolhe informações de solicitação e resposta de LUIS que terá de verificar e corrigir os seus objetivos e entidades. 

Para capturar o LUIS solicitação e resposta, o bot de aplicação web tem o **[Application Insights](https://www.npmjs.com/package/applicationinsights)** pacote NPM instalado e configurado no **App. js** ficheiro. Em seguida, os manipuladores de caixa de diálogo intenção tem de enviar o LUIS informações de solicitação e resposta para o Application Insights. 

1. No portal do Azure, no serviço de bot de aplicação web, selecione **crie** sob a **gestão de Bot** secção. 

    ![No portal do Azure, no serviço de bot de aplicação web, selecione "Build" na seção "Gerenciamento do Bot".](./media/luis-tutorial-appinsights/build.png)

2. Um novo separador do browser abre-se com o Editor de serviço de aplicações. Selecione o nome da aplicação na barra superior, em seguida, selecione **abra a consola Kudu**. 

    ![Selecione o nome da aplicação na barra superior, em seguida, selecione "Abra a consola Kudu".](./media/luis-tutorial-appinsights/kudu-console.png)

3. Na consola, introduza o seguinte comando para instalar os pacotes de caráter de sublinhado e o Application Insights:

    ```console
    cd site\wwwroot && npm install applicationinsights && npm install underscore
    ```

    ![Utilizar os comandos de npm para instalar os pacotes de caráter de sublinhado e o Application Insights](./media/luis-tutorial-appinsights/npm-install.png)

    Aguarde que os pacotes serem instalados:

    ```console
    luisbot@1.0.0 D:\home\site\wwwroot
    `-- applicationinsights@1.0.1 
      +-- diagnostic-channel@0.2.0 
      +-- diagnostic-channel-publishers@0.2.1 
      `-- zone.js@0.7.6 
    
    npm WARN luisbot@1.0.0 No repository field.
    luisbot@1.0.0 D:\home\site\wwwroot
    +-- botbuilder-azure@3.0.4
    | `-- azure-storage@1.4.0
    |   `-- underscore@1.4.4 
    `-- underscore@1.8.3 
    ```

    Isso é tudo com o separador do browser consola kudu.

## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Capturar e enviar os resultados da consulta de LUIS para o Application Insights
1. No separador do browser Editor do serviço de aplicações, abra a **App. js** ficheiro.

2. Adicione as seguintes bibliotecas NPM em existente `requires` linhas:

   [!code-javascript[Add NPM packages to app.js](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=12-16 "Add NPM packages to app.js")]

3. Criar o objeto Application Insights e utilizar a definição da aplicação web app bot **BotDevInsightsKey**: 

   [!code-javascript[Create the Application Insights object](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=68-80 "Create the Application Insights object")]

4. Adicionar a **appInsightsLog** função:

   [!code-javascript[Add the appInsightsLog function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=82-109 "Add the appInsightsLog function")]

    A última linha da função é onde os dados sejam adicionados ao Application Insights. O nome do evento é **resultados de LUIS**, um nome exclusivo para além dos outros dados telemétricos recolhidos por este bot de funções. 

5. Utilize o **appInsightsLog** função. Adicioná-lo para cada caixa de diálogo intenção:

   [!code-javascript[Use the appInsightsLog function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=117-118 "Use the appInsightsLog function")]

6. Para testar o seu bot de aplicação web, utilize o **teste na Web Chat** funcionalidade. Não verá nenhuma diferença porque todo o trabalho está no Application Insights, não as respostas de bot.

## <a name="view-luis-entries-in-application-insights"></a>Vista LUIS entradas no Application Insights
Abrir o Application Insights para ver as entradas de LUIS. 

1. No portal, selecione **todos os recursos** , em seguida, filtrar por nome do bot da aplicação web. Clique no recurso com o tipo **Application Insights**. O ícone do Application Insights é uma lâmpada. 

    ! [[Pesquisa para o app insights no Portal do Azure](./media/luis-tutorial-appinsights/search-for-app-insights.png)

2. Quando o recurso for aberta, clique nas **pesquisa** ícone da Lupa no painel à extrema direita. Um novo painel para a direita apresenta. Dependendo de quantos dados de telemetria é encontrado, o painel poderão demorar um momento para apresentar. Procure `LUIS-results` e clique em Inserir no teclado. A lista é reduzida de modo a apenas LUIS os resultados da consulta adicionados com este tutorial.

    ![Filtrar para dependências](./media/luis-tutorial-appinsights/app-insights-filter.png)

3. Selecione a entrada superior. Uma nova janela apresenta os dados mais detalhados, incluindo os dados personalizados para a consulta de LUIS na extremidade direita. Os dados incluem a intenção de principal e sua pontuação.

    ![Detalhes de dependência](./media/luis-tutorial-appinsights/app-insights-detail.png)

    Quando tiver terminado, selecione a parte superior direita **X** para regressar à lista de itens de dependência. 


> [!Tip]
> Se pretender guardar a lista de dependência e voltar a ele mais tarde, clique em **... Obter mais** e clique em **guardar favorito**.

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Application Insights para intenção, a pontuação e a expressão de consulta
O Application Insights dá-lhe o poder para consultar os dados com o [Kusto](https://docs.microsoft.com/azure/application-insights/app-insights-analytics#query-data-in-analytics) linguagem, assim como as export-lo para [PowerBI](https://powerbi.microsoft.com). 

1. Clique em **Analytics** na parte superior da dependência listagem, acima da caixa do filtro. 

    ![Botão Análise](./media/luis-tutorial-appinsights/analytics-button.png)

2. É aberta uma janela nova com uma janela de consulta na parte superior e uma janela da tabela de dados abaixo dele. Se tiver utilizado a bases de dados antes, esse esquema é familiar. A consulta inclui todos os itens das últimas 24 horas começando com o nome `LUIS-results`. O **CustomDimensions** coluna tem os resultados da consulta LUIS como pares nome/valor.

    ![Janela de consulta do Analytics](./media/luis-tutorial-appinsights/analytics-query-window.png)

3. Para extrair a intenção de principal, a pontuação e a expressão, adicione o seguinte logo acima da última linha na janela de consulta:

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_intent_intent)
    | extend score = todouble(customDimensions.LUIS_intent_score)
    | extend utterance = tostring(customDimensions.LUIS_text)
    ```

4. Execute a consulta. Desloque-se na extrema direita na tabela de dados. As novas colunas de expressão, pontuação e topIntent estão disponíveis. Clique na coluna topIntent para ordenar.

    ![Objetivo de principal de análise](./media/luis-tutorial-appinsights/app-insights-top-intent.png)


Saiba mais sobre o [linguagem de consulta de Kusto](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) ou [exportar os dados para o Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 

## <a name="next-steps"></a>Passos Seguintes

Outras informações que pretende adicionar aos dados do application insights incluem o ID da aplicação, ID de versão, data da última alteração modelo, data da última train, a última data de publicação. Estes valores ou podem ser obtidos do URL do ponto final (ID da aplicação e o ID de versão) ou de um [criação API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3d) chamar, em seguida, definido nas definições de bot da aplicação web e obtido a partir daí.  

Se estiver a utilizar a mesma subscrição do ponto final para a mais do que uma aplicação do LUIS, também deve incluir o ID de subscrição e uma propriedade que diz que é uma chave partilhada. 

> [!div class="nextstepaction"]
> [Saiba mais sobre expressões de exemplo](luis-how-to-add-example-utterances.md)
