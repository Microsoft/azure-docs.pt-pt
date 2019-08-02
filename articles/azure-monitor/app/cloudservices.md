---
title: Application Insights para serviços de nuvem do Azure | Microsoft Docs
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
ms.openlocfilehash: 64995ad0560efd06bfa0084c948527e8a01e1890
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "67443340"
---
# <a name="application-insights-for-azure-cloud-services"></a>Application Insights para serviços de nuvem do Azure
[Application insights][start] pode monitorar os [aplicativos de serviço de nuvem do Azure](https://azure.microsoft.com/services/cloud-services/) quanto à disponibilidade, ao desempenho, às falhas e ao uso combinando dados de SDKs de Application Insights com dados de [diagnóstico do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) de seus serviços de nuvem. Com o feedback que recebe relativamente ao desempenho e à eficácia da sua aplicação no terreno, pode fazer escolhas informadas sobre o rumo do design em cada ciclo de vida do desenvolvimento.

![Painel de visão geral](./media/cloudservices/overview-graphs.png)

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, você precisa:

* Uma assinatura [do Azure](https://azure.com) . Entre com seu conta Microsoft para Windows, Xbox Live ou outros serviços de nuvem da Microsoft. 
* Microsoft Azure Tools 2,9 ou posterior.
* Developer Analytics Tools 7,10 ou posterior.

## <a name="get-started-quickly"></a>Início rápido
A forma mais rápida e fácil de monitorizar o seu serviço cloud com o Application Insights é escolher essa opção quando publica o seu serviço no Azure.

![Página de configurações de diagnóstico de exemplo](./media/cloudservices/azure-cloud-application-insights.png)

Essa opção instrumenta seu aplicativo em tempo de execução, fornecendo a você toda a telemetria necessária para monitorar solicitações, exceções e dependências em sua função Web. Ele também monitora os contadores de desempenho de suas funções de trabalho. Todos os rastreamentos de diagnóstico gerados por seu aplicativo também são enviados para Application Insights.

Se essa opção for tudo de que você precisa, você está pronto. 

Suas próximas etapas estão [exibindo métricas do seu aplicativo](../../azure-monitor/app/metrics-explorer.md), [consultando seus dados com a análise](../../azure-monitor/app/analytics.md). 

Para monitorar o desempenho no navegador, talvez você também queira configurar os [testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md) e [Adicionar código às suas páginas da Web](../../azure-monitor/app/javascript.md).

As próximas seções discutem as seguintes opções adicionais:

* Envie dados de vários componentes e crie configurações para separar recursos.
* Adicionar telemetria personalizada a partir da sua aplicação.

## <a name="sample-app-instrumented-with-application-insights"></a>Aplicativo de exemplo instrumentado com Application Insights
Neste [aplicativo de exemplo](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService), Application insights é adicionado a um serviço de nuvem com duas funções de trabalho hospedadas no Azure. 

Na próxima seção, você aprenderá a adaptar seu próprio projeto de serviço de nuvem da mesma maneira.

## <a name="plan-resources-and-resource-groups"></a>Planear recursos e grupos de recursos
A telemetria do seu aplicativo é armazenada, analisada e exibida em um recurso do Azure do tipo Application Insights. 

Cada recurso pertence a um grupo de recursos. Os grupos de recursos são usados para gerenciar custos, conceder acesso a membros da equipe e implantar atualizações em uma única transação coordenada. Por exemplo, você pode [escrever um script para implantar](../../azure-resource-manager/resource-group-template-deploy.md) um serviço de nuvem do Azure e seu Application insights monitorar recursos em uma única operação.

### <a name="resources-for-components"></a>Recursos para componentes
Recomendamos que você crie um recurso separado para cada componente do seu aplicativo. Ou seja, você cria um recurso para cada função Web e função de trabalho. Você pode analisar cada componente separadamente, mas cria um [painel](../../azure-monitor/app/overview-dashboard.md) que reúne os gráficos de chave de todos os componentes, para que você possa compará-los e monitorá-los juntos em uma única exibição. 

Uma abordagem alternativa é enviar a telemetria de mais de uma função para o mesmo recurso, mas [Adicionar uma propriedade de dimensão a cada item](../../azure-monitor/app/api-filtering-sampling.md#add-properties-itelemetryinitializer) de telemetria que identifica sua função de origem. Nessa abordagem, os gráficos de métricas, como exceções, normalmente mostram uma agregação das contagens das várias funções, mas você pode segmentar o gráfico pelo identificador de função, conforme necessário. Você também pode filtrar pesquisas pela mesma dimensão. Essa alternativa torna um pouco mais fácil exibir tudo ao mesmo tempo, mas também pode levar a uma certa confusão entre as funções.

Normalmente, a telemetria do browser está incluída no mesmo recurso que a função da Web do lado do servidor.

Coloque os recursos de Application Insights para os vários componentes em um grupo de recursos. Essa abordagem facilita o gerenciamento em conjunto. 

### <a name="separate-development-test-and-production"></a>Separe o desenvolvimento, os testes e a produção
Se estiver a desenvolver eventos personalizados para a sua próxima funcionalidade enquanto a versão anterior está publicada, vai querer enviar a telemetria de desenvolvimento para um recurso do Application Insights separado. Caso contrário, pode ser difícil encontrar a telemetria de teste entre todo o tráfego do site ativo.

Para evitar essa situação, crie recursos separados para cada configuração de compilação ou "Stamp" (desenvolvimento, teste, produção e assim por diante) do seu sistema. Ponha os recursos de cada configuração de compilação num grupo de recursos separado. 

Para enviar a telemetria para os recursos apropriados, você pode configurar o SDK do Application Insights para que ele escolha uma chave de instrumentação diferente, dependendo da configuração da compilação. 

## <a name="create-an-application-insights-resource-for-each-role"></a>Criar um recurso do Application Insights para cada função

Se você decidiu criar um recurso separado para cada função e, talvez, um conjunto separado para cada configuração de compilação, é mais fácil criá-los no portal de Application Insights. Se você criar muitos recursos, poderá automatizar [o processo](../../azure-monitor/app/powershell.md).

1. Na [portal do Azure][portal], selecione **novo** > **Serviços** > de desenvolvedor**Application insights**.  

    ![Painel de Application Insights](./media/cloudservices/01-new.png)

1. Na lista suspensa **tipo de aplicativo** , selecione **aplicativo Web ASP.net**.  
    Cada recurso é identificado por uma chave de instrumentação. Você pode precisar dessa chave mais tarde se quiser configurar manualmente ou verificar a configuração do SDK.


## <a name="set-up-azure-diagnostics-for-each-role"></a>Configurar os Diagnósticos do Azure para cada função
Defina esta opção para monitorizar a sua aplicação com o Application Insights. Para funções Web, essa opção fornece monitoramento de desempenho, alertas, diagnósticos e análise de uso. Para outras funções, você pode pesquisar e monitorar Diagnóstico do Azure como reinicialização, contadores de desempenho e chamadas para System. Diagnostics. Trace. 

1. No Visual Studio Gerenciador de soluções, em  **\<** **funções**de > > de seuserviçodenuvem, abra as propriedades de cada função.

1. Em **configuração**, marque a caixa de seleção **enviar dados de diagnóstico para Application insights** e, em seguida, selecione o recurso de Application insights que você criou anteriormente.

Se tiver optado por utilizar um recurso do Application Insights separado para cada configuração de compilação, selecione primeiro a configuração.

![Configurar Application Insights](./media/cloudservices/configure-azure-diagnostics.png)

Isso tem o efeito de inserir suas chaves de instrumentação de Application Insights nos arquivos chamados de *configuração.\*. cscfg*. Aqui está o [código de exemplo](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg).

Se você quiser variar o nível de informações de diagnóstico que é enviado para Application Insights, poderá fazer isso [editando os arquivos *. cscfg* diretamente](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md).

## <a name="sdk"></a>Instalar o SDK em cada projeto
Com essa opção, você pode adicionar telemetria de negócios personalizada a qualquer função. A opção fornece uma análise mais detalhada de como seu aplicativo é usado e executado.

No Visual Studio, configure o SDK do Application Insights para cada projeto de aplicação na cloud.

1. Para configurar **funções Web**, clique com o botão direito do mouse no projeto e, em seguida, selecione **Configurar Application Insights** ou **Adicionar > Application insights telemetria**.

1. Para configurar **funções de trabalho**: 

    a. Clique com o botão direito do mouse no projeto e selecione **gerenciar pacotes NuGet**.

    b. Adicione [Application Insights para Servidores Windows](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/).

    ![Procurar “Application Insights”](./media/cloudservices/04-ai-nuget.png)

1. Para configurar o SDK para enviar dados para o recurso de Application Insights:

    a. Em uma função de inicialização adequada, defina a chave de instrumentação a partir da definição de configuração no arquivo *. cscfg* :
 
    ```csharp
   
     TelemetryConfiguration.Active.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("APPINSIGHTS_INSTRUMENTATIONKEY");
    ```
   
    b. Repita "Step a" para cada função em seu aplicativo. Veja os exemplos:
   
    * [Função da Web](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
    * [Função de trabalho](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
    * [Para páginas da Web](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13) 

1. Defina o arquivo *ApplicationInsights. config* para ser copiado sempre para o diretório de saída.  
    Uma mensagem no arquivo *. config* solicita que você coloque a chave de instrumentação lá. No entanto, para aplicativos de nuvem, é melhor defini-lo a partir do arquivo *. cscfg* . Essa abordagem garante que a função seja corretamente identificada no Portal.

## <a name="set-up-status-monitor-to-collect-full-sql-queries-optional"></a>Configurar Status Monitor para coletar consultas SQL completas (opcional)

Esta etapa só será necessária se você quiser capturar consultas SQL completas em .NET Framework. 

1. Em `\*.csdef` arquivo adicionar [tarefa de inicialização](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks) para cada função semelhante a 

    ```xml
    <Startup>
      <Task commandLine="AppInsightsAgent\InstallAgent.bat" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="ApplicationInsightsAgent.DownloadLink" value="http://go.microsoft.com/fwlink/?LinkID=522371" />
          <Variable name="RoleEnvironment.IsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```
    
2. Baixe [InstallAgent. bat](https://github.com/microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent/InstallAgent.bat) e [InstallAgent. ps1](https://github.com/microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent/InstallAgent.ps1), coloque `AppInsightsAgent` -os na pasta em cada projeto de função. Certifique-se de copiá-los para o diretório de saída por meio de propriedades de arquivo do Visual Studio ou scripts de compilação.

3. Em todas as funções de trabalho, adicione variáveis de ambiente: 

    ```xml
      <Environment>
        <Variable name="COR_ENABLE_PROFILING" value="1" />
        <Variable name="COR_PROFILER" value="{324F817A-7420-4E6D-B3C1-143FBED6D855}" />
        <Variable name="MicrosoftInstrumentationEngine_Host" value="{CA487940-57D2-10BF-11B2-A3AD5A13CBC0}" />
      </Environment>
    ```
    
## <a name="run-and-publish-the-app"></a>Executar e publicar a aplicação

1. Execute seu aplicativo e entre no Azure. 

1. Abra os recursos de Application Insights que você criou.  
    Os pontos de dados individuais são exibidos na [pesquisa](../../azure-monitor/app/diagnostic-search.md)e os dados agregados são exibidos no [Gerenciador](../../azure-monitor/app/metrics-explorer.md)de métricas. 

1. Adicione mais telemetria (consulte as próximas seções) e, em seguida, publique seu aplicativo para obter o diagnóstico ao vivo e os comentários de uso. 

Se não houver dados, faça o seguinte:
1. Para exibir eventos individuais, abra o bloco [Pesquisar][diagnostic] .
1. No aplicativo, abra várias páginas para que ele gere uma telemetria.
1. Aguarde alguns segundos e clique em **Atualizar**.  
    Para obter mais informações, consulte [Resolução de problemas][qna].

## <a name="view-azure-diagnostics-events"></a>Exibir eventos de Diagnóstico do Azure
Você pode encontrar as informações de [diagnóstico do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) em Application insights nos seguintes locais:

* Os contadores de desempenho são apresentados como métricas personalizadas. 
* Os registos de eventos do Windows são mostrados como rastreios e eventos personalizados.
* Os registos de aplicações, os registos ETW e eventuais registos de infraestrutura de diagnósticos aparecem como rastreios.

Para exibir contadores de desempenho e contagens de eventos, abra [Metrics Explorer](../../azure-monitor/app/metrics-explorer.md) e adicione o seguinte gráfico:

![Diagnóstico do Azure dados](./media/cloudservices/23-wad.png)

Para pesquisar em vários logs de rastreamento que são enviados por Diagnóstico do Azure, use a [pesquisa](../../azure-monitor/app/diagnostic-search.md) ou uma [consulta de análise](../../azure-monitor/log-query/get-started-portal.md). Por exemplo, suponha que você tenha uma exceção sem tratamento que tenha causado uma função para falhar e reciclar. Esta informação apareceria no canal Aplicação do Registo de Eventos do Windows. Você pode usar Pesquisar para exibir o erro do log de eventos do Windows e obter o rastreamento de pilha completo da exceção. Isso ajuda a encontrar a causa raiz do problema.

![Pesquisa Diagnóstico do Azure](./media/cloudservices/25-wad.png)

## <a name="more-telemetry"></a>Mais telemetria
As seções a seguir discutem como obter telemetria adicional de vários aspectos do seu aplicativo.

## <a name="track-requests-from-worker-roles"></a>Rastrear solicitações de funções de trabalho
Nas funções de trabalho, o módulo de pedidos recolhe automaticamente dados sobre os pedidos HTTP. Para obter exemplos de como você pode substituir o comportamento de coleção padrão, consulte o [exemplo de MVCWebRole](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole). 

Pode acompanhar as chamadas para as funções de trabalho da mesma forma que os pedidos HTTP para capturar o desempenho das mesmas. No Application Insights, o tipo de telemetria Pedido mede uma unidade de trabalho com nome do lado do servidor que pode ser temporizada e ter êxito ou falhar independentemente. Embora as solicitações HTTP sejam capturadas automaticamente pelo SDK, você pode inserir seu próprio código para controlar as solicitações às funções de trabalho.

Consulte as duas funções de trabalho de exemplo instrumentadas para relatar solicitações: 
* [WorkerRoleA](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA)
* [WorkerRoleB](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)

## <a name="exceptions"></a>Exceções
Para obter informações sobre como coletar exceções sem tratamento de vários tipos de aplicativos Web, consulte [monitorando exceções no Application insights](../../azure-monitor/app/asp-net-exceptions.md).

A função da Web de exemplo tem controladores MVC5 e Web API 2. As exceções não processadas dos dois são capturadas com os seguintes processadores:

* [AiHandleErrorAttribute](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs) configurado para os controladores MVC5 [, conforme mostrado neste exemplo](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12) 
* [AiWebApiExceptionLogger](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs) configurado para controladores da API Web 2 [, conforme mostrado neste exemplo](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25) 

Para funções de trabalho, você pode rastrear exceções de duas maneiras:

* Use Trackexception (ex.).
* Se você tiver adicionado o pacote NuGet do ouvinte de rastreamento de Application Insights, poderá usar System. Diagnostics. Trace para registrar exceções [, conforme mostrado neste exemplo](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107).

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

Você pode especificar outros contadores de desempenho personalizados ou adicionais do Windows editando *ApplicationInsights. config* [, conforme mostrado neste exemplo](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14).

  ![Contadores de desempenho](./media/cloudservices/002-servers.png)

## <a name="correlated-telemetry-for-worker-roles"></a>Telemetria correlacionada para funções de trabalho
Para uma experiência de diagnóstico avançada, você pode exibir o que levou a uma solicitação de falha ou de alta latência. Com as funções Web, o SDK configura automaticamente uma correlação entre telemetrias relacionadas. 

Para obter essa exibição para as funções de trabalho, você pode usar um inicializador de telemetria personalizado para definir um atributo de contexto Operation.Id comum para toda a telemetria. Isso permite que você veja rapidamente se o problema de latência ou falha foi causado por uma dependência ou seu código. 

Eis como:

* Defina CorrelationId em um CallContext [, conforme mostrado neste exemplo](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36). Nesse caso, estamos usando a ID de solicitação como CorrelationId.
* Adicione uma implementação de personalizada telemetryinitializer personalizada para definir o Operation.Id para o CorrelationId que foi definido anteriormente. Para obter um exemplo, consulte [ItemCorrelationTelemetryInitializer](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13).
* Adicione o inicializador de telemetria personalizado. Você pode fazer isso no arquivo *ApplicationInsights. config* ou no código [, conforme mostrado neste exemplo](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233).

## <a name="client-telemetry"></a>Telemetria de cliente
Para obter telemetria baseada em navegador, como contagens de exibição de página, tempos de carregamento de página ou exceções de script, e para escrever telemetria personalizada em seus scripts de página, consulte [Adicionar o SDK do JavaScript às suas páginas da][client]Web.

## <a name="availability-tests"></a>Testes de disponibilidade
Para garantir que seu aplicativo permaneça dinâmico e responsivo, [Configure testes da Web][availability].

## <a name="display-everything-together"></a>Apresentar tudo em conjunto
Para obter uma visão geral do seu sistema, você pode exibir os gráficos de monitoramento de chaves juntos em um único [painel](../../azure-monitor/app/overview-dashboard.md). Por exemplo, pode afixar as contagens de pedidos e de falhas de cada função. 

Se o seu sistema usar outros serviços do Azure, como Stream Analytics, inclua seus gráficos de monitoramento também. 

Se tiver uma aplicação móvel cliente, utilize o [App Center](../../azure-monitor/learn/mobile-center-quickstart.md). Crie consultas no [Analytics](../../azure-monitor/app/analytics.md) para apresentar as contagens de eventos e afixá-las ao dashboard.

## <a name="example"></a>Exemplo
[O exemplo](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) monitoriza um serviço que tem uma função da Web e duas funções de trabalho.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Exceção "método não encontrado" na execução nos serviços de nuvem do Azure
Compilou para .NET 4.6? O .NET 4,6 não é suportado automaticamente em funções dos serviços de nuvem do Azure. [Instale o .net 4,6 em cada função](../../cloud-services/cloud-services-dotnet-install-dotnet.md) antes de executar seu aplicativo.

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Passos Seguintes
* [Configure sending Azure Diagnostics to Application Insights](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md) (Configurar o envio de Diagnósticos do Azure para o Application Insights)
* [Criar automaticamente Application Insights recursos](../../azure-monitor/app/powershell.md)
* [Automatizar Diagnóstico do Azure](../../azure-monitor/app/powershell-azure-diagnostics.md)
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
