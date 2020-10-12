---
title: Azure Application Insights Snapshot Debugger para aplicações .NET
description: Os instantâneos de depurg são automaticamente recolhidos quando as exceções são lançadas na produção de aplicações .NET
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 10/23/2019
ms.reviewer: cweining
ms.openlocfilehash: ab142b4e0a2d5486727ffc71fc94ae4944513052
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88935811"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Instantâneos de depuração com exceções em aplicações .NET
Quando ocorre uma exceção, pode recolher automaticamente uma imagem de depurar da sua aplicação web ao vivo. O instantâneo mostra o estado do código fonte e as variáveis no momento em que a exceção foi lançada. O Snapshot Debugger in [Azure Application Insights](./app-insights-overview.md) monitoriza a telemetria de exceção da sua aplicação web. Recolhe instantâneos nas suas exceções de arremesso de topo para que tenha a informação necessária para diagnosticar problemas na produção. Inclua o [pacote NuGet do colecionador Snapshot](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) na sua aplicação e configurar opcionalmente os parâmetros de recolha em [ApplicationInsights.config](./configuration-with-applicationinsights-config.md). As imagens aparecem em [exceções](./asp-net-exceptions.md) no portal Application Insights.

Pode ver instantâneos de depuração no portal para visualizar a pilha de chamadas e inspecionar as variáveis em cada frame de pilha de chamadas. Para obter uma experiência de depuragem mais poderosa com código fonte, abra fotos com o Visual Studio 2019 Enterprise. No Visual Studio, também pode [definir Snappoints para tirar fotos interativamente](https://aka.ms/snappoint) sem esperar por uma exceção.

As fotos de depurg são armazenadas durante 15 dias. Esta política de retenção é definida numa base por aplicação. Se precisar de aumentar este valor, pode solicitar um aumento abrindo um caso de suporte no portal Azure.

## <a name="enable-application-insights-snapshot-debugger-for-your-application"></a>Ativar o Debugger Snapshot de Insights de Aplicação para a sua aplicação
A coleção Snapshot está disponível para:
* .NET Framework e ASP.NET aplicações em execução .NET Framework 4.5 ou posterior.
* .NET Core 2.0 e ASP.NET aplicações Core 2.0 em execução no Windows.

São apoiados os seguintes ambientes:

* [Serviço de Aplicações do Azure](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) com a família OS 4 ou mais tarde
* [Serviços de tecido de serviço Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) em execução no Windows Server 2012 R2 ou mais tarde
* [Máquinas virtuais Azure e conjuntos de escala de máquinas virtuais](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) em execução Windows Server 2012 R2 ou mais tarde
* [Máquinas virtuais ou físicas no local](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) que executam o Windows Server 2012 R2 ou mais tarde ou Windows 8.1 ou posterior

> [!NOTE]
> As aplicações do cliente (por exemplo, WPF, Windows Forms ou UWP) não são suportadas.

Se ativou o Snapshot Debugger mas não está a ver instantâneos, consulte o nosso [guia de resolução de problemas.](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)

## <a name="grant-permissions"></a>Conceder permissões

O acesso a instantâneos está protegido pelo controlo de acesso baseado em funções (RBAC). Para analisar um instantâneo, primeiro tem de ser adicionado à função necessária por um proprietário da subscrição.

> [!NOTE]
> Os proprietários e contribuintes não têm automaticamente esta função. Se querem ver instantâneos, devem acrescentar-se ao papel.

Os proprietários de subscrições devem atribuir a `Application Insights Snapshot Debugger` função aos utilizadores que irão inspecionar as imagens. Esta função pode ser atribuída a utilizadores ou grupos individuais por proprietários de subscrição para o recurso Target Application Insights ou o seu grupo de recursos ou subscrição.

1. Navegue para o recurso Application Insights no portal Azure.
1. Clique em **Controlo de acesso (IAM)** .
1. Clique no botão **de atribuição de função +Add.**
1. Selecione **Application Insights Snapshot Debugger** da lista de lançamento de **funções.**
1. Procure e introduza um nome para o utilizador adicionar.
1. Clique no botão **Guardar** para adicionar o utilizador à função.


> [!IMPORTANT]
> Os instantâneos podem potencialmente conter informações pessoais e outras informações sensíveis em valores variáveis e parâmetros.

## <a name="view-snapshots-in-the-portal"></a>Ver Instantâneos no Portal

Depois de ter ocorrido uma exceção na sua aplicação e de ter sido criado um instantâneo, deverá ter instantâneos para visualizar. Pode levar 5 a 10 minutos de uma exceção que ocorre a um instantâneo pronto e visível a partir do portal. Para visualizar instantâneos, no painel **'Falha',** selecione o botão **Operações** ao visualizar o separador **Operações** ou selecione o botão **Exceções** ao visualizar o separador **Exceções:**

![Página de falhas](./media/snapshot-debugger/failures-page.png)

Selecione uma operação ou exceção no painel direito para abrir o painel **de Detalhes de Transações De ponta a ponta** e, em seguida, selecione o evento de exceção. Se um instantâneo estiver disponível para a exceção dada, um botão **Open Debug Snapshot** aparece no painel direito com detalhes para a [exceção](./asp-net-exceptions.md).

![Abra o botão Debug Snapshot em exceção](./media/snapshot-debugger/e2e-transaction-page.png)

Na vista Debug Snapshot, vê-se uma pilha de chamadas e um painel de variáveis. Quando selecionar quadros da pilha de chamadas no painel de pilhas de chamadas, pode visualizar variáveis e parâmetros locais para essa chamada de função no painel de variáveis.

![Ver Debug Snapshot no portal](./media/snapshot-debugger/open-snapshot-portal.png)

As imagens podem incluir informações sensíveis, e por defeito não são visualizantes. Para ver as fotos, deve ter o `Application Insights Snapshot Debugger` papel atribuído a si.

## <a name="view-snapshots-in-visual-studio-2017-enterprise-or-above"></a>Ver Snapshots no Visual Studio 2017 Enterprise ou acima
1. Clique no botão **Download Snapshot** para descarregar um `.diagsession` ficheiro, que pode ser aberto pela Visual Studio Enterprise.

2. Para abrir o `.diagsession` ficheiro, é necessário instalar o componente Snapshot Debugger Visual Studio. O componente Snapshot Debugger é um componente necessário da carga de trabalho ASP.net no Visual Studio e pode ser selecionado a partir da lista de Componentes Individuais no instalador do Estúdio Visual. Se estiver a utilizar uma versão do Visual Studio antes da versão 15.5 do Visual Studio 2017, terá de instalar a extensão a partir do [Visual Studio Marketplace](https://aka.ms/snapshotdebugger).

3. Depois de abrir o ficheiro snapshot, aparece a página Minidump Debugging em Visual Studio. Clique em **Debug Managed Code** para começar a depurar a imagem. O instantâneo abre-se para a linha de código onde a exceção foi lançada para que possa depurar o estado atual do processo.

    ![Ver snapshot de debug no Estúdio Visual](./media/snapshot-debugger/open-snapshot-visualstudio.png)

O instantâneo descarregado inclui quaisquer ficheiros de símbolos encontrados no servidor de aplicações web. Estes ficheiros de símbolos são necessários para associar dados instantâneos com código fonte. Para aplicações do Serviço de Aplicações, certifique-se de ativar a implementação do símbolo quando publicar as suas aplicações web.

## <a name="how-snapshots-work"></a>Como funcionam os instantâneos

O Snapshot Collector é implementado como um processador de [telemetria de insights de aplicação.](./configuration-with-applicationinsights-config.md#telemetry-processors-aspnet) Quando a sua aplicação é escorria, o processador de telemetria do Coletor Snapshot é adicionado ao pipeline de telemetria da sua aplicação.
Cada vez que a sua aplicação chama [TrackException,](./asp-net-exceptions.md#exceptions)o Snapshot Collector calcula um ID de problema do tipo de exceção que está a ser lançado e do método de arremesso.
Cada vez que a sua aplicação chama TrackException, um contador é incrementado para o ID de problema apropriado. Quando o contador atinge o `ThresholdForSnapshotting` valor, o ID de problema é adicionado a um Plano de Cobrança.

O Snapshot Collector também monitoriza as exceções à medida que são lançadas ao subscrever o evento [AppDomain.CurrentDomain.FirstChanceException.](/dotnet/api/system.appdomain.firstchanceexception) Quando o evento dispara, o ID problemático da exceção é calculado e comparado com os IDs problemáticos do Plano de Recolha.
Se houver uma correspondência, então é criada uma imagem do processo de execução. O instantâneo é atribuído a um identificador único e a exceção é carimbada com esse identificador. Após o retorno do manipulador FirstChanceException, a exceção lançada é processada normalmente. Eventualmente, a exceção chega novamente ao método TrackException onde, juntamente com o identificador de instantâneo, é reportado ao Application Insights.

O processo principal continua a funcionar e a servir o tráfego aos utilizadores com pouca interrupção. Entretanto, a fotografia é entregue ao processo do Uploader Snapshot. O Uploader Snapshot cria uma minidump e envia-o para o Application Insights juntamente com quaisquer ficheiros de símbolo relevante (.pdb).

> [!TIP]
> - Um instantâneo de processo é um clone suspenso do processo de execução.
> - Criar o instantâneo leva cerca de 10 a 20 milissegundos.
> - O valor predefinido `ThresholdForSnapshotting` é 1. Este é também o valor mínimo. Portanto, a sua aplicação tem de desencadear a mesma exceção **duas vezes** antes de ser criada uma imagem instantânea.
> - Definido `IsEnabledInDeveloperMode` para verdadeiro se quiser gerar instantâneos enquanto depura no Estúdio Visual.
> - A taxa de criação instantânea é limitada pela `SnapshotsPerTenMinutesLimit` definição. Por predefinição, o limite é uma foto instantânea a cada dez minutos.
> - Não podem ser enviados mais de 50 instantâneos por dia.

## <a name="limitations"></a>Limitações

O período de retenção de dados predefinidos é de 15 dias. Para cada instância de Insights de Aplicação, é permitido um número máximo de 50 instantâneos por dia.

### <a name="publish-symbols"></a>Publicar símbolos
O Snapshot Debugger requer ficheiros de símbolos no servidor de produção para descodificar variáveis e proporcionar uma experiência de depuragem no Visual Studio.
A versão 15.2 (ou acima) do Visual Studio 2017 publica símbolos para as construções de lançamento por padrão quando publica no Serviço de Aplicações. Nas versões anteriores, é necessário adicionar a seguinte linha ao seu ficheiro de perfil de publicação `.pubxml` para que os símbolos sejam publicados no modo de lançamento:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Para o Azure Compute e outros tipos, certifique-se de que os ficheiros de símbolos estão na mesma pasta da aplicação principal .dll (normalmente, `wwwroot/bin` ) ou estão disponíveis no caminho atual.

> [!NOTE]
> Para obter mais informações sobre as diferentes opções de símbolos disponíveis consulte a documentação do [Estúdio Visual.](/visualstudio/ide/reference/advanced-build-settings-dialog-box-csharp?view=vs-2019#output
) Para obter os melhores resultados, recomendamos a utilização de "Full", "Portable" ou "Embedded".

### <a name="optimized-builds"></a>Construções otimizadas
Em alguns casos, as variáveis locais não podem ser vistas em construções de libertação devido a otimizações que são aplicadas pelo compilador JIT.
No entanto, nos Serviços de Aplicações Azure, o Snapshot Collector pode desoptimizar os métodos de arremesso que fazem parte do seu Plano de Recolha.

> [!TIP]
> Instale a Extensão do Site De Insights de Aplicação no seu Serviço de Aplicações para obter suporte de desoptimização.

## <a name="next-steps"></a>Passos seguintes
Ativar o Debugger Snapshot Debugger do Application Insights para a sua aplicação:

* [Serviço de Aplicações do Azure](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Cloud Services do Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Serviços de Tecido de Serviço Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas virtuais Azure e conjuntos de escala de máquinas virtuais](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas virtuais ou físicas no local](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Além da aplicação Insights Snapshot Debugger:
 
* [Desaponte os pontos de encaixe no seu código](/visualstudio/debugger/debug-live-azure-applications) para obter instantâneos sem esperar por uma exceção.
* [Diagnosticar exceções nas suas aplicações web](./asp-net-exceptions.md) explica como tornar mais exceções visíveis ao Application Insights.
* [A Deteção Inteligente](./proactive-diagnostics.md) descobre automaticamente anomalias de desempenho.

