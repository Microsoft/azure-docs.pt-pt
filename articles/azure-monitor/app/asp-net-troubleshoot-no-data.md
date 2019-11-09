---
title: Resolução de problemas sem dados - Application Insights para .NET
description: Não está vendo dados no Aplicativo Azure insights? Tente aqui.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/23/2018
ms.openlocfilehash: 857188ebb5ddc3c24f6a225819c47fc1643417e6
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73887521"
---
# <a name="troubleshooting-no-data---application-insights-for-net"></a>Resolução de problemas sem dados - Application Insights para .NET
## <a name="some-of-my-telemetry-is-missing"></a>Parte da minha telemetria está ausente
*Em Application Insights, vejo apenas uma fração dos eventos que estão sendo gerados pelo meu aplicativo.*

* Se você estiver vendo consistentemente a mesma fração, provavelmente é devido à [amostragem](../../azure-monitor/app/sampling.md)adaptável. Para confirmar isso, abra a pesquisa (na folha visão geral) e examine uma instância de uma solicitação ou outro evento. Na parte inferior da seção de propriedades, clique em "..." para obter detalhes completos da propriedade. Se a contagem de solicitações > 1, a amostragem estará em operação.
* Caso contrário, é possível que você esteja atingindo um [limite de taxa de dados](../../azure-monitor/app/pricing.md#limits-summary) para seu plano de preços. Esses limites são aplicados por minuto.

*Estou tendo perda de dados aleatoriamente.*

* Verifique se você está passando por perda de dados no [canal de telemetria](telemetry-channels.md#does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost)

* Verifique se há problemas conhecidos no [repositório GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet/issues) do canal de telemetria

*Estou tendo perda de dados no aplicativo de console ou no aplicativo Web quando o aplicativo está prestes a parar.*

* O canal do SDK mantém a telemetria no buffer e os envia em lotes. Se o aplicativo estiver sendo desligado, talvez seja necessário chamar explicitamente [flush ()](api-custom-events-metrics.md#flushing-data). O comportamento de `Flush()` depende do [canal](telemetry-channels.md#built-in-telemetry-channels) real usado.

## <a name="no-data-from-my-server"></a>Não há dados do meu servidor
*Eu instalei meu aplicativo no meu servidor Web e agora não vejo nenhuma telemetria dele. Funcionou OK no meu computador de desenvolvimento.*

* Provavelmente um problema de firewall. [Defina exceções de firewall para Application insights enviar dados](../../azure-monitor/app/ip-addresses.md).
* O servidor IIS pode estar sem alguns pré-requisitos: extensibilidade .NET 4,5 e ASP.NET 4,5.

*Eu [instalei status monitor](../../azure-monitor/app/monitor-performance-live-website-now.md) no meu servidor Web para monitorar os aplicativos existentes. Não vejo nenhum resultado.*

* Consulte [solução de problemas status monitor](../../azure-monitor/app/monitor-performance-live-website-now.md#troubleshoot).

## <a name="q01"></a>Nenhuma opção ' Add Application Insights ' no Visual Studio
*Quando clico com o botão direito do mouse em um projeto existente no Gerenciador de Soluções, não vejo nenhuma Application Insights opções.*

* Nem todos os tipos de projeto .NET têm suporte nas ferramentas. Há suporte para projetos Web e WCF. Para outros tipos de projeto, como aplicativos de área de trabalho ou de serviço, você ainda pode [Adicionar um SDK Application insights ao seu projeto manualmente](../../azure-monitor/app/windows-desktop.md).
* Verifique se você tem [Visual Studio 2013 atualização 3 ou posterior](https://docs.microsoft.com/visualstudio/releasenotes/vs2013-update3-rtm-vs). Ele vem pré-instalado com as ferramentas de análise do desenvolvedor, que fornecem o SDK do Application Insights.
* Selecione **ferramentas**, **extensões e atualizações** e verifique se **Developer Analytics Tools** está instalado e habilitado. Nesse caso, clique em **atualizações** para ver se há uma atualização disponível.
* Abra a caixa de diálogo novo projeto e escolha ASP.NET aplicativo Web. Se você vir a opção Application Insights lá, as ferramentas serão instaladas. Caso contrário, tente desinstalar e instalar novamente o Developer Analytics Tools.

## <a name="q02"></a>Falha ao adicionar Application Insights
*Quando tento adicionar Application Insights a um projeto existente, vejo uma mensagem de erro.*

Causas prováveis:

* Falha na comunicação com o portal de Application Insights; or
* Há algum problema com sua conta do Azure;
* Você só tem [acesso de leitura à assinatura ou ao grupo em que estava tentando criar o novo recurso](../../azure-monitor/app/resources-roles-access-control.md).

Soluciona

* Verifique se você forneceu credenciais de entrada para a conta correta do Azure.
* No navegador, verifique se você tem acesso ao [portal do Azure](https://portal.azure.com). Abra configurações e veja se há alguma restrição.
* [Adicione Application insights ao seu projeto existente](../../azure-monitor/app/asp-net.md): no Gerenciador de soluções, clique com o botão direito do mouse no projeto e escolha "Adicionar Application insights".

## <a name="emptykey"></a>Recebo um erro "a chave de instrumentação não pode estar vazia"
Parece que algo deu errado enquanto você estava instalando Application Insights ou talvez um adaptador de registro em log.

Em Gerenciador de Soluções, clique com o botão direito do mouse em seu projeto e escolha **Application Insights > configurar Application insights**. Você receberá uma caixa de diálogo que convida você a entrar no Azure e a criar um recurso Application Insights ou a reutilizar um existente.

## <a name="NuGetBuild"></a>"Pacotes NuGet estão ausentes" no meu servidor de compilação
*Tudo é criado OK quando estou Depurando no meu computador de desenvolvimento, mas obtenho um erro do NuGet no servidor de compilação.*

Consulte [restauração de pacote NuGet](https://docs.nuget.org/Consume/Package-Restore) e [restauração automática de pacote](https://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore).

## <a name="missing-menu-command-to-open-application-insights-from-visual-studio"></a>Comando de menu ausente para abrir Application Insights do Visual Studio
*Quando eu clico com o botão direito do mouse no meu projeto Gerenciador de Soluções, não vejo nenhum comando Application Insights ou não vejo um Open Application Insights Command.*

Causas prováveis:

* Se você criou o recurso de Application Insights manualmente ou se o projeto é de um tipo que não tem suporte das ferramentas de Application Insights.
* As ferramentas de análise do desenvolvedor estão desabilitadas no Visual Studio.
* O Visual Studio é mais antigo que 2013 atualização 3.

Soluciona

* Verifique se sua versão do Visual Studio é 2013 atualização 3 ou posterior.
* Selecione **ferramentas**, **extensões e atualizações** e verifique se as **Ferramentas do desenvolvedor Analytics** estão instaladas e habilitadas. Nesse caso, clique em **atualizações** para ver se há uma atualização disponível.
* Clique com o botão direito do mouse em seu projeto no Gerenciador de Soluções. Se você vir o comando **Application Insights > configurar Application insights**, use-o para conectar seu projeto ao recurso no serviço Application insights.

Caso contrário, o tipo de projeto não tem suporte direto das ferramentas de análise do desenvolvedor. Para ver sua telemetria, entre no [portal do Azure](https://portal.azure.com), escolha Application insights na barra de navegação à esquerda e selecione seu aplicativo.

## <a name="access-denied-on-opening-application-insights-from-visual-studio"></a>' Acesso negado ' ao abrir Application Insights do Visual Studio
*O comando de menu ' Open Application Insights ' me leva para a portal do Azure, mas recebo um erro de ' acesso negado '.*

A entrada da Microsoft que você usou pela última vez no navegador padrão não tem acesso ao [recurso que foi criado quando Application insights foi adicionado a esse aplicativo](../../azure-monitor/app/asp-net.md). Há dois motivos prováveis:

* Você tem mais de um conta Microsoft-talvez um trabalho e um conta Microsoft pessoal? A entrada que você usou pela última vez no navegador padrão era para uma conta diferente daquela que tem acesso para [adicionar Application insights ao projeto](../../azure-monitor/app/asp-net.md).
  * Correção: clique no seu nome na parte superior direita da janela do navegador e saia. Em seguida, entre com a conta que tem acesso. Em seguida, na barra de navegação à esquerda, clique em Application Insights e selecione seu aplicativo.
* Outra pessoa adicionou Application Insights ao projeto e se esqueceu de conceder [acesso ao grupo de recursos](../../azure-monitor/app/resources-roles-access-control.md) no qual ele foi criado.
  * Correção: se eles usaram uma conta institucional, eles podem adicioná-lo à equipe; ou podem conceder a você acesso individual ao grupo de recursos.

## <a name="asset-not-found-on-opening-application-insights-from-visual-studio"></a>' Ativo não encontrado ' ao abrir Application Insights do Visual Studio
*O comando de menu ' Open Application Insights ' me leva para a portal do Azure, mas recebo um erro de ' ativo não encontrado '.*

Causas prováveis:

* O recurso de Application Insights para seu aplicativo foi excluído; or
* A chave de instrumentação foi definida ou alterada em ApplicationInsights. config editando-a diretamente, sem Atualizar o arquivo de projeto.

A chave de instrumentação em ApplicationInsights. config controla onde a telemetria é enviada. Uma linha no arquivo de projeto controla qual recurso é aberto quando você usa o comando no Visual Studio.

Soluciona

* Em Gerenciador de Soluções, clique com o botão direito do mouse no projeto e escolha Application Insights, configure Application Insights. Na caixa de diálogo, você pode optar por enviar telemetria a um recurso existente ou criar um novo. Or
* Abra o recurso diretamente. Entre no [portal do Azure](https://portal.azure.com), clique em Application insights na barra de navegação à esquerda e, em seguida, selecione seu aplicativo.

## <a name="where-do-i-find-my-telemetry"></a>Onde encontro minha telemetria?
*Entrei na [portal do Microsoft Azure](https://portal.azure.com)e estou olhando para o painel de início do Azure. Onde encontro meus dados de Application Insights?*

* Na barra de navegação à esquerda, clique em Application Insights, em seguida, o nome do aplicativo. Se você não tiver nenhum projeto, precisará [Adicionar ou configurar Application insights em seu projeto Web](../../azure-monitor/app/asp-net.md).  
  Lá, você verá alguns gráficos de resumo. Você pode clicar neles para ver mais detalhes.
* No Visual Studio, enquanto você estiver Depurando seu aplicativo, clique no botão Application Insights.

## <a name="q03"></a>Nenhum dado de servidor (ou nenhum dado)
*Executei meu aplicativo e abri o serviço Application Insights no Microsoft Azure, mas todos os gráficos mostram ' saiba como coletar... ' ou ' não configurado '.* Ou *somente exibição de página e dados de usuário, mas sem dados de servidor.*

* Execute seu aplicativo no modo de depuração no Visual Studio (F5). Use o aplicativo para gerar alguma telemetria. Verifique se você pode ver os eventos registrados na janela de saída do Visual Studio.  
  ![](./media/asp-net-troubleshoot-no-data/output-window.png)
* No portal de Application Insights, abra a [pesquisa de diagnóstico](../../azure-monitor/app/diagnostic-search.md). Normalmente, os dados aparecem aqui primeiro.
* Clique no botão atualizar. A folha se atualiza periodicamente, mas você também pode fazer isso manualmente. O intervalo de atualização é maior para intervalos de tempo maiores.
* Verifique se as chaves de instrumentação correspondem. Na folha principal do seu aplicativo no portal de Application Insights, na lista suspensa **Essentials** , examine a **chave de instrumentação**. Em seguida, em seu projeto no Visual Studio, abra ApplicationInsights. config e localize o `<instrumentationkey>`. Verifique se as duas chaves são iguais. Caso contrário:  
  * No portal, clique em Application Insights e procure o recurso de aplicativo com a chave correta; or
  * No Visual Studio Gerenciador de Soluções, clique com o botão direito do mouse no projeto e escolha Application Insights, configurar. Redefina o aplicativo para enviar telemetria para o recurso correto.
  * Se você não encontrar as chaves correspondentes, verifique se está usando as mesmas credenciais de entrada no Visual Studio como no Portal.
* No [painel Microsoft Azure página inicial](https://portal.azure.com), examine o mapa de integridade do serviço. Se houver algumas indicações de alerta, aguarde até que elas sejam retornadas para OK e, em seguida, feche e abra novamente sua folha de aplicativo Application Insights.
* Verifique também [nosso blog de status](https://blogs.msdn.microsoft.com/servicemap-status/).
* Você escreveu algum código para o [SDK do lado do servidor](../../azure-monitor/app/api-custom-events-metrics.md) que pode alterar a chave de instrumentação em `TelemetryClient` instâncias ou em `TelemetryContext`? Ou você escreveu uma [configuração de filtro ou de amostragem](../../azure-monitor/app/api-filtering-sampling.md) que poderia estar filtrando muito?
* Se você editou ApplicationInsights. config, verifique cuidadosamente a configuração de [TelemetryInitializers e TelemetryProcessors](../../azure-monitor/app/api-filtering-sampling.md). Um tipo ou parâmetro nomeado incorretamente pode fazer com que o SDK não envie dados.

## <a name="q04"></a>Não há dados em exibições de página, navegadores, uso
*Vejo dados no tempo de resposta do servidor e nos gráficos de solicitações do servidor, mas nenhum dado no tempo de carregamento da exibição de página ou nas folhas de uso ou no navegador.*

Os dados são provenientes de scripts nas páginas da Web. 

* Se você adicionou Application Insights a um projeto Web existente, [precisará adicionar os scripts manualmente](../../azure-monitor/app/javascript.md).
* Certifique-se de que o Internet Explorer não está exibindo seu site no modo de compatibilidade.
* Use o recurso de depuração do navegador (F12 em alguns navegadores e, em seguida, escolha rede) para verificar se os dados estão sendo enviados para `dc.services.visualstudio.com`.

## <a name="no-dependency-or-exception-data"></a>Sem dados de dependência ou de exceção
Consulte [telemetria de dependência](../../azure-monitor/app/asp-net-dependencies.md) e [telemetria de exceção](asp-net-exceptions.md).

## <a name="no-performance-data"></a>Nenhum dado de desempenho
Os dados de desempenho (CPU, taxa de e/s e assim por diante) estão disponíveis para [Serviços Web Java](../../azure-monitor/app/java-collectd.md), [aplicativos de área de trabalho do Windows](../../azure-monitor/app/windows-desktop.md), [aplicativos Web do IIS e serviços se você instalar o status monitor](../../azure-monitor/app/monitor-performance-live-website-now.md)e os [serviços de nuvem do Azure](../../azure-monitor/app/app-insights-overview.md). Você o encontrará em configurações, servidores.

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>Nenhum (servidor) dados desde que publiquei o aplicativo no meu servidor
* Verifique se você realmente copiou todos os Microsoft. ApplicationInsights DLLs para o servidor, junto com Microsoft. Diagnostics. Instrumentation. Extensions. Intercept. dll
* Em seu firewall, talvez seja necessário [abrir algumas portas TCP](../../azure-monitor/app/ip-addresses.md).
* Se você precisar usar um proxy para enviar de sua rede corporativa, defina [defaultProxy](https://msdn.microsoft.com/library/aa903360.aspx) em Web. config
* Windows Server 2008: Verifique se você instalou as seguintes atualizações: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://support.microsoft.com/kb/2600217).

## <a name="i-used-to-see-data-but-it-has-stopped"></a>Eu costumava ver os dados, mas ele foi interrompido
* Verifique o [blog de status](https://blogs.msdn.com/b/applicationinsights-status/).
* Você atingiu sua cota mensal de pontos de dados? Abra as configurações/cota e os preços para descobrir. Nesse caso, você pode atualizar seu plano ou pagar por capacidade adicional. Consulte o [esquema de preços](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="i-dont-see-all-the-data-im-expecting"></a>Não vejo todos os dados que estou esperando
Se seu aplicativo enviar muitos dados e você estiver usando o SDK do Application Insights para a versão ASP.NET 2.0.0-Beta3 ou posterior, o recurso de [amostragem adaptável](../../azure-monitor/app/sampling.md) poderá operar e enviar apenas um percentual de sua telemetria.

Você pode desabilitá-lo, mas isso não é recomendado. A amostragem é projetada para que a telemetria relacionada seja transmitida corretamente, para fins de diagnóstico.

## <a name="client-ip-address-is-0000"></a>O endereço IP do cliente é 0.0.0.0

Em fevereiro de 5 2018, anunciamos que removemos o registro em log do endereço IP do cliente. Isso não afeta o local geográfico.

> [!NOTE]
> Se você precisar dos três primeiros octetos do endereço IP, poderá usar um [inicializador de telemetria](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#addmodify-properties-itelemetryinitializer) para adicionar um atributo personalizado.
> Isto não afeta os dados recolhidos antes de 5 de fevereiro de 2018.

## <a name="wrong-geographical-data-in-user-telemetry"></a>Dados geográficos errados na telemetria do usuário
As dimensões de cidade, região e país são derivadas de endereços IP e nem sempre são precisas. Esses endereços IP são processados primeiro para localização e, em seguida, alterados para 0.0.0.0 a serem armazenados.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Exceção “método não encontrado” ao executar nos Serviços Cloud do Azure
Compilou para .NET 4.6? O 4.6 não é suportado automaticamente nas funções dos Serviços Cloud do Azure. [Instale o 4.6 em cada função](../../cloud-services/cloud-services-dotnet-install-dotnet.md) antes de executar a aplicação.

## <a name="troubleshooting-logs"></a>Solucionando problemas de logs

Siga estas instruções para capturar os logs de solução de problemas para sua estrutura.

### <a name="net-framework"></a>.NET Framework

1. Instale o pacote [Microsoft. AspNet. ApplicationInsights. HostingStartup](https://www.nuget.org/packages/Microsoft.AspNet.ApplicationInsights.HostingStartup) do NuGet. A versão que você instalar deve corresponder à versão instalada atual do `Microsoft.ApplicationInsighs`

2. Modifique o arquivo applicationinsights. config para incluir o seguinte:

    ```xml
    <TelemetryModules>
      <Add Type="Microsoft.ApplicationInsights.Extensibility.HostingStartup.FileDiagnosticsTelemetryModule, Microsoft.AspNet.ApplicationInsights.HostingStartup">
        <Severity>Verbose</Severity>
        <LogFileName>mylog.txt</LogFileName>
        <LogFilePath>C:\\SDKLOGS</LogFilePath>
      </Add>
    </TelemetryModules>
    ```
    Seu aplicativo deve ter permissões de gravação para o local configurado

3. Reinicie o processo para que essas novas configurações sejam selecionadas pelo SDK

4. Reverta essas alterações quando terminar.

### <a name="net-core"></a>.NET Core

1. Instale o pacote [Microsoft. AspNet. ApplicationInsights. HostingStartup](https://www.nuget.org/packages/Microsoft.AspNet.ApplicationInsights.HostingStartup) do NuGet. A versão que você instalar deve corresponder à versão instalada atual do `Microsoft.ApplicationInsights`

A versão mais recente de Microsoft. ApplicationInsights. AspNetCore é 2.7.1 e se refere à Microsoft. ApplicationInsights versão 2,10. Portanto, a versão de Microsoft. AspNet. ApplicationInsights. HostingStartup a ser instalada deve ser 2.10.0

2. Modifique `ConfigureServices` método em sua classe `Startup.cs`.:

    ```csharp
    services.AddSingleton<ITelemetryModule, FileDiagnosticsTelemetryModule>();
    services.ConfigureTelemetryModule<FileDiagnosticsTelemetryModule>( (module, options) => {
        module.LogFilePath = "C:\\SDKLOGS";
        module.LogFileName = "mylog.txt";
        module.Severity = "Verbose";
    } );
    ```
    Seu aplicativo deve ter permissões de gravação para o local configurado

3. Reinicie o processo para que essas novas configurações sejam selecionadas pelo SDK

4. Reverta essas alterações quando terminar.


## <a name="PerfView"></a>Coletar logs com PerfView
O [Perfview](https://github.com/Microsoft/perfview) é uma ferramenta gratuita de análise de desempenho e diagnóstico que ajuda a isolar a CPU, a memória e outros problemas ao coletar e visualizar informações de diagnóstico de várias fontes.

Os logs de autosoluçãoização de log EventSource do Application Insights SDK que podem ser capturados pelo PerfView.

Para coletar logs, baixe PerfView e execute este comando:
```cmd
PerfView.exe collect -MaxCollectSec:300 -NoGui /onlyProviders=*Microsoft-ApplicationInsights-Core,*Microsoft-ApplicationInsights-Data,*Microsoft-ApplicationInsights-WindowsServer-TelemetryChannel,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Dependency,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Web,*Microsoft-ApplicationInsights-Extensibility-DependencyCollector,*Microsoft-ApplicationInsights-Extensibility-HostingStartup,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector,*Microsoft-ApplicationInsights-Extensibility-EventCounterCollector,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector-QuickPulse,*Microsoft-ApplicationInsights-Extensibility-Web,*Microsoft-ApplicationInsights-Extensibility-WindowsServer,*Microsoft-ApplicationInsights-WindowsServer-Core,*Microsoft-ApplicationInsights-Extensibility-EventSourceListener,*Microsoft-ApplicationInsights-AspNetCore
```

Você pode modificar esses parâmetros conforme necessário:
- **MaxCollectSec**. Defina esse parâmetro para impedir que o PerfView seja executado indefinidamente e afete o desempenho do servidor.
- **OnlyProviders**. Defina esse parâmetro para coletar somente os logs do SDK. Você pode personalizar essa lista com base em suas investigações específicas. 
- **NoGui**. Defina esse parâmetro para coletar logs sem a GUI.


Para obter mais informações,
- [Gravando rastreamentos de desempenho com o Perfview](https://github.com/dotnet/roslyn/wiki/Recording-performance-traces-with-PerfView).
- [Fontes de eventos de Application Insights](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/ETW)

## <a name="still-not-working"></a>Ainda não está funcionando...
* [Fórum de Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)
