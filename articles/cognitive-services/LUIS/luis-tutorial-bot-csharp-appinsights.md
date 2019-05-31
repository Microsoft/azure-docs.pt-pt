---
title: Application Insights comC#
titleSuffix: Azure Cognitive Services
description: Crie um bot integrado com um aplicativo de LUIS e Application Insights com c#.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/30/2019
ms.author: diberry
ms.openlocfilehash: 56ceb48be9d5cc9d1cdceed7505e2e3e918a7286
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399667"
---
# <a name="add-luis-results-to-application-insights-with-a-bot-in-c"></a>Adicionar o LUIS resultados para o Application Insights com um Bot emC#

Este tutorial adiciona informações de resposta de LUIS para [Application Insights](https://azure.microsoft.com/services/application-insights/) armazenamento de dados de telemetria. Depois de ter esses dados, pode consultá-lo com o idioma de Kusto ou o Power BI para analisar, Agregar, o relatório de em objetivos e entidades de expressão em tempo real. Isto ajuda a análise é determinar se deve utilizar para adicionar ou editar as intenções e entidades da sua aplicação LUIS.

O bot baseia-se com o Bot Framework 3.x e bot de aplicação Web do Azure. R [Bot Framework 4.x com tutorial de LUIS](luis-csharp-tutorial-bf-v4.md) também está disponível.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicionar o Application Insights para um bot de aplicação web
> * Capturar e enviar os resultados da consulta de LUIS para o Application Insights
> * Consultar o Application Insights para intenção superior, a pontuação e a expressão

## <a name="prerequisites"></a>Pré-requisitos

* O LUIS bot de funções do **[tutorial anterior](luis-csharp-tutorial-build-bot-framework-sample.md)** com o Application Insights ativado.
* [Visual Studio 2017](https://www.visualstudio.com/downloads/) instalado localmente no seu computador.

> [!Tip]
> Se ainda não tiver uma subscrição, pode se registrar para uma [conta gratuita](https://azure.microsoft.com/free/).

Todo o código neste tutorial está disponível na [repositório do GitHub do Azure-Samples](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/csharp) e cada linha associada com este tutorial é Comentada com `//LUIS Tutorial:`.

## <a name="review-luis-web-app-bot"></a>Reveja o LUIS bot de funções

Este tutorial parte do princípio de que tem o código que tem a seguinte aparência ou que concluiu o [outro tutorial](luis-csharp-tutorial-build-bot-framework-sample.md):

   [!code-csharp[Web app bot with LUIS](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs "Web app bot with LUIS")]

## <a name="application-insights-in-web-app-bot"></a>Application Insights no bot de funções

Atualmente, o serviço do Application Insights, adicionado como parte da criação do serviço de web app bot, recolhe telemetria do Estado geral para o bot. Não recolhe informações de resposta do LUIS. Para analisar e melhorar o LUIS, terá das informações de resposta do LUIS.  

Para capturar a resposta do LUIS, tem do bot de funções **[Application Insights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/)** instalado e configurado para o projeto.

## <a name="download-web-app-bot"></a>Baixe o bot de funções

Uso [Visual Studio 2017](https://www.visualstudio.com/downloads/) para adicionar e configurar o Application Insights para o bot de aplicação web. Para utilizar o bot de aplicação web no Visual Studio, Baixe o código de bot de aplicação web.

1. No portal do Azure, para o bot de aplicação web, selecione **criar**.

    ![Selecione a compilação no portal](./media/luis-tutorial-bot-csharp-appinsights/download-build-menu.png)

2. Selecione **ficheiro zip de transferência** e esperar até que o ficheiro está preparado.

    ![Transferir o ficheiro zip](./media/luis-tutorial-bot-csharp-appinsights/download-link.png)

3. Selecione **ficheiro zip de transferência** na janela de pop-up. Lembre-se a localização no seu computador, irá precisar na próxima seção.

    ![Baixe o pop-up de ficheiro de zip](./media/luis-tutorial-bot-csharp-appinsights/download-popup.png)

## <a name="open-solution-in-visual-studio-2017"></a>Abra a solução no Visual Studio 2017

1. Extraia o ficheiro para uma pasta.

2. Abra o Visual Studio 2017 e abra o arquivo da solução, `Microsoft.Bot.Sample.LuisBot.sln`. Se o aviso de segurança é exibida, selecione "OK".

    ![Abra a solução no Visual Studio 2017](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-security-warning.png)

3. Visual Studio tem de adicionar as dependências à solução. Na **Explorador de soluções**, clique com o botão direito no **referências**e selecione **gerir pacotes NuGet...** .

    ![Gerir pacotes NuGet](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-manage-nuget-packages.png)

4. O Gestor de pacotes de NuGet mostra uma lista de pacotes instalados. Selecione **restaurar** na barra de amarela. Aguarde o conclusão do processo de restauro.

    ![Restaurar pacotes NuGet](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-restore-packages.png)

## <a name="add-application-insights-to-the-project"></a>Adicionar o Application Insights ao projeto

Instalar e configurar o Application Insights no Visual Studio.

1. No Visual Studio 2017, no menu superior, selecione **Project**, em seguida, selecione **adicionar telemetria do Application Insights...** .

2. Na **configuração do Application Insights** janela, selecione **começar gratuitamente**

    ![Iniciar a configuração do Application Insights](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-configure-app-insights.png)

3. Registe a sua aplicação com o Application Insights. Poderá ter de introduzir as credenciais do portais do Azure.

4. O Visual Studio adiciona o Application Insights ao projeto, exibir o status como fazê-lo.

    ![Estado do Application Insights](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-adding-application-insights-to-project.png)

    Quando o processo for concluído, o **configuração do Application Insights** mostra o estado do progresso.

    ![Estado de progresso do Application Insights](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-configured-application-insights-to-project.png)

    O **ativar a recolha de rastreio** está vermelha, o que significa que não está ativada. Este tutorial não utiliza a funcionalidade.

## <a name="build-and-resolve-errors"></a>Criar e resolver erros

1. Criar a solução ao selecionar o **crie** menu, em seguida, selecione **reconstruir solução**. Aguarde que a compilação seja concluída.

2. Se a compilação falhar com `CS0104` erros, tem de corrigi-los. Na `Controllers` pasta, na `MessagesController.cs file`, corrigir a utilização de ambígua de `Activity` tipo colocando um tipo de atividade com o tipo de conector. Para fazer isso, altere o nome `Activity` em linhas 22 e 36 partir `Activity` para `Connector.Activity`. Compile a solução novamente. Não deve haver nenhuma mais erros de compilação.

    O código-fonte completo desse arquivo é:

    [!code-csharp[MessagesController.cs file](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/MessagesController.cs "MessagesController.cs file")]

## <a name="publish-project-to-azure"></a>Publicar o projeto no Azure

O **Application Insights** pacote está agora no projeto e configurado corretamente para as suas credenciais no portal do Azure. As alterações para o projeto tem de ser publicada novamente para o Azure.

1. Na **Explorador de soluções**, clique com o botão direito no nome do projeto, em seguida, selecione **Publish**.

    ![Publicar o projeto no portal](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish.png)

2. Na **Publish** janela, selecione **criar novo perfil**.

    ![Como parte da publicação, crie novo perfil.](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish-1.png)

3. Selecione **importar perfil**e selecione **OK**.

    ![Como parte da publicação, importar perfil](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish-2.png)

4. Na **Importar ficheiro de definições de publicação** windows, navegue até à pasta do projeto, navegue para o `PostDeployScripts` pasta, selecione o ficheiro que termina em `.PublishSettings`e selecione `Open`. Agora que configurou a publicação para esse projeto.

5. Publicar o seu código-fonte local para o serviço de Bot, selecionando o **publicar** botão. O **saída** janela mostra o estado. O resto do tutorial é concluído no portal do Azure. Feche o Visual Studio 2017.

## <a name="open-three-browser-tabs"></a>Abra os três separadores de browser

No portal do Azure, encontre o bot de funções web e abri-lo. Os passos seguintes utilizam três exibições diferentes do bot de aplicação web. Pode ser mais fácil de ter três separadores separados abrir no browser:
  
>  * Teste na Web Chat
>  * Editor de código online de compilação/Open-> Editor do serviço de aplicações
>  * Consola Kudu aberto/Editor do serviço de aplicações -> Console de diagnóstico

## <a name="modify-basicluisdialogcs-code"></a>Modificar o código de BasicLuisDialog.cs

1. Na **Editor do serviço de aplicações** separador do browser, abra o `BasicLuisDialog.cs` ficheiro.

2. Adicione a seguinte dependência do NuGet em existente `using` linhas:

   [!code-csharp[Add using statement](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=11-12 "Add using statement")]

3. Adicionar o `LogToApplicationInsights` função:

   [!code-csharp[Add the LogToApplicationInsights function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=61-92 "Add the LogToApplicationInsights function")]

    A chave de instrumentação do Application Insights já está a definição de aplicação do bot de aplicação web com o nome `BotDevInsightsKey`.

    A última linha da função adiciona os dados para o Application Insights. Nome do rastreio é `LUIS`, um nome exclusivo para além dos outros dados telemétricos recolhidos por este bot de funções. Todas as propriedades também são prefixadas com `LUIS_` para que possa ver quais os dados neste tutorial adiciona em comparação com a informação que é fornecida pelo bot de aplicação web.

4. Chamar o `LogToApplicationInsights` função na parte superior do `ShowLuisResult` função:

   [!code-csharp[Use the LogToApplicationInsights function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=114-115 "Use the LogToApplicationInsights function")]

## <a name="build-web-app-bot"></a>Criar bot de funções

1. Crie o bot de aplicação web em uma de duas formas. O primeiro método é com o botão direito no `build.cmd` no **Editor do serviço de aplicações**, em seguida, selecione **executar consola**. A saída da consola apresenta e é concluída com `Finished successfully.`

2. Se isso não concluir com êxito, terá de abrir a consola, navegue para o script e executá-la com seguintes passos. Na **Editor do serviço de aplicações**, na barra azul superior, selecione o nome do seu bot, em seguida, selecione **abra a consola Kudu** na lista pendente.

    ![Consola Kudu aberto](./media/luis-tutorial-bot-csharp-appinsights/open-kudu-console.png)

3. Na janela da consola, introduza o seguinte comando:

    ```console
    cd site\wwwroot && build.cmd
    ```

    Aguarde que a compilação a ser concluída com `Finished successfully.`

## <a name="test-the-web-app-bot"></a>O bot de funções de teste

1. Para testar o seu bot de aplicação web, abra a **teste na Web Chat** recurso no portal.

2. Introduza a frase `Coffee bar on please`.  

    ![Bot de funções de teste em bate-papo](./media/luis-tutorial-bot-csharp-appinsights/test-in-web-chat.png)

3. Não verá nenhuma diferença na resposta chatbot. A alteração está a enviar dados para o Application Insights, não no bot respostas. Introduza expressões mais alguns, portanto, há um pouco mais dados no Application Insights:

|Expressões|
|--|
|Volte a fornecer uma pizza|
|Desativar todas as luzes|
|Ativar a luz hall|


## <a name="view-luis-entries-in-application-insights"></a>Vista LUIS entradas no Application Insights

Abrir o Application Insights para ver as entradas de LUIS.

1. No portal, selecione **todos os recursos** , em seguida, filtrar por nome do bot da aplicação web. Clique no recurso com o tipo **Application Insights**. O ícone do Application Insights é uma lâmpada.

    ![Procurar informações de aplicação no portal do Azure](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights.png)

2. Quando o recurso for aberta, clique nas **pesquisa** ícone da Lupa no painel à extrema direita. Um novo painel para a direita apresenta. Dependendo de quantos dados de telemetria é encontrado, o painel poderão demorar um momento para apresentar. Procurar `LUIS`. A lista é reduzida de modo a apenas LUIS os resultados da consulta adicionados com este tutorial.

    ![Procurar rastreios](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-trace.png)

3. Selecione a entrada superior. Uma nova janela apresenta os dados mais detalhados, incluindo os dados personalizados para a consulta de LUIS na extremidade direita. Os dados incluem a intenção de principal e sua pontuação.

    ![Item de rastreio de revisão](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-trace-item.png)

    Quando tiver terminado, selecione a parte superior direita **X** para regressar à lista de itens de dependência.

> [!Tip]
> Se pretender guardar a lista de dependência e voltar a ele mais tarde, clique em **... Obter mais** e clique em **guardar favorito**.

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Application Insights para intenção, a pontuação e a expressão de consulta

O Application Insights dá-lhe o poder para consultar os dados com o [Kusto](https://docs.microsoft.com/azure/application-insights/app-insights-analytics#query-data-in-analytics) linguagem, assim como as export-lo para [Power BI](https://powerbi.microsoft.com).

1. Clique em **Analytics** na parte superior da dependência listagem, acima da caixa do filtro.

    ![Botão Análise](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-analytics-button.png)

2. É aberta uma janela nova com uma janela de consulta na parte superior e uma janela da tabela de dados abaixo dele. Se tiver utilizado a bases de dados antes, esse esquema é familiar. A consulta inclui todos os itens das últimas 24 horas começando com o nome `LUIS`. O **CustomDimensions** coluna tem os resultados da consulta LUIS como pares nome/valor.

    ![Relatório de análise de predefinição](./media/luis-tutorial-bot-csharp-appinsights/analytics-query-1.png)

3. Para extrair a intenção de principal, a pontuação e a expressão, adicione o seguinte logo acima da última linha na janela de consulta:

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_topScoringIntent)
    | extend score = todouble(customDimensions.LUIS_topScoringIntentScore)
    | extend utterance = tostring(customDimensions.LUIS_query)
    ```

4. Execute a consulta. Desloque-se na extrema direita na tabela de dados. As novas colunas de expressão, pontuação e topIntent estão disponíveis. Clique na coluna topIntent para ordenar.

    ![Relatório de análise personalizada](./media/luis-tutorial-bot-csharp-appinsights/analytics-query-2.png)

Saiba mais sobre o [linguagem de consulta de Kusto](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) ou [exportar os dados para o Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi).

## <a name="learn-more-about-bot-framework"></a>Saiba mais sobre o Bot Framework

Saiba mais sobre [Bot Framework](https://dev.botframework.com/).

## <a name="next-steps"></a>Passos Seguintes

Outras informações que pretende adicionar aos dados do application insights incluem o ID da aplicação, ID de versão, data da última alteração modelo, data da última train, a última data de publicação. Estes valores ou podem ser obtidos do URL do ponto final (ID da aplicação e o ID de versão) ou de um [criação API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3d) chamar, em seguida, definido nas definições de bot da aplicação web e obtido a partir daí.  

Se estiver a utilizar a mesma subscrição do ponto final para a mais do que uma aplicação do LUIS, também deve incluir o ID de subscrição e uma propriedade que diz que é uma chave partilhada.

> [!div class="nextstepaction"]
> [Saiba mais sobre expressões de exemplo](luis-how-to-add-example-utterances.md)
