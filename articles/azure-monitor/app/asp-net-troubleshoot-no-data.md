---
title: Resolução de problemas sem dados - Application Insights para .NET
description: Não estás a ver dados em Insights de Aplicação Azure? Tente aqui.
ms.topic: conceptual
ms.date: 07/23/2018
ms.openlocfilehash: 2627fde55f4177798d04aab02db169f3117d32dd
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77665906"
---
# <a name="troubleshooting-no-data---application-insights-for-netnet-core"></a>Resolução de problemas sem dados - Informações de aplicação para .NET/.NET Core

## <a name="some-of-my-telemetry-is-missing"></a>Falta alguma da minha telemetria.
*Em Application Insights, só vejo uma fração dos eventos que estão a ser gerados pela minha app.*

* Se está constantemente a ver a mesma fração, provavelmente deve-se a [uma amostragem](../../azure-monitor/app/sampling.md)adaptável. Para confirmar isto, abra a Pesquisa (a partir da lâmina de visão geral) e veja uma instância de um Pedido ou outro evento. Na parte inferior da secção de propriedades clique em "..." para obter detalhes completos da propriedade. Se request Count > 1, então a amostragem está em funcionamento.
* Caso contrário, é possível que esteja a atingir um limite de taxa de [dados](../../azure-monitor/app/pricing.md#limits-summary) para o seu plano de preços. Estes limites são aplicados por minuto.

*Estou a sofrer perdas de dados aleatoriamente.*

* Verifique se está a sofrer perda de dados no [Canal Telemettry](telemetry-channels.md#does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost)

* Verifique quaisquer problemas conhecidos no Telemettry Channel [GitHub repo](https://github.com/Microsoft/ApplicationInsights-dotnet/issues)

*Estou a sentir perda de dados na App consola ou na Web App quando a aplicação está prestes a parar.*

* O canal SDK mantém a telemetria no tampão e envia-as em lotes. Se o pedido estiver desligado, poderá ter de ligar explicitamente para [flush()](api-custom-events-metrics.md#flushing-data). O comportamento do `Flush()` depende do [canal](telemetry-channels.md#built-in-telemetry-channels) usado.

## <a name="no-data-from-my-server"></a>Nenhum dado do meu servidor
*Instalei a minha aplicação no meu servidor web, e agora não vejo nenhuma telemetria. Funcionou bem na minha máquina de dev.*

* Provavelmente um problema de firewall. [Definir exceções de firewall para que os Insights de Aplicação enviem dados](../../azure-monitor/app/ip-addresses.md).
* O IIS Server pode estar a faltar alguns pré-requisitos: .NET Extensibility 4.5 e ASP.NET 4.5.

*[Instalei](../../azure-monitor/app/monitor-performance-live-website-now.md) o Status Monitor no meu servidor web para monitorizar as aplicações existentes. Não vejo resultados.*

* Consulte o Monitor de [Estado de Resolução de Problemas](../../azure-monitor/app/monitor-performance-live-website-now.md#troubleshoot).

## <a name="q01"></a>Sem opção 'Adicionar Insights de Aplicação' no Estúdio Visual
*Quando clico num projeto existente no Solution Explorer, não vejo opções de Insights de Aplicação.*

* Nem todos os tipos de projeto .NET são suportados pelas ferramentas. Os projetos Web e WCF são apoiados. Para outros tipos de projeto, como desktop ou aplicações de serviço, ainda pode [adicionar manualmente um SDK](../../azure-monitor/app/windows-desktop.md)de Insights de Aplicação ao seu projeto .
* Certifique-se de que tem [o Visual Studio 2013 Update 3 ou mais tarde](https://docs.microsoft.com/visualstudio/releasenotes/vs2013-update3-rtm-vs). Vem pré-instalado com ferramentas Developer Analytics, que fornecem o SDK de Insights de Aplicação.
* Selecione **Ferramentas,** **Extensões e Atualizações** e verifique se as **Ferramentas de Análise** do Desenvolvedor estão instaladas e ativadas. Em caso afirmativo, clique em **Atualizações** para ver se existe uma atualização disponível.
* Abra o diálogo do Novo Projeto e escolha ASP.NET aplicação Web. Se vir a opção Insights de Aplicação, as ferramentas são instaladas. Caso contrário, tente desinstalar e, em seguida, reinstalar as ferramentas de análise do desenvolvedor.

## <a name="q02"></a>Adicionar insights de aplicação falhou
*Quando tento adicionar insights de aplicação a um projeto existente, vejo uma mensagem de erro.*

Causas prováveis:

* A comunicação com o portal Deinsights de Aplicações falhou; ou
* Há algum problema com a sua conta Azure;
* Só [leu acesso à subscrição ou grupo onde estava a tentar criar o novo recurso.](../../azure-monitor/app/resources-roles-access-control.md)

Correção:

* Verifique se forneceu credenciais de entrada para a conta Azure certa.
* No seu navegador, verifique se tem acesso ao [portal Azure.](https://portal.azure.com) Abra as Definições e veja se há alguma restrição.
* [Adicione insights de aplicação ao seu projeto existente](../../azure-monitor/app/asp-net.md): No Solution Explorer, clique no seu projeto e escolha "Adicionar Insights de Aplicação".

## <a name="emptykey"></a>Tenho um erro "A chave de instrumentação não pode estar vazia"
Parece que algo correu mal enquanto instalavas a Aplicação Insights ou talvez um adaptador de registo.

No Solution Explorer, clique no seu projeto e escolha **Insights de Aplicação > Configure Insights**de aplicação . Receberá um diálogo que o convida a iniciar sessão no Azure e a criar um recurso Application Insights, ou a reutilizar um existente.

## <a name="NuGetBuild"></a>"Pacotes NuGet(s) estão desaparecidos" no meu servidor de construção
*Tudo se constrói bem quando estou a depurar a minha máquina de desenvolvimento, mas tenho um erro nuGet no servidor de construção.*

Por favor, consulte [a restauração](https://docs.nuget.org/Consume/Package-Restore) do pacote NuGet e a [restauração automática do pacote.](https://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore)

## <a name="missing-menu-command-to-open-application-insights-from-visual-studio"></a>Falta de comando de menu para abrir Insights de Aplicação do Estúdio Visual
*Quando clico no meu projeto Solution Explorer, não vejo nenhum comando de Insights de Aplicação, ou não vejo um comando de Insights de Aplicação Aberta.*

Causas prováveis:

* Se criou o recurso Application Insights manualmente, ou se o projeto é de um tipo que não é suportado pelas ferramentas Application Insights.
* As ferramentas Developer Analytics são desativadas no seu Estúdio Visual.
* O seu Estúdio Visual é mais antigo do que o Update 3 de 2013.

Correção:

* Certifique-se de que a sua versão Visual Studio é a atualização 3 ou posterior de 2013.
* Selecione **Ferramentas,** **Extensões e Atualizações** e verifique se as **ferramentas Developer Analytics** estão instaladas e ativadas. Em caso afirmativo, clique em **Atualizações** para ver se existe uma atualização disponível.
* Clique no seu projeto no Solution Explorer. Se vir o comando **Application Insights > Configure Application Insights,** use-o para ligar o seu projeto ao recurso no serviço Deinsights de Aplicação.

Caso contrário, o seu tipo de projeto não é suportado diretamente pelas ferramentas Developer Analytics. Para ver a sua telemetria, inscreva-se no [portal Azure,](https://portal.azure.com)escolha Insights de Aplicação na barra de navegação esquerda e selecione a sua aplicação.

## <a name="access-denied-on-opening-application-insights-from-visual-studio"></a>'Acesso negado' na abertura de Insights de Aplicação do Estúdio Visual
*O comando do menu 'Open Application Insights' leva-me ao portal Azure, mas tenho um erro de acesso negado.*

O registo da Microsoft que usou pela última vez no seu navegador predefinido não tem acesso ao recurso que foi criado quando o [Application Insights foi adicionado a esta aplicação](../../azure-monitor/app/asp-net.md). Há duas razões prováveis:

* Tem mais do que uma conta microsoft - talvez uma conta pessoal da Microsoft? O registo que utilizou pela última vez no seu navegador predefinido foi para uma conta diferente daquela que tem acesso para adicionar Insights de [Aplicação ao projeto](../../azure-monitor/app/asp-net.md).
  * Correção: Clique no seu nome na parte superior direita da janela do navegador e assine. Em seguida, assine com a conta que tem acesso. Em seguida, na barra de navegação esquerda, clique em Insights de Aplicação e selecione a sua aplicação.
* Outra pessoa adicionou a Application Insights ao projeto, e esqueceram-se de lhe dar [acesso ao grupo](../../azure-monitor/app/resources-roles-access-control.md) de recursos em que foi criado.
  * Correção: Se utilizarem uma conta organizacional, podem adicioná-lo à equipa; ou podem conceder-lhe acesso individual ao grupo de recursos.

## <a name="asset-not-found-on-opening-application-insights-from-visual-studio"></a>'Ativo não encontrado' na abertura de Insights de Aplicação do Estúdio Visual
*O comando do menu 'Open Application Insights' leva-me ao portal Azure, mas tenho um erro de "ativo não encontrado".*

Causas prováveis:

* O recurso Insights de Aplicação para a sua aplicação foi eliminado; ou
* A chave de instrumentação foi definida ou alterada em ApplicationInsights.config editando-a diretamente, sem atualizar o ficheiro do projeto.

A chave de instrumentação em ApplicationInsights.config controla para onde a telemetria é enviada. Uma linha no ficheiro do projeto controla qual recurso é aberto quando utiliza o comando no Estúdio Visual.

Correção:

* No Solution Explorer, clique no projeto e escolha Insights de Aplicação, Configure Insights de Aplicação. No diálogo, pode optar por enviar telemetria para um recurso existente, ou criar um novo. Ou:
* Abra o recurso diretamente. Inscreva-se [no portal Azure,](https://portal.azure.com)clique em Insights de Aplicação na barra de navegação esquerda e, em seguida, selecione a sua aplicação.

## <a name="where-do-i-find-my-telemetry"></a>Onde encontro a minha telemetria?
*Inscrevi-me no portal do [Microsoft Azure](https://portal.azure.com)e estou a olhar para o painel de instrumentos do Azure. Então, onde encontro os meus dados de Informação de Aplicação?*

* Na barra de navegação à esquerda, clique em Insights de Aplicação, em seguida, o nome da sua aplicação. Se não tiver quaisquer projetos lá, precisa adicionar ou configurar Insights de [Aplicação no seu projeto web](../../azure-monitor/app/asp-net.md).  
  Aí verá alguns gráficos de resumo. Pode clicar neles para ver mais detalhes.
* No Estúdio Visual, enquanto está a depurar a sua aplicação, clique no botão Insights de Aplicação.

## <a name="q03"></a>Sem dados do servidor (ou sem dados)
*Corri a minha aplicação e abri o serviço Application Insights no Microsoft Azure, mas todos os gráficos mostram "Saiba colecionar..." ou 'Não configurado'.* Ou, *apenas o Page View e os dados do utilizador, mas sem dados do servidor.*

* Execute a sua aplicação em modo dedepura no Estúdio Visual (F5). Use a aplicação de modo a gerar alguma telemetria. Verifique se pode ver eventos registados na janela de saída do Estúdio Visual.  
  ![](./media/asp-net-troubleshoot-no-data/output-window.png)
* No portal Informações de Aplicação, abra [a Pesquisa de Diagnóstico.](../../azure-monitor/app/diagnostic-search.md) Os dados geralmente aparecem aqui primeiro.
* Clique no botão Refresh. A lâmina refresca-se periodicamente, mas também pode fazê-lo manualmente. O intervalo de atualização é mais longo para intervalos de tempo maiores.
* Verifique a correspondência das teclas de instrumentação. Na lâmina principal da sua aplicação no portal Application Insights, no **"Drop-down" do Essentials,** veja a **chave de instrumentação**. Em seguida, no seu projeto no Estúdio Visual, abra ApplicationInsights.config e encontre o `<instrumentationkey>`. Verifique se as duas chaves são iguais. Se não:  
  * No portal, clique em Insights de Aplicação e procure o recurso da aplicação com a chave certa; ou
  * No Visual Studio Solution Explorer, clique no projeto e escolha Insights de Aplicação, Configure. Redefinir a aplicação para enviar telemetria para o recurso certo.
  * Se não encontrar as chaves correspondentes, verifique se está a usar as mesmas credenciais de entrada no Estúdio Visual como no portal.
* No painel de [instrumentos domésticos do Microsoft Azure,](https://portal.azure.com)veja o mapa de Saúde de Serviço. Se houver algumas indicações de alerta, aguarde até que voltem ao OK e, em seguida, feche e reabra a sua lâmina de aplicação Application Insights.
* Verifique também [o nosso blog de status](https://blogs.msdn.microsoft.com/servicemap-status/).
* Escreveu algum código para o [SDK](../../azure-monitor/app/api-custom-events-metrics.md) do lado do servidor que pudesse alterar a chave de instrumentação em `TelemetryClient` casos ou em `TelemetryContext`? Ou escreveu um [filtro ou uma configuração de amostragem](../../azure-monitor/app/api-filtering-sampling.md) que pode estar a filtrar demasiado?
* Se editou ApplicationInsights.config, verifique cuidadosamente a configuração de [TelemettryInitializers e TelemettryProcessors](../../azure-monitor/app/api-filtering-sampling.md). Um tipo ou parâmetro de nome incorreto pode fazer com que o SDK não envie dados.

## <a name="q04"></a>Não há dados sobre visualizações de página, navegadores, utilização
*Vejo dados nos gráficos de Tempo de Resposta ao Servidor e pedidos de servidor, mas nenhum dado no tempo de carregamento do Page View, ou nas lâminas de navegador ou de utilização.*

Os dados provêm de scripts nas páginas web. 

* Se adicionou insights de aplicação a um projeto web existente, [tem de adicionar os scripts à mão](../../azure-monitor/app/javascript.md).
* Certifique-se de que o Internet Explorer não está a exibir o seu site no modo de compatibilidade.
* Utilize a funcionalidade de depuração do navegador (F12 em alguns navegadores, depois escolha a Rede) para verificar se os dados estão a ser enviados para `dc.services.visualstudio.com`.

## <a name="no-dependency-or-exception-data"></a>Sem dados de dependência ou exceção
Ver [telemetria de dependência](../../azure-monitor/app/asp-net-dependencies.md) e [telemetria de exceção.](asp-net-exceptions.md)

## <a name="no-performance-data"></a>Sem dados de desempenho
Os dados de desempenho (CPU, taxa IO, e assim por diante) estão disponíveis para [serviços web java,](../../azure-monitor/app/java-collectd.md) [aplicações de desktop windows,](../../azure-monitor/app/windows-desktop.md) [aplicações e serviços web IIS se instalar o monitor](../../azure-monitor/app/monitor-performance-live-website-now.md)de estado e os [Serviços Azure Cloud.](../../azure-monitor/app/app-insights-overview.md) vai encontrá-lo em Definições, Servidores.

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>Não há dados (servidor) desde que publiquei a aplicação no meu servidor
* Verifique se copiou toda a Microsoft. ApplicationInsights DLLs para o servidor, juntamente com Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll
* Na sua firewall, talvez tenha de [abrir algumas portas TCP.](../../azure-monitor/app/ip-addresses.md)
* Se tiver de usar um proxy para enviar da sua rede corporativa, detete o [proxy predefinido](https://msdn.microsoft.com/library/aa903360.aspx) em Web.config
* Windows Server 2008: Certifique-se de que instalou as seguintes atualizações: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://support.microsoft.com/kb/2600217).

## <a name="i-used-to-see-data-but-it-has-stopped"></a>Costumava ver dados, mas parou.
* Verifique o blog de [estado](https://blogs.msdn.com/b/applicationinsights-status/).
* Atingiu a sua quota mensal de pontos de dados? Abra as Definições/Quota e Preços para saber. Em caso afirmativo, pode atualizar o seu plano ou pagar por uma capacidade adicional. Consulte o [regime de preços](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="i-dont-see-all-the-data-im-expecting"></a>Não vejo todos os dados que estou à espera.
Se a sua aplicação enviar muitos dados e estiver a utilizar o SDK de Insights de Aplicação para ASP.NET versão 2.0.0-beta3 ou posterior, a função de [amostragem adaptativa](../../azure-monitor/app/sampling.md) pode funcionar e enviar apenas uma percentagem da sua telemetria.

Pode desativá-lo, mas isto não é recomendado. A amostragem foi concebida de modo a que a telemetria relacionada seja corretamente transmitida, para fins de diagnóstico.

## <a name="client-ip-address-is-0000"></a>O endereço IP do cliente é 0.0.0.0

No dia 5 de fevereiro de 2018, anunciamos que removemos o registo do endereço IP do Cliente. Isto não afeta a Geo Localização.

> [!NOTE]
> Se precisar dos primeiros 3 octetos do endereço IP, pode utilizar um inicializador de [telemetria](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#addmodify-properties-itelemetryinitializer) para adicionar um atributo personalizado.
> Isto não afeta os dados recolhidos antes de 5 de fevereiro de 2018.

## <a name="wrong-geographical-data-in-user-telemetry"></a>Dados geográficos errados na telemetria do utilizador
As dimensões da cidade, região e país são derivadas de endereços IP e nem sempre são precisas. Estes endereços IP são processados para localização primeiro e depois alterados para 0.0.0.0 para serem armazenados.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Exceção “método não encontrado” ao executar nos Serviços Cloud do Azure
Compilou para .NET 4.6? O 4.6 não é suportado automaticamente nas funções dos Serviços Cloud do Azure. [Instale o 4.6 em cada função](../../cloud-services/cloud-services-dotnet-install-dotnet.md) antes de executar a aplicação.

## <a name="troubleshooting-logs"></a>Registos de resolução de problemas

Siga estas instruções para capturar registos de resolução de problemas para a sua estrutura.

### <a name="net-framework"></a>.NET Framework

1. Instale o pacote [Microsoft.AspNet.ApplicationInsights.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNet.ApplicationInsights.HostingStartup) a partir do NuGet. A versão que instalar deve coincidir com a versão instalada atual de `Microsoft.ApplicationInsighs`

2. Modifique o ficheiro applicationinsights.config para incluir o seguinte:

    ```xml
    <TelemetryModules>
      <Add Type="Microsoft.ApplicationInsights.Extensibility.HostingStartup.FileDiagnosticsTelemetryModule, Microsoft.AspNet.ApplicationInsights.HostingStartup">
        <Severity>Verbose</Severity>
        <LogFileName>mylog.txt</LogFileName>
        <LogFilePath>C:\\SDKLOGS</LogFilePath>
      </Add>
    </TelemetryModules>
    ```
    A sua aplicação deve ter permissões de escrita para o local configurado

3. Reiniciar o processo para que estas novas definições sejam captadas pelo SDK

4. Reverta estas alterações quando terminar.

### <a name="net-core"></a>.NET Core

1. Instale o pacote [Microsoft.AspNet.ApplicationInsights.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNet.ApplicationInsights.HostingStartup) a partir do NuGet. A versão que instalar deve coincidir com a versão instalada atual de `Microsoft.ApplicationInsights`

A versão mais recente do Microsoft.ApplicationInsights.AspNetCore é de 2.8.2, e refere-se à versão 2.11.2 do Microsoft.ApplicationInsights. Daí que a versão do Microsoft.AspNet.ApplicationInsights.HostingStartup a instalar deve ser 2.11.2

2. Modifique `ConfigureServices` método na sua aula de `Startup.cs`.:

    ```csharp
    services.AddSingleton<ITelemetryModule, FileDiagnosticsTelemetryModule>();
    services.ConfigureTelemetryModule<FileDiagnosticsTelemetryModule>( (module, options) => {
        module.LogFilePath = "C:\\SDKLOGS";
        module.LogFileName = "mylog.txt";
        module.Severity = "Verbose";
    } );
    ```
    A sua aplicação deve ter permissões de escrita para o local configurado

3. Reiniciar o processo para que estas novas definições sejam captadas pelo SDK

4. Reverta estas alterações quando terminar.


## <a name="PerfView"></a>Colete registos com PerfView
[O PerfView](https://github.com/Microsoft/perfview) é uma ferramenta gratuita de diagnóstico e análise de desempenho que ajuda a isolar cpU, memória e outros problemas, recolhendo e visualizando informações de diagnóstico de muitas fontes.

Os registos de auto-resolução de problemas do SDK De Insights de Aplicação SDK que podem ser capturados pelo PerfView.

Para recolher registos, baixe o PerfView e execute este comando:
```cmd
PerfView.exe collect -MaxCollectSec:300 -NoGui /onlyProviders=*Microsoft-ApplicationInsights-Core,*Microsoft-ApplicationInsights-Data,*Microsoft-ApplicationInsights-WindowsServer-TelemetryChannel,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Dependency,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Web,*Microsoft-ApplicationInsights-Extensibility-DependencyCollector,*Microsoft-ApplicationInsights-Extensibility-HostingStartup,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector,*Microsoft-ApplicationInsights-Extensibility-EventCounterCollector,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector-QuickPulse,*Microsoft-ApplicationInsights-Extensibility-Web,*Microsoft-ApplicationInsights-Extensibility-WindowsServer,*Microsoft-ApplicationInsights-WindowsServer-Core,*Microsoft-ApplicationInsights-Extensibility-EventSourceListener,*Microsoft-ApplicationInsights-AspNetCore
```

Pode modificar estes parâmetros conforme necessário:
- **MaxCollectSec**. Defina este parâmetro para evitar que o PerfView funcionasse indefinidamente e afetasse o desempenho do seu servidor.
- **Apenas Fornecedores**. Defina este parâmetro para recolher apenas registos do SDK. Pode personalizar esta lista com base nas suas investigações específicas. 
- **Nogui.** Defina este parâmetro para recolher registos sem o Gui.


Para mais informações,
- [Gravando vestígios de desempenho com PerfView](https://github.com/dotnet/roslyn/wiki/Recording-performance-traces-with-PerfView).
- [Fontes de evento sinuosas de aplicação](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/ETW)

## <a name="still-not-working"></a>Ainda não estou a trabalhar...
* [Fórum de Insights de Aplicação](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)
