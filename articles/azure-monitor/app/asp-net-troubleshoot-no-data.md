---
title: Resolução de problemas sem dados - Application Insights para .NET
description: Não ver dados em Azure Application Insights? Tente aqui.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 05/21/2020
ms.openlocfilehash: fbf53f6d4a928215d25874f4e405147c73cbf81f
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056577"
---
# <a name="troubleshooting-no-data---application-insights-for-netnet-core"></a>Resolução de problemas sem dados - Insights de aplicação para .NET/.NET Core

## <a name="some-of-my-telemetry-is-missing"></a>Falta alguma da minha telemetria.
*No Application Insights, só vejo uma fração dos eventos que estão a ser gerados pela minha app.*

* Se estiver sempre a ver a mesma fração, deve-se provavelmente a [uma amostragem](./sampling.md)adaptativa. Para confirmar isto, abra a Pesquisa (a partir da lâmina de visão geral) e veja uma instância de um Pedido ou outro evento. Na parte inferior da secção de propriedades clique em "..." para obter detalhes completos da propriedade. Se o Pedido de Contagem > 1, então a amostragem está em funcionamento.
* Caso contrário, é possível que esteja a atingir um [limite de taxa de dados](./pricing.md#limits-summary) para o seu plano de preços. Estes limites são aplicados por minuto.

*Estou a sentir perda de dados aleatoriamente.*

* Verifique se está a sentir perda de dados no [Canal de Telemetria](telemetry-channels.md#does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost)

* Verifique se há problemas conhecidos no telemetria Canal [GitHub repo](https://github.com/Microsoft/ApplicationInsights-dotnet/issues)

*Estou a sentir perda de dados na App de Consola ou na Web App quando a aplicação está prestes a parar.*

* O canal SDK mantém a telemetria no tampão e envia-as em lotes. Se o pedido estiver desligado, poderá ter de ligar explicitamente para [o Flush()](api-custom-events-metrics.md#flushing-data). O comportamento `Flush()` depende do [canal](telemetry-channels.md#built-in-telemetry-channels) real usado.

## <a name="no-data-from-my-server"></a>Nenhum dado do meu servidor
*Instalei a minha aplicação no meu servidor web, e agora não vejo nenhuma telemetria dele. Funcionou bem na minha máquina de dev.*

* Provavelmente um problema com firewall. [Desempeciu exceções de firewall para o Application Insights para enviar dados](./ip-addresses.md).
* O IIS Server pode estar a perder alguns pré-requisitos: .NET Extensibilidade 4.5 e ASP.NET 4.5.

*[Instalei o Status Monitor](./monitor-performance-live-website-now.md) no meu servidor web para monitorizar as aplicações existentes. Não vejo resultados.*

* Consulte [o Monitor de Estado de resolução de problemas](./monitor-performance-live-website-now.md#troubleshoot).

> [!IMPORTANT]
> As novas regiões de Azure **requerem** a utilização de cordas de ligação em vez de teclas de instrumentação. [A cadeia de ligação](./sdk-connection-string.md?tabs=net) identifica o recurso com o que pretende associar os seus dados de telemetria. Também permite modificar os pontos finais que o seu recurso utilizará como destino para a sua telemetria. Terá de copiar o fio de ligação e adicioná-lo ao código da sua aplicação ou a uma variável ambiental.


## <a name="filenotfoundexception-could-not-load-file-or-assembly-microsoftaspnet-telemetrycorrelation"></a>FileNotFoundException: Não foi possível carregar ficheiro ou montagem 'Microsoft.AspNet TelemetryCorrelation

Para obter mais informações sobre este erro consulte [edição gitHub 1610 ] https://github.com/microsoft/ApplicationInsights-dotnet/issues/1610) .

Ao atualizar a partir de SDKs mais antigos do que (2.4) é necessário certificar-se de que as seguintes alterações aplicadas `web.config` e `ApplicationInsights.config` :

1. Dois módulos http em vez de um. Em `web.config` você deve ter dois módulos http. A ordem é importante para alguns cenários:

    ``` xml
    <system.webServer>
      <modules>
          <add name="TelemetryCorrelationHttpModule" type="Microsoft.AspNet.TelemetryCorrelation.TelemetryCorrelationHttpModule, Microsoft.AspNet.TelemetryCorrelation" preCondition="integratedMode,managedHandler" />
          <add name="ApplicationInsightsHttpModule" type="Microsoft.ApplicationInsights.Web.ApplicationInsightsHttpModule, Microsoft.AI.Web" preCondition="managedHandler" />
      </modules>
    </system.webServer>
    ```

2. `ApplicationInsights.config`Além `RequestTrackingTelemetryModule` disso, deverá ter o seguinte módulo de telemetria:

    ``` xml
    <TelemetryModules>
      <Add Type="Microsoft.ApplicationInsights.Web.AspNetDiagnosticTelemetryModule, Microsoft.AI.Web"/>
    </TelemetryModules>
    ```

***A não atualização correta pode levar a exceções inesperadas ou a não recolha de telemetria.***


## <a name="no-add-application-insights-option-in-visual-studio"></a><a name="q01"></a>Sem opção 'Adicionar Insights de Aplicação' no Estúdio Visual
*Quando clico com o botão direito de um projeto existente no Solution Explorer, não vejo nenhuma opção de Insights de Aplicação.*

* Nem todos os tipos de projeto .NET são suportados pelas ferramentas. Os projetos Web e WCF são apoiados. Para outros tipos de projetos, como aplicações de desktop ou serviço, ainda pode [adicionar um SDK Application Insights ao seu projeto manualmente](./windows-desktop.md).
* Certifique-se de que tem [Visual Studio 2013 Update 3 ou mais tarde](/visualstudio/releasenotes/vs2013-update3-rtm-vs). Vem pré-instalado com ferramentas Developer Analytics, que fornecem o Application Insights SDK.
* Selecione **Ferramentas, Extensões e Atualizações** e verifique se **as Ferramentas de Análise do Programador** estão instaladas e ativadas.  Em caso afirmativo, clique em **Atualizações** para ver se existe uma atualização disponível.
* Abra o diálogo new project e escolha ASP.NET aplicação Web. Se vir a opção Application Insights, as ferramentas são instaladas. Caso contrário, tente desinstalar e, em seguida, reinstalar as Ferramentas de Análise do Desenvolvedor.

## <a name="adding-application-insights-failed"></a><a name="q02"></a>A adição de Insights de Aplicação falhou
*Quando tento adicionar Insights de Aplicação a um projeto existente, vejo uma mensagem de erro.*

Causas prováveis:

* A comunicação com o portal Application Insights falhou; ou
* Há algum problema com a sua conta Azure;
* Só [leu o acesso à subscrição ou grupo onde estava a tentar criar o novo recurso.](./resources-roles-access-control.md)

Correção:

* Verifique se forneceu credenciais de inscrição para a conta Azure certa.
* No seu navegador, verifique se tem acesso ao [portal Azure.](https://portal.azure.com) Abra as definições e veja se existe alguma restrição.
* [Adicione Insights de Aplicação ao seu projeto existente](./asp-net.md): No Solution Explorer, clique em clique no seu projeto e escolha "Adicionar Insights de Aplicação".

## <a name="i-get-an-error-instrumentation-key-cannot-be-empty"></a><a name="emptykey"></a>Tenho um erro: "A chave de instrumentação não pode estar vazia"
Parece que algo correu mal enquanto instalava Insights de Aplicação ou talvez um adaptador de registo.

No Solution Explorer, clique com o botão direito do seu projeto e escolha **Insights de Aplicação > Configure Application Insights**. Receberá um diálogo que o convida a iniciar sessão no Azure e a criar um recurso Application Insights ou a reutilizar um existente.

## <a name="nuget-packages-are-missing-on-my-build-server"></a><a name="NuGetBuild"></a> "Pacotes NuGet(s) estão em falta" no meu servidor de construção
*Tudo se constrói bem quando estou a depurar a minha máquina de desenvolvimento, mas tenho um erro do NuGet no servidor de construção.*

Consulte a [Restauração do Pacote NuGet](https://docs.nuget.org/Consume/Package-Restore) e [a Restauração automática do pacote.](https://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore)

## <a name="missing-menu-command-to-open-application-insights-from-visual-studio"></a>Falta de comando de menu para abrir Insights de Aplicações do Estúdio Visual
*Quando clico com a direita no meu projeto Solution Explorer, não vejo nenhum comando de Insights de Aplicação, ou não vejo um comando Open Application Insights.*

Causas prováveis:

* Se criou o recurso Application Insights manualmente, ou se o projeto é de um tipo que não é suportado pelas ferramentas Application Insights.
* As ferramentas Developer Analytics são desativadas no seu Estúdio Visual.
* O seu Estúdio Visual é mais antigo que o Update 3 de 2013.

Correção:

* Certifique-se de que a sua versão Visual Studio é atualização 3 ou mais tarde de 2013.
* Selecione **Ferramentas, Extensões e Atualizações** e verifique se **as ferramentas do Developer Analytics** estão instaladas e ativadas.  Em caso afirmativo, clique em **Atualizações** para ver se existe uma atualização disponível.
* Clique com o botão direito no seu projeto no Solution Explorer. Se vir o comando **Application Insights > Configurar Insights de Aplicação,** utilize-o para ligar o seu projeto ao recurso no serviço Application Insights.

Caso contrário, o seu tipo de projeto não é suportado diretamente pelas ferramentas Developer Analytics. Para ver a sua telemetria, inscreva-se no [portal Azure,](https://portal.azure.com)escolha Insights de Aplicação na barra de navegação à esquerda e selecione a sua aplicação.

## <a name="access-denied-on-opening-application-insights-from-visual-studio"></a>'Acesso negado' na abertura de Insights de Aplicações do Visual Studio
*O comando do menu 'Open Application Insights' leva-me ao portal Azure, mas tenho um erro de "acesso negado".*

O início de sposição da Microsoft que utilizou pela última vez no seu navegador padrão não tem acesso [ao recurso criado quando o Application Insights foi adicionado a esta aplicação.](./asp-net.md) Há duas razões prováveis:

* Tem mais do que uma conta microsoft - talvez um trabalho e uma conta pessoal da Microsoft? O sinal que utilizou pela última vez no seu navegador padrão foi para uma conta diferente daquela que tem acesso para [adicionar Insights de Aplicação ao projeto.](./asp-net.md)
  * Correção: Clique no seu nome no topo direito da janela do navegador e assine. Em seguida, inscreva-se na conta que tem acesso. Em seguida, na barra de navegação à esquerda, clique em Insights de Aplicação e selecione a sua aplicação.
* Outra pessoa acrescentou a Application Insights ao projeto, e esqueceram-se de lhe dar [acesso ao grupo de recursos](./resources-roles-access-control.md) em que foi criado.
  * Correção: Se utilizaram uma conta organizacional, podem adicioná-lo à equipa; ou podem conceder-lhe acesso individual ao grupo de recursos.

## <a name="asset-not-found-on-opening-application-insights-from-visual-studio"></a>'Ativo não encontrado' na abertura de Insights de Aplicações do Visual Studio
*O comando do menu 'Open Application Insights' leva-me ao portal Azure, mas tenho um erro de "ativo não encontrado".*

Causas prováveis:

* O recurso Application Insights para a sua aplicação foi eliminado; ou
* A chave de instrumentação foi definida ou alterada em ApplicationInsights.config editando-a diretamente, sem atualizar o ficheiro do projeto.

A chave de instrumentação ApplicationInsights.config controla onde a telemetria é enviada. Uma linha no ficheiro do projeto controla qual o recurso que é aberto quando utiliza o comando no Visual Studio.

Correção:

* No Solution Explorer, clique com o botão direito no projeto e escolha Insights de Aplicação, Configure Application Insights. No diálogo, pode optar por enviar telemetria para um recurso existente ou criar um novo. Ou:
* Abra o recurso diretamente. Inscreva-se [no portal Azure,](https://portal.azure.com)clique em Insights de Aplicação na barra de navegação esquerda e, em seguida, selecione a sua aplicação.

## <a name="where-do-i-find-my-telemetry"></a>Onde encontro a minha telemetria?
*Inscrevi-me no [portal Microsoft Azure](https://portal.azure.com)e estou a ver o painel de instrumentos da casa do Azure. Então, onde encontro os meus dados de Insights de Aplicação?*

* Na barra de navegação à esquerda, clique em Insights de Aplicação e, em seguida, no nome da sua aplicação. Se não tiver nenhum projeto lá, precisa adicionar [ou configurar insights de aplicação no seu projeto web.](./asp-net.md)  
  Lá verá alguns gráficos de resumo. Pode clicar neles para ver mais detalhes.
* No Visual Studio, enquanto depura a sua aplicação, clique no botão Application Insights.

## <a name="no-server-data-or-no-data-at-all"></a><a name="q03"></a> Sem dados do servidor (ou nenhum dado)
*Corri a minha aplicação e abri o serviço application Insights no Microsoft Azure, mas todos os gráficos mostram 'Saiba como recolher...' ou "Não configurado."* Ou, *apenas, Visualização de página e dados do utilizador, mas sem dados do servidor.*

* Execute a sua aplicação em modo depuror no Visual Studio (F5). Utilize a aplicação de modo a gerar alguma telemetria. Verifique se pode ver os eventos registados na janela de saída do Estúdio Visual.  
  ![Screenshot que mostra executar a sua aplicação em modo de depuramento no Estúdio Visual.](./media/asp-net-troubleshoot-no-data/output-window.png)
* No portal Application Insights, abrir [a Pesquisa de Diagnóstico](./diagnostic-search.md). Os dados geralmente aparecem aqui primeiro.
* Clique no botão Refresh. A lâmina refresca-se periodicamente, mas também pode fazê-lo manualmente. O intervalo de atualização é maior para intervalos de tempo maiores.
* Verifique se as teclas de instrumentação coincidem. Na lâmina principal da sua aplicação no portal Application Insights, no **drop-down Essentials,** veja a **tecla instrumentação**. Em seguida, no seu projeto no Visual Studio, abra ApplicationInsights.config e encontre o `<instrumentationkey>` . Verifique se as duas chaves são iguais. Se não:  
  * No portal, clique em Application Insights e procure o recurso da aplicação com a chave certa; ou
  * No Visual Studio Solution Explorer, clique com o botão direito no projeto e escolha Application Insights, Configure. Reinicie a aplicação para enviar telemetria para o recurso certo.
  * Se não encontrar as teclas correspondentes, verifique se está a usar as mesmas credenciais de inscrição no Visual Studio do que no portal.
* No [painel de instrumentos caseiro microsoft Azure,](https://portal.azure.com)veja o mapa de Saúde do Serviço. Se houver algumas indicações de alerta, aguarde até que voltem a OK e, em seguida, feche e volte a abrir a sua lâmina de aplicação Application Insights.
* Consulte também o [nosso blog de estado.](https://techcommunity.microsoft.com/t5/azure-monitor-status/bg-p/AzureMonitorStatusBlog)
* Escreveu algum código para o [SDK do lado](./api-custom-events-metrics.md) do servidor que possa alterar a tecla de instrumentação em `TelemetryClient` casos ou `TelemetryContext` em? Ou escreveu um [filtro ou uma configuração de amostragem](./api-filtering-sampling.md) que pode estar a filtrar demasiado?
* Se editou ApplicationInsights.config, verifique cuidadosamente a configuração de [TelemetriaInitializers e TelemetriaProcessadores](./api-filtering-sampling.md). Um tipo ou parâmetro mal nomeado pode fazer com que o SDK não envie dados.

## <a name="no-data-on-page-views-browsers-usage"></a><a name="q04"></a>Sem dados sobre visualizações de página, navegadores, utilização
*Vejo dados nos gráficos de tempo de resposta do servidor e pedidos de servidor, mas nenhum dado no tempo de carga da visualização da página, ou nas lâminas de navegador ou de utilização.*

Os dados vêm de scripts nas páginas web. 

* Se adicionou Application Insights a um projeto web existente, [tem de adicionar os scripts à mão.](./javascript.md)
* Certifique-se de que o Internet Explorer não está a exibir o seu site no modo de compatibilidade.
* Utilize a funcionalidade de depurar do navegador (F12 em alguns navegadores e, em seguida, escolha Rede) para verificar se os dados estão a ser enviados para `dc.services.visualstudio.com` .

## <a name="no-dependency-or-exception-data"></a>Sem dados de dependência ou exceção
Consulte [a telemetria de dependência](./asp-net-dependencies.md) e a [telemetria de exceção](asp-net-exceptions.md).

## <a name="no-performance-data"></a>Sem dados de desempenho
Os dados de desempenho (CPU, taxa IO, e assim por diante) estão disponíveis para [serviços web java,](./java-collectd.md) [aplicações de desktop Windows,](./windows-desktop.md) [aplicações e serviços web IIS se instalar o monitor](./monitor-performance-live-website-now.md)de estado , e [Azure Cloud Services](./app-insights-overview.md). irá encontrá-lo em Definições, Servidores.

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>Nenhum (servidor) dados desde que publiquei a app para o meu servidor
* Verifique se copiou toda a Microsoft. ApplicationInsights DLLs para o servidor, juntamente com Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll
* Na sua firewall, poderá ter de [abrir algumas portas TCP.](./ip-addresses.md)
* Se tiver de usar um representante para enviar para fora da sua rede corporativa, desaprote [oProxy em](/previous-versions/dotnet/netframework-1.1/aa903360(v=vs.71)) Web.config
* Windows Server 2008: Certifique-se de que instalou as seguintes atualizações: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://web.archive.org/web/20150129090641/http://support.microsoft.com/kb/2600217).

## <a name="i-used-to-see-data-but-it-has-stopped"></a>Costumava ver dados, mas parou.
* Já atingiu a sua quota mensal de pontos de dados? Abra as Definições/Quota e Preços para descobrir. Em caso afirmativo, pode atualizar o seu plano ou pagar por uma capacidade adicional. Consulte o [regime de preços.](https://azure.microsoft.com/pricing/details/application-insights/)

## <a name="i-dont-see-all-the-data-im-expecting"></a>Não vejo todos os dados que espero.
Se a sua aplicação enviar muitos dados e estiver a utilizar o SDK Application Insights para ASP.NET versão 2.0.0-beta3 ou posterior, a [função de amostragem adaptativa](./sampling.md) pode funcionar e enviar apenas uma percentagem da sua telemetria.

Pode desativá-lo, mas isto não é recomendado. A amostragem foi concebida de modo a que a telemetria relacionada seja corretamente transmitida, para fins de diagnóstico.

## <a name="client-ip-address-is-0000"></a>O endereço IP do cliente é 0.0.0.0

No dia 5 de fevereiro de 2018, anunciamos que removemos a sessão de registo do endereço IP do Cliente. Isto não afeta a Localização Geo.

> [!NOTE]
> Se precisar dos primeiros 3 octetos do endereço IP, pode utilizar um [inicializador de telemetria](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) para adicionar um atributo personalizado.
> Isto não afeta os dados recolhidos antes de 5 de fevereiro de 2018.

## <a name="wrong-geographical-data-in-user-telemetry"></a>Dados geográficos errados na telemetria do utilizador
As dimensões da cidade, região e país são derivadas de endereços IP e nem sempre são precisas. Estes endereços IP são processados primeiro para a localização e depois alterados para 0.0.0.0 para serem armazenados.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Exceção “método não encontrado” ao executar nos Serviços Cloud do Azure
Compilou para .NET 4.6? O 4.6 não é suportado automaticamente nas funções dos Serviços Cloud do Azure. [Instale o 4.6 em cada função](../../cloud-services/cloud-services-dotnet-install-dotnet.md) antes de executar a aplicação.

## <a name="troubleshooting-logs"></a>Logs de resolução de problemas

Siga estas instruções para capturar registos de resolução de problemas para a sua estrutura.

### <a name="net-framework"></a>.NET Framework

1. Instale o pacote [Microsoft.AspNet.ApplicationInsights.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNet.ApplicationInsights.HostingStartup) a partir do NuGet. A versão que instala deve corresponder à versão atual instalada de `Microsoft.ApplicationInsighs`

2. Modifique o seu ficheiro applicationinsights.config para incluir o seguinte:

    ```xml
    <TelemetryModules>
      <Add Type="Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.FileDiagnosticsTelemetryModule, Microsoft.ApplicationInsights">
        <Severity>Verbose</Severity>
        <LogFileName>mylog.txt</LogFileName>
        <LogFilePath>C:\\SDKLOGS</LogFilePath>
      </Add>
    </TelemetryModules>
    ```
    A sua aplicação deve ter permissões de escrita para o local configurado

3. Reiniciar o processo para que estas novas definições sejam captadas pela SDK

4. Reverta estas mudanças quando terminar.

### <a name="net-core"></a>.NET Core

1. Instale o [pacote SDK NuGet do Application Insights para ASP.NET](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) pacote Core do NuGet. A versão que instalou deve coincidir com a versão atual instalada de `Microsoft.ApplicationInsights` .

   A versão mais recente do Microsoft.ApplicationInsights.AspNetCore é 2.14.0 e refere-se à versão 2.14.0 do Microsoft.ApplicationInsights. Daí que a versão do Microsoft.ApplicationInsights.AspNetCore a ser instalada deve ser 2.14.0.

2. Modifique `ConfigureServices` o método na sua `Startup.cs` classe.:

    ```csharp
    services.AddSingleton<ITelemetryModule, FileDiagnosticsTelemetryModule>();
    services.ConfigureTelemetryModule<FileDiagnosticsTelemetryModule>( (module, options) => {
        module.LogFilePath = "C:\\SDKLOGS";
        module.LogFileName = "mylog.txt";
        module.Severity = "Verbose";
    } );
    ```
    A sua aplicação deve ter permissões de escrita para o local configurado

3. Reiniciar o processo para que estas novas definições sejam captadas pela SDK

4. Reverta estas mudanças quando terminar.


## <a name="collect-logs-with-perfview"></a><a name="PerfView"></a> Recolher registos com PerfView
[PerfView](https://github.com/Microsoft/perfview) é uma ferramenta de diagnóstico e análise de desempenho gratuita que ajuda a isolar CPU, memória e outros problemas, recolhendo e visualizando informações de diagnóstico de muitas fontes.

Os registos SDK log SDK de registo de resolução de problemas de resolução de problemas que podem ser capturados pelo PerfView.

Para recolher registos, descarregue o PerfView e execute este comando:
```cmd
PerfView.exe collect -MaxCollectSec:300 -NoGui /onlyProviders=*Microsoft-ApplicationInsights-Core,*Microsoft-ApplicationInsights-Data,*Microsoft-ApplicationInsights-WindowsServer-TelemetryChannel,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Dependency,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Web,*Microsoft-ApplicationInsights-Extensibility-DependencyCollector,*Microsoft-ApplicationInsights-Extensibility-HostingStartup,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector,*Microsoft-ApplicationInsights-Extensibility-EventCounterCollector,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector-QuickPulse,*Microsoft-ApplicationInsights-Extensibility-Web,*Microsoft-ApplicationInsights-Extensibility-WindowsServer,*Microsoft-ApplicationInsights-WindowsServer-Core,*Microsoft-ApplicationInsights-LoggerProvider,*Microsoft-ApplicationInsights-Extensibility-EventSourceListener,*Microsoft-ApplicationInsights-AspNetCore
```

Pode modificar estes parâmetros conforme necessário:
- **MaxCollectSec**. Desacorra este parâmetro para evitar que o PerfView entre a funcionar indefinidamente e afete o desempenho do seu servidor.
- **Apenas Os Protetores.** Desabroque este parâmetro apenas para recolher registos do SDK. Pode personalizar esta lista com base nas suas investigações específicas. 
- **NoGui.** Desabrote este parâmetro para recolher registos sem o GUI.


Para mais informações,
- [Registo de traços de desempenho com PerfView](https://github.com/dotnet/roslyn/wiki/Recording-performance-traces-with-PerfView).
- [Fontes de evento de insights de aplicação](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/examples/ETW)

## <a name="collect-logs-with-dotnet-trace"></a>Recolher troncos com traços de dotnet

Em alternativa, os clientes também podem utilizar uma ferramenta cross-platform .NET Core, [`dotnet-trace`](/dotnet/core/diagnostics/dotnet-trace) para recolher registos que possam ajudar ainda mais na resolução de problemas. Isto pode ser particularmente útil para ambientes baseados em linux.

Após a instalação [`dotnet-trace`](/dotnet/core/diagnostics/dotnet-trace) de , execute o comando abaixo em bash.

```bash
dotnet-trace collect --process-id <PID> --providers Microsoft-ApplicationInsights-Core,Microsoft-ApplicationInsights-Data,Microsoft-ApplicationInsights-WindowsServer-TelemetryChannel,Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Dependency,Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Web,Microsoft-ApplicationInsights-Extensibility-DependencyCollector,Microsoft-ApplicationInsights-Extensibility-HostingStartup,Microsoft-ApplicationInsights-Extensibility-PerformanceCollector,Microsoft-ApplicationInsights-Extensibility-EventCounterCollector,Microsoft-ApplicationInsights-Extensibility-PerformanceCollector-QuickPulse,Microsoft-ApplicationInsights-Extensibility-Web,Microsoft-ApplicationInsights-Extensibility-WindowsServer,Microsoft-ApplicationInsights-WindowsServer-Core,Microsoft-ApplicationInsights-LoggerProvider,Microsoft-ApplicationInsights-Extensibility-EventSourceListener,Microsoft-ApplicationInsights-AspNetCore
```

## <a name="how-to-remove-application-insights"></a>Como remover insights de aplicações

Saiba como remover os Insights de Aplicação no Estúdio Visual seguindo os passos indicados no [artigo](./remove-application-insights.md)de remoção .

## <a name="still-not-working"></a>Ainda não estou a funcionar...
* [Microsoft Q&Uma página de perguntas para Insights de Aplicações](/answers/topics/azure-monitor.html)
