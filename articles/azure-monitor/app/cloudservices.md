---
title: Insights de Aplicação para serviços em nuvem Azure / Microsoft Docs
description: Monitorizar as funções Web e de trabalho eficazmente com o Application Insights
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 09/05/2018
ms.openlocfilehash: 29482403358936b95fc5e814b68238cc8c25f7a8
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186359"
---
# <a name="application-insights-for-azure-cloud-services"></a>Insights de Aplicação para serviços em nuvem Azure
[O Application Insights][start] pode monitorizar [as aplicações do serviço de cloud Azure](https://azure.microsoft.com/services/cloud-services/) para disponibilidade, desempenho, falhas e utilização, combinando dados de SDKs app Insights com dados de [Diagnóstico Azure](../platform/diagnostics-extension-overview.md) dos seus serviços na nuvem. Com o feedback que recebe relativamente ao desempenho e à eficácia da sua aplicação no terreno, pode fazer escolhas informadas sobre o rumo do design em cada ciclo de vida do desenvolvimento.

![Painel de visão geral](./media/cloudservices/overview-graphs.png)

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, precisa:

* Uma assinatura [Azure.](https://azure.com) Inscreva-se na sua conta microsoft para windows, Xbox Live ou outros serviços na nuvem da Microsoft. 
* Ferramentas Microsoft Azure 2.9 ou posterior.
* Ferramentas de Análise do Desenvolvedor 7.10 ou posteriores.

## <a name="get-started-quickly"></a>Começar rapidamente
A forma mais rápida e fácil de monitorizar o seu serviço cloud com o Application Insights é escolher essa opção quando publica o seu serviço no Azure.

![Página de Definições de Diagnóstico de Exemplo](./media/cloudservices/azure-cloud-application-insights.png)

Esta opção aplica a sua aplicação em tempo de execução, dando-lhe toda a telemetria de que necessita para monitorizar pedidos, exceções e dependências na sua função web. Também monitoriza os contadores de desempenho das suas funções de trabalhador. Quaisquer vestígios de diagnóstico gerados pela sua aplicação também são enviados para o Application Insights.

Se esta opção é tudo o que precisas, estás feito. 

Os seus próximos passos são [visualizar métricas da sua aplicação,](../platform/metrics-charts.md) [consultando os seus dados com o Analytics.](../log-query/log-query-overview.md) 

Para monitorizar o desempenho no navegador, também pode querer configurar [testes de disponibilidade](./monitor-web-app-availability.md) e [adicionar código às suas páginas web](./javascript.md).

As próximas secções discutem as seguintes opções adicionais:

* Envie dados de vários componentes e construa configurações para separar recursos.
* Adicionar telemetria personalizada a partir da sua aplicação.

## <a name="sample-app-instrumented-with-application-insights"></a>App de amostra instrumentalizada com Insights de Aplicação
Nesta [aplicação de amostras,](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService)o Application Insights é adicionado a um serviço de cloud com duas funções de trabalhador hospedados no Azure. 

Na secção seguinte, aprende-se a adaptar o seu próprio projeto de serviço em nuvem da mesma forma.

## <a name="plan-resources-and-resource-groups"></a>Planear recursos e grupos de recursos
A telemetria da sua aplicação é armazenada, analisada e exibida num recurso Azure do tipo Application Insights. 

Cada recurso pertence a um grupo de recursos. Os grupos de recursos são utilizados para gerir custos, para garantir o acesso aos membros da equipa e para implementar atualizações numa única transação coordenada. Por exemplo, pode [escrever um script para implementar](../../azure-resource-manager/templates/deploy-powershell.md) um serviço de cloud Azure e os seus recursos de monitorização de Aplicações Insights numa única operação.

### <a name="resources-for-components"></a>Recursos para componentes
Recomendamos que crie um recurso separado para cada componente da sua aplicação. Ou seja, cria-se um recurso para cada papel web e papel de trabalhador. Pode analisar cada componente separadamente, mas cria um [dashboard](./overview-dashboard.md) que reúne os gráficos-chave de todos os componentes, para que possa compará-los e monitorizá-los juntos numa única vista. 

Uma abordagem alternativa é enviar a telemetria de mais de uma função para o mesmo recurso, mas [adicionar uma propriedade de dimensão a cada item de telemetria](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) que identifique o seu papel de origem. Nesta abordagem, gráficos métricos, como exceções, normalmente mostram uma agregação das contagens das várias funções, mas você pode segmentar o gráfico pelo identificador de funções, se necessário. Também pode filtrar pesquisas pela mesma dimensão. Esta alternativa torna um pouco mais fácil ver tudo ao mesmo tempo, mas também pode levar a alguma confusão entre os papéis.

Normalmente, a telemetria do browser está incluída no mesmo recurso que a função da Web do lado do servidor.

Coloque os recursos de Insights de Aplicação para os vários componentes num único grupo de recursos. Esta abordagem facilita a sua gestão em conjunto. 

### <a name="separate-development-test-and-production"></a>Separe o desenvolvimento, os testes e a produção
Se estiver a desenvolver eventos personalizados para a sua próxima funcionalidade enquanto a versão anterior está publicada, vai querer enviar a telemetria de desenvolvimento para um recurso do Application Insights separado. Caso contrário, pode ser difícil encontrar a sua telemetria de teste entre todo o tráfego do site ao vivo.

Para evitar esta situação, crie recursos separados para cada configuração de construção ou "carimbo" (desenvolvimento, teste, produção, e assim por diante) do seu sistema. Ponha os recursos de cada configuração de compilação num grupo de recursos separado. 

Para enviar a telemetria para os recursos apropriados, pode configurar o Application Insights SDK para que ele apanhe uma chave de instrumentação diferente, dependendo da configuração de construção. 

Aprenda a [definir dinamicamente a chave de instrumentação](./separate-resources.md#dynamic-ikey) para diferentes fases. 

## <a name="create-an-application-insights-resource-for-each-role"></a>Criar um recurso do Application Insights para cada função

Se decidiu criar um recurso separado para cada papel, e talvez um conjunto separado para cada configuração de construção, é mais fácil criá-los todos no portal Application Insights. Se criar muitos recursos, pode [automatizar o processo.](./powershell.md)

1. No [portal Azure,][portal]selecione **New**  >  **Developer Services** Application  >  **Insights**.  

    ![Painel de Insights de Aplicação](./media/cloudservices/01-new.png)

1. Na lista de drop-down do Tipo de **Aplicação,** selecione **ASP.NET aplicação web**.

Cada recurso é identificado por uma chave de instrumentação. Poderá necessitar desta chave mais tarde se pretender configurar manualmente ou verificar a configuração do SDK.


## <a name="set-up-azure-diagnostics-for-each-role"></a>Configurar os Diagnósticos do Azure para cada função
Defina esta opção para monitorizar a sua aplicação com o Application Insights. Para funções web, esta opção fornece monitorização de desempenho, alertas, diagnósticos e análise de utilização. Para outras funções, pode pesquisar e monitorizar diagnósticos Azure tais como reiniciar, contadores de desempenho e chamadas para System.Diagnostics.Trace. 

1. No Visual Studio Solution Explorer, em **\<YourCloudService>**  >  **Roles,** abra as propriedades de cada papel.

1. Na **Configuração**, selecione os **dados de diagnóstico de envio para a** caixa de verificação de Insights de Aplicação e, em seguida, selecione o recurso 'Insights de Aplicação' que criou anteriormente.

Se tiver optado por utilizar um recurso do Application Insights separado para cada configuração de compilação, selecione primeiro a configuração.

![Configure insights de aplicação](./media/cloudservices/configure-azure-diagnostics.png)

Isto tem o efeito de inserir as chaves de instrumentação de Insights de Aplicação nos ficheiros denominados *ServiceConfiguration. \* cscfg*. Aqui está o [código de amostra.](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg)

Se pretender variar o nível de informação de diagnóstico que é enviado para o Application Insights, pode fazê-lo [editando diretamente os *ficheiros .cscfg*](../platform/diagnostics-extension-to-application-insights.md).

## <a name="install-the-sdk-in-each-project"></a><a name="sdk"></a>Instalar o SDK em cada projeto
Com esta opção, pode adicionar telemetria de negócio personalizada a qualquer papel. A opção fornece uma análise mais aprofundada de como a sua aplicação é usada e executa.

No Visual Studio, configure o SDK do Application Insights para cada projeto de aplicação na cloud.

1. Para configurar **funções web,** clique com o botão direito do projeto e, em seguida, selecione **Configure Application Insights** ou **Add > Application Insights telemetria**.

1. Para configurar **funções de trabalhador:** 

    a. Clique com o botão direito no projeto e, em seguida, **selecione Gerir Pacotes NuGet**.

    b. Adicione [Application Insights para Servidores Windows](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/).

    ![Procurar “Application Insights”](./media/cloudservices/04-ai-nuget.png)

1. Para configurar o SDK para enviar dados para o recurso Application Insights:

    a. Numa função de arranque adequada, defina a tecla de instrumentação a partir da definição de configuração no ficheiro *.cscfg:*
 
    ```csharp
   
     TelemetryConfiguration.Active.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("APPINSIGHTS_INSTRUMENTATIONKEY");
    ```
   
    b. Repita "passo a" para cada papel na sua aplicação. Veja os exemplos:
   
    * [Função da Web](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
    * [Função de trabalho](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
    * [Para páginas web](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13) 

1. Desave o ficheiro *ApplicationInsights.config* a ser copiado sempre para o diretório de saída.

   Uma mensagem no ficheiro *.config* pede-lhe para colocar a chave de instrumentação lá. No entanto, para aplicações em nuvem, é melhor defini-lo a partir do ficheiro *.cscfg.* Esta abordagem garante que o papel está corretamente identificado no portal.

## <a name="set-up-status-monitor-to-collect-full-sql-queries-optional"></a>Configurar o Status Monitor para recolher consultas completas sql (opcional)

Este passo só é necessário se quiser capturar consultas SQL completas no Quadro .NET. 

1. Em `\*.csdef` ficheiro Adicionar tarefa de [arranque](../../cloud-services/cloud-services-startup-tasks.md) para cada papel semelhante a 

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
    
2. Descarregue [InstallAgent.bat](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent/InstallAgent.bat) e [InstallAgent.ps1, ](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent/InstallAgent.ps1)coloque-os `AppInsightsAgent` na pasta em cada projeto de função. Certifique-se de copiá-los para o diretório de saída através de propriedades de ficheiros Visual Studio ou construir scripts.

3. Em todas as Funções dos Trabalhadores, adicione variáveis ambientais: 

    ```xml
      <Environment>
        <Variable name="COR_ENABLE_PROFILING" value="1" />
        <Variable name="COR_PROFILER" value="{324F817A-7420-4E6D-B3C1-143FBED6D855}" />
        <Variable name="MicrosoftInstrumentationEngine_Host" value="{CA487940-57D2-10BF-11B2-A3AD5A13CBC0}" />
      </Environment>
    ```
    
## <a name="run-and-publish-the-app"></a>Executar e publicar a aplicação

1. Executar a sua aplicação e entrar no Azure. 

1. Abra os recursos application insights que criou.

   Os pontos de dados individuais são apresentados na [Pesquisa,][diagnostic]e os dados agregados são apresentados no [Metric Explorer](../platform/metrics-charts.md).

1. Adicione mais telemetria (ver as próximas secções) e, em seguida, publique a sua app para obter diagnósticos ao vivo e feedback de utilização. 

Se não houver dados, faça o seguinte:

1. Para visualizar eventos individuais, abra o azulejo [de Pesquisa.][diagnostic]
1. Na aplicação, abra várias páginas para gerar alguma telemetria.
1. Aguarde alguns segundos e, em seguida, clique em **Refresh**.  

Para obter mais informações, consulte [Resolução de problemas][qna].

## <a name="view-azure-diagnostics-events"></a>Ver eventos de Diagnóstico Azure
Pode encontrar a informação do [Azure Diagnostics](../platform/diagnostics-extension-overview.md) em Application Insights nos seguintes locais:

* Os contadores de desempenho são apresentados como métricas personalizadas. 
* Os registos de eventos do Windows são mostrados como rastreios e eventos personalizados.
* Os registos de aplicações, os registos ETW e eventuais registos de infraestrutura de diagnósticos aparecem como rastreios.

Para ver contadores de desempenho e contagem de eventos, abra [o Metrics Explorer](../platform/metrics-charts.md) e adicione o seguinte gráfico:

![Dados do Azure Diagnostics](./media/cloudservices/23-wad.png)

Para pesquisar os vários registos de vestígios enviados pela Azure Diagnostics, utilize [search](./diagnostic-search.md) ou uma [consulta de Analytics](../log-query/log-analytics-tutorial.md). Por exemplo, suponha que tem uma exceção sem manipulação que fez com que um papel se despenhe e recicle. Esta informação apareceria no canal Aplicação do Registo de Eventos do Windows. Pode utilizar a Pesquisa para ver o erro do Registo de Eventos do Windows e obter o rastreio completo da pilha para a exceção. Fazê-lo ajuda-o a encontrar a causa principal do problema.

![Pesquisa de Diagnósticos Azure](./media/cloudservices/25-wad.png)

## <a name="more-telemetry"></a>Mais telemetria
As próximas secções discutem como obter telemetria adicional de vários aspetos da sua app.

## <a name="track-requests-from-worker-roles"></a>Rastreio de pedidos de funções de trabalhador
Nas funções de trabalho, o módulo de pedidos recolhe automaticamente dados sobre os pedidos HTTP. Por exemplo, como pode sobrepor-se ao comportamento de recolha padrão, consulte a [amostra MVCWebRole](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole). 

Pode acompanhar as chamadas para as funções de trabalho da mesma forma que os pedidos HTTP para capturar o desempenho das mesmas. No Application Insights, o tipo de telemetria Pedido mede uma unidade de trabalho com nome do lado do servidor que pode ser temporizada e ter êxito ou falhar independentemente. Embora os pedidos HTTP sejam capturados automaticamente pelo SDK, pode inserir o seu próprio código para rastrear os pedidos às funções dos trabalhadores.

Consulte as duas funções de trabalhador da amostra instrumentadas para reportar pedidos: 
* [OperárioRolea](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA)
* [WorkerRoleB](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)

## <a name="exceptions"></a>Exceções
Para obter informações sobre como recolher exceções não manipuladas de vários tipos de aplicações web, consulte [as exceções de monitorização no Application Insights](./asp-net-exceptions.md).

A função da Web de exemplo tem controladores MVC5 e Web API 2. As exceções não processadas dos dois são capturadas com os seguintes processadores:

* [AiHandleErrorAttribute](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs) configurado para controladores MVC5, [como mostrado neste exemplo](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12) 
* [AiWebApiExceptionLogger](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs) configurado para controladores Web API 2 [como mostrado neste exemplo](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25) 

Para funções de trabalhador, pode acompanhar exceções de duas formas:

* Use TrackException(ex).
* Se tiver adicionado o pacote NuGet de rastreio de rastreio de rastreio de aplicações, pode utilizar System.Diagnostics.Trace para registar exceções [como mostrado neste exemplo](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107).

## <a name="performance-counters"></a>Contadores de desempenho
São recolhidos por predefinição os contadores seguintes:

* \Processo(?? APP_WIN32_PROC??) \% Tempo do processador
* \Memory\Available Bytes
* \.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec
* \Process(??APP_WIN32_PROC??)\Private Bytes
* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
* \Processor(_Total)\% Processor Time

Para funções da Web, também são recolhidos estes contadores:

* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec
* \ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time
* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue

Pode especificar contadores de desempenho personalizados ou outros windows editando *ApplicationInsights.config* [como mostrado neste exemplo.](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14)

  ![Contadores de desempenho](./media/cloudservices/002-servers.png)

## <a name="correlated-telemetry-for-worker-roles"></a>Telemetria correlacionada para funções de trabalhador
Para uma experiência de diagnóstico rica, você pode ver o que levou a um pedido falhado ou de alta latência. Com as funções web, o SDK estabelece automaticamente uma correlação entre a telemetria relacionada. 

Para obter esta visão para funções de trabalhador, você pode usar um inicializador de telemetria personalizado para definir um atributo de contexto Operation.Id comum para toda a telemetria. Ao fazê-lo, permite-lhe ver se o problema de latência ou falha foi causado por uma dependência ou pelo seu código. 

Eis como:

* Coloque a correlaçãoId num Texto de Chamada [como mostrado neste exemplo](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36). Neste caso, estamos a usar o ID do pedido como correlaid.
* Adicione uma implementação personalizada do TelemetryInitializer, para definir o Operation.Id ao correlationId que foi definido anteriormente. Por exemplo, consulte [ItemCorrelationTelemetryInitializer](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13).
* Adicione o inicializador de telemetria personalizado. Pode fazê-lo no ficheiro *ApplicationInsights.config* ou em [código, como mostra este exemplo](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233).

## <a name="client-telemetry"></a>Telemetria de cliente
Para obter telemetria baseada no navegador, como contagens de visualização de página, tempos de carga de página ou exceções de scripts, e para escrever telemetria personalizada nos scripts da sua página, consulte [Adicionar o SDK JavaScript às suas páginas web][client].

## <a name="availability-tests"></a>Testes de disponibilidade
Para se certificar de que a sua aplicação permanece ao vivo e responsiva, [crie testes web][availability].

## <a name="display-everything-together"></a>Apresentar tudo em conjunto
Para uma imagem geral do seu sistema, pode apresentar as tabelas de monitorização das chaves juntas num [tablier](./overview-dashboard.md). Por exemplo, pode afixar as contagens de pedidos e de falhas de cada função. 

Se o seu sistema utilizar outros serviços Azure, como o Stream Analytics, também inclua os seus gráficos de monitorização. 

Se tiver uma aplicação móvel cliente, utilize o [App Center](../learn/mobile-center-quickstart.md). Crie consultas no [Analytics](../log-query/log-query-overview.md) para apresentar as contagens de eventos e afixá-las ao dashboard.

## <a name="example"></a>Exemplo
[O exemplo](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) monitoriza um serviço que tem uma função da Web e duas funções de trabalho.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Exceção "método não encontrado" em funcionamento nos serviços de nuvem Azure
Compilou para .NET 4.6? .NET 4.6 não é suportado automaticamente em funções de serviços em nuvem Azure. [Instale .NET 4.6 em cada função](../../cloud-services/cloud-services-dotnet-install-dotnet.md) antes de executar a sua aplicação.

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Passos seguintes
* [Configure sending Azure Diagnostics to Application Insights](../platform/diagnostics-extension-to-application-insights.md) (Configurar o envio de Diagnósticos do Azure para o Application Insights)
* [Criar automaticamente recursos de Insights de Aplicação](./powershell.md)
* [Automatizar diagnósticos Azure](./powershell-azure-diagnostics.md)
* [Funções do Azure](https://github.com/christopheranderson/azure-functions-app-insights-sample)

[api]: ./api-custom-events-metrics.md
[availability]: ./monitor-web-app-availability.md
[azure]: ./app-insights-overview.md
[client]: ./javascript.md
[diagnostic]: ./diagnostic-search.md
[netlogs]: ./asp-net-trace-logs.md
[portal]: https://portal.azure.com/
[qna]: ../faq.md
[redfield]: ./monitor-performance-live-website-now.md
[start]: ./app-insights-overview.md