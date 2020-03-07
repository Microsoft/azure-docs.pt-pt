---
title: Azure Application Insights Snapshot Debugger para .NET apps
description: Depure instantâneos são automaticamente recolhidos quando exceções forem lançadas em aplicações de .NET de produção
ms.topic: conceptual
ms.date: 10/23/2019
ms.reviewer: cweining
ms.openlocfilehash: 18f43ba90157d71ec9488b6858fa9f41b2ee42a5
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78374974"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Instantâneos de depuração com exceções em aplicações .NET
Quando ocorre uma exceção, é possível recolher automaticamente um instantâneo de depuração da sua aplicação web em direto. O instantâneo mostra o estado do código-fonte e variáveis no momento que a exceção foi acionada. O Snapshot Debugger em [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) monitoriza a telemetria de exceção da sua aplicação web. Recolhe os instantâneos em suas exceções jogando em cima, de modo a que tem as informações necessárias diagnosticar problemas na produção. Inclua o [pacote NuGet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) do colecionador Snapshot na sua aplicação e configurar opcionalmente os parâmetros de recolha em [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). Os instantâneos aparecem em [exceções](../../azure-monitor/app/asp-net-exceptions.md) no portal Application Insights.

Pode ver instantâneos de depuração no portal para visualizar a pilha de chamadas e inspecionar as variáveis em cada frame de pilha de chamadas. Para obter uma experiência de depuração mais poderosa com código fonte, abra fotos com visual studio 2019 Enterprise. No Visual Studio, também pode [definir Snappoints para tirar fotos interativamente](https://aka.ms/snappoint) sem esperar por uma exceção.

As fotos de depuração são armazenadas durante 15 dias. Esta política de retenção está definida numa base por aplicação. Se precisar de aumentar este valor, pode pedir um aumento ao abrir um incidente de suporte no portal do Azure.

## <a name="enable-application-insights-snapshot-debugger-for-your-application"></a>Ativar Insights de aplicação Snapshot Debugger para a sua aplicação
Coleção de instantâneos está disponível para:
* Aplicações de .NET framework e o ASP.NET com o .NET Framework 4.5 ou posterior.
* Aplicações de .NET core 2.0 e ASP.NET Core 2.0 em execução no Windows.

São suportados os seguintes ambientes:

* [App Service do Azure](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) executando a família OS 4 ou mais tarde
* [Serviço Azure Serviço Fabric serviços](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) em execução no Windows Server 2012 R2 ou posterior
* [Máquinas virtuais azure e conjuntos](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) de escala de máquinas virtuais que executam o Windows Server 2012 R2 ou posterior
* [No local, máquinas virtuais ou físicas](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) que executam o Windows Server 2012 R2 ou posteriormente ou o Windows 8.1 ou posterior

> [!NOTE]
> Não são suportadas aplicações de cliente (por exemplo, WPF, formulários Windows ou UWP).

Se ativou o Snapshot Debugger mas não está a ver fotografias, consulte o nosso guia de [resolução de problemas.](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)

## <a name="grant-permissions"></a>Conceder permissões

Acesso a instantâneos está protegido pelo controlo de acesso baseado em funções (RBAC). Para inspecionar um instantâneo, deve primeiro ser adicionado à função necessária por um proprietário da subscrição.

> [!NOTE]
> Os proprietários e contribuintes não têm automaticamente esta função. Se pretende ver instantâneos, têm de adicionar-se para a função.

Os proprietários de subscrições devem atribuir a função `Application Insights Snapshot Debugger` aos utilizadores que inspecionem as imagens. Esta função pode ser atribuída a utilizadores individuais ou grupos por proprietários de subscrições para o destino do recurso do Application Insights ou o grupo de recursos ou subscrição.

1. Navegue para o recurso do Application Insights no portal do Azure.
1. Clique em **Controlo de acesso (IAM)** .
1. Clique no botão de atribuição de **funções +Adicionar.**
1. Selecione **Application Insights Snapshot Debugger** da lista de abandono de **papéis.**
1. Procure e introduza um nome para o utilizador adicione.
1. Clique no botão **Guardar** para adicionar o utilizador à função.


> [!IMPORTANT]
> Instantâneos potencialmente podem conter informações confidenciais pessoais e outras valores de variável e o parâmetro.

## <a name="view-snapshots-in-the-portal"></a>Ver snapshots no Portal

Depois de ter ocorrido uma exceção na sua aplicação e de ter sido criado um instantâneo, deverá ter imagens para visualizar. Pode levar de 5 a 10 minutos a partir de uma exceção que ocorre a um instantâneo pronto e visível a partir do portal. Para visualizar instantâneos, no painel **'Falha',** selecione o botão **Operações** ao visualizar o separador **Operações** ou selecione o botão **Exceções** ao visualizar o separador **Exceções:**

![Página de Falhas](./media/snapshot-debugger/failures-page.png)

Selecione uma operação ou exceção no painel certo para abrir o painel de detalhes de **transação** de ponta a ponta e, em seguida, selecione o evento de exceção. Se estiver disponível um instantâneo para a exceção dada, aparece um botão **Open Debug Snapshot** no painel direito com detalhes para [a exceção](../../azure-monitor/app/asp-net-exceptions.md).

![Botão de abrir instantâneo de depuração na exceção](./media/snapshot-debugger/e2e-transaction-page.png)

Na vista de instantâneo de depuração, verá uma pilha de chamadas e um painel de variáveis. Quando seleciona quadros de pilha de chamadas no painel de pilha de chamada, pode ver variáveis locais e parâmetros para essa função chamam-se no painel de variáveis.

![Ver instantâneo de depuração no portal](./media/snapshot-debugger/open-snapshot-portal.png)

Instantâneos podem incluir informações confidenciais e, por predefinição não estão visíveis. Para ver imagens, deve ter o papel `Application Insights Snapshot Debugger` que lhe foi atribuído.

## <a name="view-snapshots-in-visual-studio-2017-enterprise-or-above"></a>Ver Snapshots no Visual Studio 2017 Enterprise ou acima
1. Clique no botão **Download Snapshot** para descarregar um ficheiro `.diagsession`, que pode ser aberto pela Visual Studio Enterprise.

2. Para abrir o ficheiro `.diagsession`, é necessário instalar o componente Snapshot Debugger Visual Studio. O componente Snapshot Debugger é um componente necessário da carga de trabalho ASP.net no Estúdio Visual e pode ser selecionado a partir da lista de Componentes Individuais no instalador do Estúdio Visual. Se estiver a utilizar uma versão do Visual Studio antes do Visual Studio 2017 versão 15.5, terá de instalar a extensão do [Visual Studio Marketplace](https://aka.ms/snapshotdebugger).

3. Depois de abrir o ficheiro de instantâneo, é apresentada a página de Minidespejo depuração no Visual Studio. Clique no **Código Gerido de Debug** para começar a depurar o instantâneo. O instantâneo é aberta a linha de código em que a exceção foi acionada, para que pode depurar o estado atual do processo.

    ![Ver instantâneo de depuração no Visual Studio](./media/snapshot-debugger/open-snapshot-visualstudio.png)

O instantâneo transferido inclui os arquivos de símbolo que foram encontrados no seu servidor de aplicativos web. Esses arquivos de símbolo são necessárias para associar dados de instantâneos de código-fonte. Para aplicações de serviço de aplicações, certifique-se ativar a implementação de símbolo quando publicar as suas aplicações web.

## <a name="how-snapshots-work"></a>Como funcionam os instantâneos

O Snapshot Collector é implementado como processador de [telemetria Insights de Aplicação](../../azure-monitor/app/configuration-with-applicationinsights-config.md#telemetry-processors-aspnet)Insights . Quando executa a aplicação, o processador de telemetria do Recoletor de instantâneos é adicionado ao pipeline de telemetria do seu aplicativo.
Cada vez que a sua aplicação chama [TrackException,](../../azure-monitor/app/asp-net-exceptions.md#exceptions)o Snapshot Collector calcula um ID de problema do tipo de exceção que está a ser lançado e do método de lançamento.
Sempre que seu aplicativo chama TrackException, um contador é incrementado para o ID de problema apropriado. Quando o contador atinge o valor `ThresholdForSnapshotting`, o ID problema é adicionado a um Plano de Recolha.

O Snapshot Collector também monitoriza as exceções à medida que são lançadas ao subscrever o evento [AppDomain.CurrentDomain.FirstChanceException.](https://docs.microsoft.com/dotnet/api/system.appdomain.firstchanceexception) Quando esse evento é acionado, o ID do problema da exceção é calculado e comparado com os IDs de problema no plano de coleção.
Se existir uma correspondência, em seguida, é criado um instantâneo do processo em execução. O instantâneo é atribuído um identificador exclusivo e a exceção está marcada com esse identificador. Depois do manipulador de FirstChanceException retorna, a exceção lançada é processada como normal. Eventualmente, a exceção atinge novamente o método de TrackException onde, juntamente com o identificador de instantâneos, será reportado para o Application Insights.

O processo principal continua a executar e a servir o tráfego para os utilizadores com pouca interrupção. Enquanto isso, o instantâneo é transferido para o processo de carregador de instantâneo. O carregador de instantâneo cria um minidespejo e carrega-o para o Application Insights, juntamente com quaisquer arquivos de símbolo relevantes (. pdb).

> [!TIP]
> - Um instantâneo de processo é um clone suspenso do processo em execução.
> - Criar o instantâneo demora cerca de 10 a 20 milissegundos.
> - O valor padrão para `ThresholdForSnapshotting` é 1. Isso também é o valor mínimo. Por isso, a sua aplicação tem de desencadear a mesma exceção **duas vezes** antes de ser criado um instantâneo.
> - Detete `IsEnabledInDeveloperMode` verdadeiro se quiser gerar instantâneos enquanto depura no Estúdio Visual.
> - A taxa de criação instantânea é limitada pela definição `SnapshotsPerTenMinutesLimit`. Por predefinição, o limite é um instantâneo de cada dez minutos.
> - Podem ser carregados não mais de 50 instantâneos por dia.

## <a name="limitations"></a>Limitações

O período de retenção de dados por defeito é de 15 dias. Para cada instância de Insights de Aplicação, é permitido um número máximo de 50 instantâneos por dia.

### <a name="publish-symbols"></a>Publicar símbolos
O Snapshot Debugger requer arquivos de símbolo no servidor de produção para descodificar variáveis e fornecer uma experiência de depuração no Visual Studio.
Versão 15.2 (ou superior) do Visual Studio 2017 publica símbolos para as compilações de versão por predefinição quando publica no serviço de aplicações. Em versões anteriores, é necessário adicionar a seguinte linha ao seu perfil de publicação `.pubxml` ficheiro para que os símbolos sejam publicados no modo de lançamento:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Para a Azure Compute e outros tipos, certifique-se de que os ficheiros de símbolo estão na mesma pasta da aplicação principal .dll (normalmente, `wwwroot/bin`) ou estão disponíveis no caminho atual.

> [!NOTE]
> Para obter mais informações sobre as diferentes opções de símbolos disponíveis consulte a documentação do [Estúdio Visual.](https://docs.microsoft.com/visualstudio/ide/reference/advanced-build-settings-dialog-box-csharp?view=vs-2019#output
) Para obter os melhores resultados, recomendamos a utilização de "Full", "Portable" ou "Embedded".

### <a name="optimized-builds"></a>Compilações otimizadas
Em alguns casos, não não possível ver variáveis locais, nas compilações de versão, devido a otimizações que são aplicadas pelo compilador JIT.
No entanto, nos serviços de aplicações do Azure, o Recoletor de instantâneos pode deoptimize lançando métodos que fazem parte do seu plano de coleção.

> [!TIP]
> Instale a extensão de Site do Application Insights no seu serviço de aplicações para obter suporte deoptimization.

## <a name="next-steps"></a>Passos seguintes
Ativar Insights de aplicação Snapshot Debugger para a sua aplicação:

* [App Service do Azure](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Serviços em Nuvem do Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Serviço Azure Fabric](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas virtuais azure e conjuntos de escala de máquinas virtuais](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [No local máquinas virtuais ou físicas](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Além da aplicação Insights Snapshot Debugger:
 
* [Detete pontos de encaixe no seu código](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications) para obter instantâneos sem esperar por uma exceção.
* [Diagnosticar exceções nas suas aplicações web](../../azure-monitor/app/asp-net-exceptions.md) explica como tornar mais exceções visíveis aos Insights de Aplicação.
* [A Deteção Inteligente](../../azure-monitor/app/proactive-diagnostics.md) descobre automaticamente anomalias de desempenho.
