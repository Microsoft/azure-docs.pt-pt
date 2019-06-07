---
title: Serviços cloud do Application Insights para o Azure | Documentos da Microsoft
description: Monitorizar as funções Web e de trabalho eficazmente com o Application Insights
services: application-insights
documentationcenter: ''
keywords: WAD2AI, Diagnóstico do Azure
author: mrbullwinkle
manager: carmonm
ms.assetid: 5c7a5b34-329e-42b7-9330-9dcbb9ff1f88
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.workload: tbd
ms.date: 09/05/2018
ms.author: mbullwin
ms.openlocfilehash: eb7cbb80be12498242363eb8141a468e08cba73a
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66478321"
---
# <a name="application-insights-for-azure-cloud-services"></a>Serviços cloud do Application Insights para o Azure
[O Application Insights] [ start] pode monitorizar [aplicações de serviço cloud do Azure](https://azure.microsoft.com/services/cloud-services/) para disponibilidade, desempenho, falhas e utilização, ao combinar dados de SDKs do Application Insights com [Diagnóstico do azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) dados dos seus serviços cloud. Com o feedback que recebe relativamente ao desempenho e à eficácia da sua aplicação no terreno, pode fazer escolhas informadas sobre o rumo do design em cada ciclo de vida do desenvolvimento.

![Dashboard de descrição geral](./media/cloudservices/overview-graphs.png)

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, terá de:

* Uma [Azure](https://azure.com) subscrição. Inicie sessão com a sua conta Microsoft para Windows, Xbox Live ou outros serviços cloud da Microsoft. 
* Ferramentas do Microsoft Azure 2.9 ou posteriores.
* Developer Analytics Tools 7.10 ou posterior.

## <a name="get-started-quickly"></a>Início rápido
A forma mais rápida e fácil de monitorizar o seu serviço cloud com o Application Insights é escolher essa opção quando publica o seu serviço no Azure.

![Página de definições de diagnóstico de exemplo](./media/cloudservices/azure-cloud-application-insights.png)

Esta opção instrui a sua aplicação em tempo de execução, dando-lhe toda a telemetria de que tem de monitorizar pedidos, exceções e dependências na sua função da web. Também monitora os contadores de desempenho a partir das suas funções de trabalho. Qualquer rastreios de diagnóstico gerados pela sua aplicação também são enviados para o Application Insights.

Se esta opção é tudo o que precisa, terá concluído. 

Os passos seguintes são [ver métricas da sua aplicação](../../azure-monitor/app/metrics-explorer.md), [consultar dados com o Analytics](../../azure-monitor/app/analytics.md). 

Para monitorizar o desempenho no browser, pode também querer configurar [testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md) e [adicionar código às suas páginas Web](../../azure-monitor/app/javascript.md).

As próximas seções discutem as seguintes opções adicionais:

* Enviar dados de vários componentes e configurações de compilação para recursos separados.
* Adicionar telemetria personalizada a partir da sua aplicação.

## <a name="sample-app-instrumented-with-application-insights"></a>Aplicação de exemplo instrumentada com o Application Insights
Nesta [aplicação de exemplo](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService), Application Insights é adicionado a um serviço cloud com duas funções de trabalho alojadas no Azure. 

Na secção seguinte, irá aprender a adaptar o seu próprio projeto de serviço em nuvem da mesma forma.

## <a name="plan-resources-and-resource-groups"></a>Planear recursos e grupos de recursos
A telemetria da sua aplicação é armazenada, analisada e apresentada num recurso do Azure do tipo Application Insights. 

Cada recurso pertence a um grupo de recursos. Grupos de recursos são utilizados para gerir os custos, para conceder acesso aos membros da Equipe e para implementar atualizações numa única transação coordenada. Por exemplo, poderia [escrever um script para implementar](../../azure-resource-manager/resource-group-template-deploy.md) um serviço cloud do Azure e seus recursos numa operação de monitorização do Application Insights.

### <a name="resources-for-components"></a>Recursos para componentes
Recomendamos que crie um recurso separado para cada componente da sua aplicação. Ou seja, vai criar um recurso para cada função da web e a função de trabalho. Pode analisar cada componente em separado, mas criar um [dashboard](../../azure-monitor/app/overview-dashboard.md) que junta os gráficos-chave de todos os componentes, para que possa comparar e monitorizá-las em conjunto numa única vista. 

Uma abordagem alternativa é enviar a telemetria de mais de uma função para o mesmo recurso, mas [adicionar uma propriedade de dimensão para cada item de telemetria](../../azure-monitor/app/api-filtering-sampling.md#add-properties-itelemetryinitializer) que identifica a respetiva função de origem. Nesta abordagem, gráficos de métricas, como exceções, mostram, geralmente, uma agregação das contagens das várias funções, mas pode segmentá-los pelo identificador da função, conforme necessário. Também pode filtrar pesquisas pela mesma dimensão. Esta alternativa faz com que seja um pouco mais fácil ver tudo ao mesmo tempo, mas também poderia levar a alguma confusão entre as funções.

Normalmente, a telemetria do browser está incluída no mesmo recurso que a função da Web do lado do servidor.

Coloque os recursos do Application Insights para os vários componentes num grupo de recursos. Essa abordagem torna mais fácil geri-los em conjunto. 

### <a name="separate-development-test-and-production"></a>Separe o desenvolvimento, os testes e a produção
Se estiver a desenvolver eventos personalizados para a sua próxima funcionalidade enquanto a versão anterior está publicada, vai querer enviar a telemetria de desenvolvimento para um recurso do Application Insights separado. Caso contrário, pode ser difícil encontrar a telemetria de teste entre todo o tráfego do site publicado.

Para evitar esta situação, crie recursos separados para cada configuração de compilação ou "carimbo" (desenvolvimento, teste, produção e assim por diante) do seu sistema. Ponha os recursos de cada configuração de compilação num grupo de recursos separado. 

Para enviar a telemetria para os recursos adequados, pode configurar o Application Insights SDK para que recolha uma chave de instrumentação diferente, dependendo da configuração de compilação. 

## <a name="create-an-application-insights-resource-for-each-role"></a>Criar um recurso do Application Insights para cada função

Se tiver optado por criar um recurso separado para cada função e, talvez um conjunto à parte para cada configuração de compilação, é mais fácil criá-los todos no portal do Application Insights. Se criar recursos muito, pode [automatizar o processo](../../azure-monitor/app/powershell.md).

1. Na [portal do Azure][portal], selecione **New** > **serviços de programação**  >   **O Application Insights**.  

    ![Painel do Application Insights](./media/cloudservices/01-new.png)

1. Na **tipo de aplicação** na lista pendente, selecione **aplicação web ASP.NET**.  
    Cada recurso é identificado por uma chave de instrumentação. Poderá ser necessário esta chave mais tarde, se quiser configurar ou verificar a configuração do SDK manualmente.


## <a name="set-up-azure-diagnostics-for-each-role"></a>Configurar os Diagnósticos do Azure para cada função
Defina esta opção para monitorizar a sua aplicação com o Application Insights. Para funções da web, esta opção fornece monitorização de desempenho, alertas, diagnóstico e análise de utilização. Para outras funções, pode pesquisar e monitorizar diagnósticos do Azure como o reinício, contadores de desempenho e chamadas para Trace. 

1. No Explorador de soluções do Visual Studio, em  **\<YourCloudService >**  > **funções**, abra as propriedades de cada função.

1. Na **Configuration**, selecione a **enviar dados de diagnóstico para o Application Insights** caixa de verificação e, em seguida, selecione o recurso do Application Insights que criou anteriormente.

Se tiver optado por utilizar um recurso do Application Insights separado para cada configuração de compilação, selecione primeiro a configuração.

![Configurar o Application Insights](./media/cloudservices/configure-azure-diagnostics.png)

Isso tem o efeito de inserir as chaves de instrumentação do Application Insights para os ficheiros com o nome *ServiceConfiguration.\*. cscfg*. Aqui está o [código de exemplo](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg).

Se quiser variar o nível de informações de diagnóstico que são enviadas para o Application Insights, pode fazê-lo [ao editar o *. cscfg* ficheiros diretamente](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md).

## <a name="sdk"></a>Instalar o SDK em cada projeto
Com esta opção, pode adicionar telemetria empresarial personalizada a qualquer função. A opção oferece uma análise detalhada de como a sua aplicação é utilizada e executa.

No Visual Studio, configure o SDK do Application Insights para cada projeto de aplicação na cloud.

1. Para configurar **funções da web**, clique com o botão direito no projeto e, em seguida, selecione **configurar o Application Insights** ou **adicionar > telemetria do Application Insights**.

1. Para configurar **funções de trabalho**: 

    a. Com o botão direito no projeto e, em seguida, selecione **gerir pacotes NuGet**.

    b. Adicione [Application Insights para Servidores Windows](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/).

    ![Procurar “Application Insights”](./media/cloudservices/04-ai-nuget.png)

1. Para configurar o SDK para enviar dados para o recurso do Application Insights:

    a. Na função de arranque adequada, defina a chave de instrumentação da definição de configuração no *. cscfg* ficheiro:
 
    ```csharp
   
     TelemetryConfiguration.Active.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("APPINSIGHTS_INSTRUMENTATIONKEY");
    ```
   
    b. Repita "passo um" para cada função na sua aplicação. Veja os exemplos:
   
    * [Função da Web](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
    * [Função de trabalho](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
    * [Para páginas Web](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13) 

1. Definir o *applicationinsights. config* ficheiro a ser sempre copiado para o diretório de saída.  
    Uma mensagem na *. config* ficheiro pede-lhe colocar a chave de instrumentação aqui. No entanto, para aplicações na cloud, é melhor defini-lo a partir da *. cscfg* ficheiro. Esta abordagem garante que a função é identificada corretamente no portal.

#### <a name="run-and-publish-the-app"></a>Executar e publicar a aplicação

1. Executar a sua aplicação e inicie sessão no Azure. 

1. Abra os recursos do Application Insights que criou.  
    Pontos de dados individuais são apresentados na [pesquisa](../../azure-monitor/app/diagnostic-search.md), e os dados agregados são apresentados na [Explorador de métricas](../../azure-monitor/app/metrics-explorer.md). 

1. Adicione mais telemetria (consulte as secções seguintes) e, em seguida, publique a sua aplicação para obter comentários em direto de diagnóstico e utilização. 

Se não houver nenhum dado, faça o seguinte:
1. Para ver eventos individuais, abra a [pesquisa] [ diagnostic] mosaico.
1. Na aplicação, abra várias páginas para gerar alguma telemetria.
1. Aguarde alguns segundos e, em seguida, clique em **atualizar**.  
    Para obter mais informações, consulte [resolução de problemas][qna].

## <a name="view-azure-diagnostics-events"></a>Ver eventos de diagnóstico do Azure
Pode encontrar os [diagnóstico do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) informações no Application Insights nas seguintes localizações:

* Os contadores de desempenho são apresentados como métricas personalizadas. 
* Os registos de eventos do Windows são mostrados como rastreios e eventos personalizados.
* Os registos de aplicações, os registos ETW e eventuais registos de infraestrutura de diagnósticos aparecem como rastreios.

Para ver contadores de desempenho e contagens de eventos, abra [Explorador de métricas](../../azure-monitor/app/metrics-explorer.md) e adicionar o gráfico a seguir:

![Dados de diagnóstico do Azure](./media/cloudservices/23-wad.png)

Para pesquisar pelos vários registos de rastreio que são enviados pelo diagnóstico do Azure, utilize [pesquisa](../../azure-monitor/app/diagnostic-search.md) ou uma [consulta do Analytics](../../azure-monitor/log-query/get-started-portal.md). Por exemplo, suponha que tem uma exceção não processada que fez uma função falhasse e reciclagem. Esta informação apareceria no canal Aplicação do Registo de Eventos do Windows. Pode usar a pesquisa para ver o erro de registo de eventos do Windows e obter o rastreio de pilha completa para a exceção. Se o fizer, ajuda-o a determinar a causa do problema.

![Pesquisa de diagnóstico do Azure](./media/cloudservices/25-wad.png)

## <a name="more-telemetry"></a>Mais telemetria
As próximas seções discutem como obter telemetria adicional de diferentes aspetos da sua aplicação.

## <a name="track-requests-from-worker-roles"></a>Acompanhar pedidos de funções de trabalho
Nas funções de trabalho, o módulo de pedidos recolhe automaticamente dados sobre os pedidos HTTP. Para obter exemplos de como pode substituir o comportamento de coleção padrão, consulte a [exemplo MVCWebRole](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole). 

Pode acompanhar as chamadas para as funções de trabalho da mesma forma que os pedidos HTTP para capturar o desempenho das mesmas. No Application Insights, o tipo de telemetria Pedido mede uma unidade de trabalho com nome do lado do servidor que pode ser temporizada e ter êxito ou falhar independentemente. Embora os pedidos HTTP são capturados automaticamente pelo SDK, pode inserir o seu próprio código para monitorizar os pedidos para funções de trabalho.

Veja as duas funções de trabalho de exemplo instrumentadas para reportar pedidos: 
* [WorkerRoleA](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA)
* [WorkerRoleB](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)

## <a name="exceptions"></a>Exceções
Para obter informações sobre como recolher exceções não tratadas de diversos tipos de aplicação web, consulte [monitorização de exceções no Application Insights](../../azure-monitor/app/asp-net-exceptions.md).

A função da Web de exemplo tem controladores MVC5 e Web API 2. As exceções não processadas dos dois são capturadas com os seguintes processadores:

* [AiHandleErrorAttribute](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs) configurados para controladores MVC5 [conforme mostrado neste exemplo](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12) 
* [AiWebApiExceptionLogger](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs) configurados para controladores Web API 2 [conforme mostrado neste exemplo](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25) 

Para funções de trabalho, pode controlar as exceções de duas formas:

* Utilize TrackException(ex).
* Se tiver adicionado o pacote de NuGet de serviço de escuta de rastreio do Application Insights, pode usar Trace para registar as exceções [conforme mostrado neste exemplo](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107).

## <a name="performance-counters"></a>Contadores de desempenho
São recolhidos por predefinição os contadores seguintes:

* \Process(??APP_WIN32_PROC??)\% Hora do Processador
* \Memory\Available Bytes
* \.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec
* \Process(??APP_WIN32_PROC??)\Private Bytes
* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
* \Processor(_Total)\% Processor Time

Para funções da Web, também são recolhidos estes contadores:

* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec
* \ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time
* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue

Pode especificar personalizadas adicionais ou outros contadores de desempenho do Windows editando *applicationinsights. config* [conforme mostrado neste exemplo](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14).

  ![Contadores de desempenho](./media/cloudservices/002-servers.png)

## <a name="correlated-telemetry-for-worker-roles"></a>Telemetria correlacionada para funções de trabalho
Para uma experiência de diagnóstico avançadas, pode ver o que levou a um pedido com falhas ou com latência elevada. Com as funções da web, o SDK configura automaticamente uma correlação entre a telemetria relacionada. 

Para obter esta vista para funções de trabalho, pode utilizar um inicializador de telemetria personalizado para definir um atributo de contexto Operation.Id para toda a telemetria. Se o fizer, permite-lhe ver rapidamente se o problema de latência ou a falha foi causado por uma dependência ou o seu código. 

Eis como:

* Definir correlationId num CallContext [conforme mostrado neste exemplo](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36). Neste caso, estamos a utilizar o ID do pedido como o correlationId.
* Adicione uma implementação de TelemetryInitializer personalizada para definir o Operation.Id como o correlationId que foi definido anteriormente. Por exemplo, veja [ItemCorrelationTelemetryInitializer](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13).
* Adicione o inicializador de telemetria personalizado. Pode fazer por isso, além da *applicationinsights. config* arquivo ou no código [conforme mostrado neste exemplo](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233).

## <a name="client-telemetry"></a>Telemetria de cliente
Para obter telemetria baseada no browser, como contagens de visualizações de página, tempos de carregamento de página ou exceções de script e para escrever telemetria personalizada nos seus scripts de página, consulte [adicione o SDK JavaScript às suas páginas Web][client].

## <a name="availability-tests"></a>Testes de disponibilidade
Para tornar-se de que a sua aplicação permanece em direto e reativa, [configurar testes web][availability].

## <a name="display-everything-together"></a>Apresentar tudo em conjunto
Para obter uma visão geral do seu sistema, pode exibir a chave de monitorização de gráficos em conjunto num [dashboard](../../azure-monitor/app/overview-dashboard.md). Por exemplo, pode afixar as contagens de pedidos e de falhas de cada função. 

Se seu sistema utilizar outros serviços do Azure, como o Stream Analytics, inclua também os gráficos de monitorização. 

Se tiver uma aplicação móvel cliente, utilize o [App Center](../../azure-monitor/learn/mobile-center-quickstart.md). Crie consultas no [Analytics](../../azure-monitor/app/analytics.md) para apresentar as contagens de eventos e afixá-las ao dashboard.

## <a name="example"></a>Exemplo
[O exemplo](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) monitoriza um serviço que tem uma função da Web e duas funções de trabalho.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Exceção "método não encontrado" em execução nos serviços cloud do Azure
Compilou para .NET 4.6? .NET 4.6 não é suportado automaticamente nas funções de serviços cloud do Azure. [Instalar o .NET 4.6 em cada função](../../cloud-services/cloud-services-dotnet-install-dotnet.md) antes de executar a sua aplicação.

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Passos Seguintes
* [Configure sending Azure Diagnostics to Application Insights](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md) (Configurar o envio de Diagnósticos do Azure para o Application Insights)
* [Criar automaticamente os recursos do Application Insights](../../azure-monitor/app/powershell.md)
* [Automatizar o diagnóstico do Azure](../../azure-monitor/app/powershell-azure-diagnostics.md)
* [Funções do Azure](https://github.com/christopheranderson/azure-functions-app-insights-sample)

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[azure]: ../../azure-monitor/app/app-insights-overview.md
[client]: ../../azure-monitor/app/javascript.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[netlogs]: ../../azure-monitor/app/asp-net-trace-logs.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md 
