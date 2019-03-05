---
title: Resolução de problemas sem dados - Application Insights para .NET
description: Não são apresentados dados no Azure Application Insights? Tente aqui.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: e231569f-1b38-48f8-a744-6329f41d91d3
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/23/2018
ms.author: mbullwin
ms.openlocfilehash: 9b94445d3053f036510baf7945c1086558d28188
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57341685"
---
# <a name="troubleshooting-no-data---application-insights-for-net"></a>Resolução de problemas sem dados - Application Insights para .NET
## <a name="some-of-my-telemetry-is-missing"></a>Alguns dos meus telemetria está em falta
*No Application Insights, vejo apenas uma fração dos eventos que estão a ser gerados pelo meu aplicativo.*

* Se vir consistentemente a fração do mesmo, é provavelmente devido a adaptável [amostragem](../../azure-monitor/app/sampling.md). Para confirmar isso, abra a pesquisa (a partir do painel de descrição geral) e observe uma instância de um pedido ou outro evento. Na parte inferior da secção de propriedades clique "..." para obter detalhes de propriedade completa. Se pedir contagem > 1, em seguida, a amostragem é numa operação.
* Caso contrário, é possível que estiver a obter um [limite de taxa de dados](../../azure-monitor/app/pricing.md#limits-summary) para o plano de preços. Estes limites são aplicados por minuto.

## <a name="no-data-from-my-server"></a>Não existem dados a partir do meu servidor
*Eu Instalei a minha aplicação no meu servidor web e, agora eu não vejo qualquer telemetria do mesmo. Ele trabalhou OK no computador de desenvolvimento.*

* Provavelmente é um problema de firewall. [Definir exceções de firewall para o Application Insights enviar dados](../../azure-monitor/app/ip-addresses.md).
* Servidor IIS alguns pré-requisitos poderá estar em falta: .NET Extensibility 4.5 e o ASP.NET 4.5.

*Eu [instalado o Monitor de estado](../../azure-monitor/app/monitor-performance-live-website-now.md) no meu servidor de web para monitorizar aplicações existentes. Não ver quaisquer resultados.*

* Ver [Monitor de estado de resolução de problemas](../../azure-monitor/app/monitor-performance-live-website-now.md#troubleshoot).

## <a name="q01"></a>Nenhuma opção de "A adicionar o Application Insights" no Visual Studio
*Quando eu com o botão direito um projeto existente no Explorador de soluções, não vejo as opções do Application Insights.*

* Nem todos os tipos de projeto do .NET são suportados pelas ferramentas. Projetos da Web e WCF são suportados. Para outros tipos de projeto, como aplicativos de área de trabalho ou serviços específico, ainda pode [adicionar manualmente um SDK do Application Insights ao seu projeto](../../azure-monitor/app/windows-desktop.md).
* Certifique-se de que tem [Visual Studio 2013 atualização 3 ou posterior](https://docs.microsoft.com/visualstudio/releasenotes/vs2013-update3-rtm-vs). Ele vem pré-instalada com ferramentas de análise de programador, que fornecem o Application Insights SDK.
* Selecione **ferramentas**, **extensões e atualizações** e verifique se **Developer Analytics Tools** está instalado e ativado. Se assim for, clique em **atualizações** para ver se existe uma atualização disponível.
* Abra a caixa de diálogo novo projeto e escolha a aplicação ASP.NET Web. Se vir a opção Application Insights, em seguida, as ferramentas são instaladas. Caso contrário, tente desinstalar e reinstalar o Application Insights Tools.

## <a name="q02"></a>Falha ao adicionar o Application Insights
*Ao tentar ao adicionar o Application Insights para um projeto existente, vejo uma mensagem de erro.*

Causas prováveis:

* Falha na comunicação com o portal do Application Insights; ou
* Existe algum problema com a sua conta do Azure;
* Tem apenas [acesso de leitura para a subscrição ou grupo em que estava a tentar criar o novo recurso](../../azure-monitor/app/resources-roles-access-control.md).

CORREÇÃO:

* Verifique que forneceu as credenciais de início de sessão para a conta do Azure certo.
* No seu browser, verifique se tem acesso para o [portal do Azure](https://portal.azure.com). Abra as definições e verificar se existe qualquer restrição.
* [Adicionar o Application Insights ao seu projeto existente](../../azure-monitor/app/asp-net.md): No Solution Explorer, clique com o botão direito do rato em seu projeto e escolha "Adicionar Application Insights."
* Se ainda não está funcionando, siga os [procedimento manual](../../azure-monitor/app/windows-services.md) para adicionar um recurso no portal e, em seguida, adicione o SDK ao seu projeto.

## <a name="emptykey"></a>Recebo uma mensagem de erro "chave de instrumentação não pode estar vazio"
Parece que ocorreu um erro enquanto foram a instalar o Application Insights ou talvez um adaptador de registo.

No Solution Explorer, clique no projeto e escolha **Application Insights > configurar o Application Insights**. Obterá uma caixa de diálogo que convida que inicie sessão no Azure e cria a um recurso do Application Insights, ou reutilize um já existente.

## <a name="NuGetBuild"></a> "Os pacotes de NuGet estão em falta" no meu servidor de compilação
*Tudo o que cria OK, quando eu estou depuração em minha máquina de desenvolvimento, mas recebo uma mensagem de erro do NuGet no servidor de compilação.*

Veja [restauro do pacote NuGet](https://docs.nuget.org/Consume/Package-Restore) e [restauro do pacote automática](https://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore).

## <a name="missing-menu-command-to-open-application-insights-from-visual-studio"></a>Comando de menu em falta para abrir o Application Insights a partir do Visual Studio
*Quando eu com o botão direito meu projeto do Solution Explorer, não vejo qualquer comando do Application Insights ou não vejo um comando abrir Application Insights.*

Causas prováveis:

* Se tiver criado o recurso do Application Insights manualmente, ou se o projeto é um tipo que não é suportado pelas ferramentas do Application Insights.
* As ferramentas de análise de programador estão desativadas no Visual Studio.
* No Visual Studio é anterior ao 2013 atualização 3.

CORREÇÃO:

* Certifique-se a versão do Visual Studio 2013 atualização 3 ou posterior.
* Selecione **ferramentas**, **extensões e atualizações** e verifique se **ferramentas de análise de programador** está instalado e ativado. Se assim for, clique em **atualizações** para ver se existe uma atualização disponível.
* Com o botão direito do rato no Explorador de soluções. Se vir o comando **Application Insights > configurar o Application Insights**, utilizá-lo para ligar o seu projeto para o recurso no serviço do Application Insights.

Caso contrário, seu tipo de projeto não é suportado diretamente pelas ferramentas do Application Insights. Para ver a telemetria, inicie sessão para o [portal do Azure](https://portal.azure.com), escolha Application Insights na barra de navegação à esquerda e selecione a aplicação.

## <a name="access-denied-on-opening-application-insights-from-visual-studio"></a>"Acesso negado" sobre como abrir o Application Insights a partir do Visual Studio
*O comando de menu "Abrir o Application Insights" me leva ao portal do Azure, mas recebo um erro "acesso negado".*

O Microsoft início de sessão que foi utilizado pela última vez no seu browser predefinido não tem acesso ao [o recurso que foi criado quando o Application Insights foi adicionado a esta aplicação](../../azure-monitor/app/asp-net.md). Existem dois motivos provavelmente:

* Tem mais do que uma conta Microsoft - talvez um trabalho e uma conta Microsoft pessoal? O início de sessão que foi utilizado pela última vez no seu browser predefinido foi para uma conta diferente daquela que tem acesso ao [adicionar o Application Insights ao projeto](../../azure-monitor/app/asp-net.md).
  * CORREÇÃO: Clique no seu nome no canto superior direito da janela do navegador e terminar sessão. Em seguida, inicie sessão com a conta que tem acesso. Em seguida, na barra de navegação esquerdo, clique em Application Insights e selecione a sua aplicação.
* Alguém adicionou o Application Insights ao projeto, e eles se esqueceu de dar-lhe [acesso ao grupo de recursos](../../azure-monitor/app/resources-roles-access-control.md) no qual ele foi criado.
  * CORREÇÃO: Se usaram uma conta institucional, eles podem adicioná-lo para a equipe; ou eles podem conceder-lhe acesso individual para o grupo de recursos.

## <a name="asset-not-found-on-opening-application-insights-from-visual-studio"></a>"Ativo não encontrado" sobre como abrir o Application Insights a partir do Visual Studio
*O comando de menu "Abrir o Application Insights" me leva ao portal do Azure, mas recebo um erro "o recurso não encontrado".*

Causas prováveis:

* O recurso do Application Insights para a sua aplicação tiver sido eliminado; ou
* A chave de instrumentação foi definir ou alterada no applicationinsights. config por editá-lo diretamente, sem atualizar o ficheiro de projeto.

A chave de instrumentação em controles de applicationinsights. config, onde a telemetria é enviada. Uma linha no arquivo de projeto controla qual o recurso é aberto quando usar o comando no Visual Studio.

CORREÇÃO:

* No Solution Explorer, clique com o botão direito no projeto e escolha Application Insights, configurar o Application Insights. Na caixa de diálogo, pode optar por enviar telemetria para um recurso existente ou criar um novo. Ou:
* Abra o recurso diretamente. Inicie sessão no [do portal do Azure](https://portal.azure.com), clique em Application Insights na barra de navegação esquerdo e, em seguida, selecione a sua aplicação.

## <a name="where-do-i-find-my-telemetry"></a>Onde posso encontrar minha telemetria?
*Sessão iniciada para o [portal do Microsoft Azure](https://portal.azure.com), e vou analisar o dashboard principal do Azure. Então, onde posso encontrar os meus dados do Application Insights?*

* Na barra de navegação esquerda, clique em Application Insights, em seguida, o nome da sua aplicação. Se não tiver todos os projetos lá, terá [adicione ou configure o Application Insights no seu projeto da web](../../azure-monitor/app/asp-net.md).  
  Aqui verá alguns gráficos de resumo. Pode clicar sucessivamente-los para ver mais detalhes.
* No Visual Studio, enquanto estiver a depurar a aplicação, clique no botão Application Insights.

## <a name="q03"></a> Não existem dados de servidor (ou não existem dados em todos)
*Executei meu aplicativo e, em seguida, abrir o serviço Application Insights no Microsoft Azure, mas todos os gráficos mostram "Saiba como recolher..." ou "Não configurado."* Ou, *apenas dados de utilizador e vista de página, mas não existem dados de servidor.*

* Execute a sua aplicação no modo de depuração no Visual Studio (F5). Utilize a aplicação de modo a gerar alguma telemetria. Verifique se pode ver os eventos registados na janela de saída do Visual Studio.  
  ![](./media/asp-net-troubleshoot-no-data/output-window.png)
* No portal do Application Insights, abra [pesquisa de diagnóstico](../../azure-monitor/app/diagnostic-search.md). Dados, normalmente, são apresentados aqui primeiro.
* Clique no botão de atualização. O painel é atualizada em si periodicamente, mas pode também fazê-lo manualmente. O intervalo de atualização é mais longo para intervalos de tempo maior.
* Verifique as chaves de instrumentação correspondem. No painel principal para a sua aplicação no portal do Application Insights, no **Essentials** menu pendente, examinar **chave de instrumentação**. Em seguida, no seu projeto no Visual Studio, abra applicationinsights. config e localize o `<instrumentationkey>`. Verifique que as duas chaves forem iguais. Se não for:  
  * No portal, clique em Application Insights e procure o recurso de aplicação com a chave correta; ou
  * No Explorador de soluções do Visual Studio, clique com o botão direito no projeto e escolha Application Insights, configurar. Reponha a aplicação para enviar telemetria para o recurso indicado.
  * Se não conseguir encontrar as chaves correspondentes, verifique que está a utilizar as mesmas credenciais de início de sessão no Visual Studio como no portal.
* Na [dashboard inicial do Microsoft Azure](https://portal.azure.com), ver o mapa de estado de funcionamento do serviço. Se existirem algumas indicações de alerta, aguarde até que eles tem devolvido para OK e, em seguida, feche e volte a abrir o painel de aplicações do Application Insights.
* Verifique também [nosso blogue de estado](https://blogs.msdn.microsoft.com/servicemap-status/).
* Fez escrever qualquer código para o [SDK do lado do servidor](../../azure-monitor/app/api-custom-events-metrics.md) que podem ser alteradas a chave de instrumentação na `TelemetryClient` instâncias ou na `TelemetryContext`? Ou fez escrever um [configuração de filtro ou amostragem](../../azure-monitor/app/api-filtering-sampling.md) que poderá ser filtragem horizontalmente muito?
* Se editou o applicationinsights. config, verifique com cuidado a configuração do [TelemetryInitializers e TelemetryProcessors](../../azure-monitor/app/api-filtering-sampling.md). Um tipo chamado incorretamente ou um parâmetro pode fazer com que o SDK para enviar dados não.

## <a name="q04"></a>Não existem dados sobre a utilização de vistas de página, navegadores,
*Posso ver dados nos gráficos de tempo de resposta do servidor e pedidos de servidor, mas não dados em tempo de carregamento da vista de página ou nos painéis Browser ou na utilização.*

Os dados provêm de scripts em páginas da web. 

* Se tiver adicionado o Application Insights para um projeto web existente, [é necessário adicionar os scripts manualmente](../../azure-monitor/app/javascript.md).
* Certifique-se de que o Internet Explorer não é apresentada a seu site no modo de compatibilidade.
* Utilizar a funcionalidade de depuração do navegador (F12 em alguns navegadores, em seguida, escolha rede) para verificar que os dados estão a ser enviados para `dc.services.visualstudio.com`.

## <a name="no-dependency-or-exception-data"></a>Não existem dados de dependência ou a exceção
Ver [telemetria de dependência](../../azure-monitor/app/asp-net-dependencies.md) e [telemetria de exceção](asp-net-exceptions.md).

## <a name="no-performance-data"></a>Não existem dados de desempenho
Dados de desempenho (CPU, a taxa de e/s e assim por diante) está disponível para [serviços da web de Java](../../azure-monitor/app/java-collectd.md), [aplicativos de desktop do Windows](../../azure-monitor/app/windows-desktop.md), [IIS aplicações web e serviços se instalar o monitor de estado](../../azure-monitor/app/monitor-performance-live-website-now.md), e [Serviços Cloud do azure](../../azure-monitor/app/app-insights-overview.md). descobrirá que é em definições, servidores.

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>Não existem dados (servidor), uma vez que eu publicado a aplicação ao meu servidor
* Verifique que copiou na verdade, todos os da Microsoft. DLLs do Application Insights para o servidor, juntamente com Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll
* Na firewall, terá de [abrir algumas portas TCP](../../azure-monitor/app/ip-addresses.md).
* Se tiver de utilizar um proxy para enviar sua rede empresarial, defina [defaultProxy](https://msdn.microsoft.com/library/aa903360.aspx) em Web. config
* Windows Server 2008: Certifique-se de que instalou as seguintes atualizações: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://support.microsoft.com/kb/2600217).

## <a name="i-used-to-see-data-but-it-has-stopped"></a>Usei para ver os dados, mas foi parado
* Verifique os [blog de estado](https://blogs.msdn.com/b/applicationinsights-status/).
* Ter atingido a quota mensal de pontos de dados? Abra o preço e definições/Quota para descobrir. Nesse caso, pode atualizar o seu plano ou pagar por capacidade adicional. Consulte a [esquema de preços](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="i-dont-see-all-the-data-im-expecting"></a>Não vejo todos os dados que eu estou esperando
Se a sua aplicação envia uma grande quantidade de dados e estiver a utilizar o Application Insights SDK para ASP.NET versão 2.0.0-Beta3 ou posterior, o [amostragem adaptável](../../azure-monitor/app/sampling.md) funcionalidade pode operar e enviar apenas uma percentagem da sua telemetria.

Pode desativá-lo, mas isso não é recomendável. Amostragem foi concebida para que a telemetria relacionada corretamente é transmitida, para fins de diagnóstico.

## <a name="client-ip-address-is-0000"></a>Endereço IP do cliente for 0.0.0.0

5 de Fevereiro 2018, anunciámos que Removemos o registo do endereço IP de cliente. Isso não afeta a localização geográfica.

> [!NOTE]
> Se precisar dos primeiros 3 octetos do endereço IP, pode utilizar um [inicializador de telemetria](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#add-properties-itelemetryinitializer) para adicionar um atributo personalizado.
> Isto não afeta os dados recolhidos antes de 5 de fevereiro de 2018.

## <a name="wrong-geographical-data-in-user-telemetry"></a>Dados geográficos errados na telemetria do utilizador
A cidade, região e dimensões de país são derivadas de endereços IP e nem sempre são precisas. Estes endereços IP são processados pela primeira vez para a localização e, em seguida, alterados para 0.0.0.0 sejam armazenados.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Exceção “método não encontrado” ao executar nos Serviços Cloud do Azure
Compilou para .NET 4.6? O 4.6 não é suportado automaticamente nas funções dos Serviços Cloud do Azure. [Instale o 4.6 em cada função](../../cloud-services/cloud-services-dotnet-install-dotnet.md) antes de executar a aplicação.

## <a name="troubleshooting-logs"></a>Registos de resolução de problemas

Siga estas instruções para capturar os registos de resolução de problemas para sua estrutura.

### <a name="net-framework"></a>.NET Framework

1. Instalar o [Microsoft.AspNet.ApplicationInsights.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNet.ApplicationInsights.HostingStartup) pacote do NuGet. A versão instalada tem de corresponder à versão atual instalada do `Microsoft.ApplicationInsighs`

2. Modifique o ficheiro applicationinsights. config para incluir o seguinte:

    ```xml
    <TelemetryModules>
      <Add Type="Microsoft.ApplicationInsights.Extensibility.HostingStartup.FileDiagnosticsTelemetryModule, Microsoft.AspNet.ApplicationInsights.HostingStartup">
        <Severity>Verbose</Severity>
        <LogFileName>mylog.txt</LogFileName>
        <LogFilePath>C:\\SDKLOGS</LogFilePath>
      </Add>
    </TelemetryModules>
    ```
    A aplicação tem de ter permissões de escrita para a localização configurada

3. Reinicie o processo para que essas novas configurações são captadas pelo SDK

4. Quando tiver terminado, reverta essas alterações.

### <a name="net-core"></a>.Net Core

1. Instalar o [Microsoft.AspNetCore.ApplicationInsights.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.ApplicationInsights.HostingStartup) pacote do NuGet. A versão instalada tem de corresponder à versão atual instalada do `Microsoft.ApplicationInsights`

2. Modificar `ConfigureServices` método na sua `Startup.cs` classe.:

    ```csharp
    services.AddSingleton<ITelemetryModule, FileDiagnosticsTelemetryModule>();
    services.ConfigureTelemetryModule<FileDiagnosticsTelemetryModule>( (module, options) => {
        module.LogFilePath = "C:\\SDKLOGS";
        module.LogFileName = "mylog.txt";
        module.Severity = "Verbose";
    } );
    ```
    A aplicação tem de ter permissões de escrita para a localização configurada

3. Reinicie o processo para que essas novas configurações são captadas pelo SDK

4. Quando tiver terminado, reverta essas alterações.

## <a name="still-not-working"></a>Ainda não está a funcionar...
* [Fórum do Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)
