---
title: Azure Application Insights Snapshot Debugger para .NET apps
description: As snapshots de depuração são automaticamente recolhidas quando as exceções são lançadas na produção .NET apps
ms.topic: conceptual
ms.date: 10/23/2019
ms.reviewer: cweining
ms.openlocfilehash: 18f43ba90157d71ec9488b6858fa9f41b2ee42a5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79275766"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Instantâneos de depuração com exceções em aplicações .NET
Quando ocorre uma exceção, pode recolher automaticamente um instantâneo de depuração da sua aplicação web ao vivo. O instantâneo mostra o estado do código fonte e variáveis no momento em que a exceção foi lançada. O Snapshot Debugger em [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) monitoriza a telemetria de exceção da sua aplicação web. Recolhe fotos nas suas exceções de topo para que tenha a informação necessária para diagnosticar problemas na produção. Inclua o [pacote NuGet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) do colecionador Snapshot na sua aplicação e configurar opcionalmente os parâmetros de recolha em [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). Os instantâneos aparecem em [exceções](../../azure-monitor/app/asp-net-exceptions.md) no portal Application Insights.

Pode ver instantâneos de depuração no portal para visualizar a pilha de chamadas e inspecionar as variáveis em cada frame de pilha de chamadas. Para obter uma experiência de depuração mais poderosa com código fonte, abra fotos com visual studio 2019 Enterprise. No Visual Studio, também pode [definir Snappoints para tirar fotos interativamente](https://aka.ms/snappoint) sem esperar por uma exceção.

As fotos de depuração são armazenadas durante 15 dias. Esta política de retenção é definida numa base por aplicação. Se precisar de aumentar este valor, pode solicitar um aumento abrindo um caso de suporte no portal Azure.

## <a name="enable-application-insights-snapshot-debugger-for-your-application"></a>Ativar Insights de aplicação Snapshot Debugger para a sua aplicação
A coleção snapshot está disponível para:
* .NET Enquadramento e aplicações ASP.NET em execução .NET Quadro 4.5 ou posterior.
* .NET Core 2.0 e ASP.NET aplicações Core 2.0 em execução no Windows.

Os seguintes ambientes são apoiados:

* [Serviço de Aplicações do Azure](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) executando a família OS 4 ou mais tarde
* [Serviço Azure Serviço Fabric serviços](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) em execução no Windows Server 2012 R2 ou posterior
* [Máquinas virtuais azure e conjuntos](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) de escala de máquinas virtuais que executam o Windows Server 2012 R2 ou posterior
* [No local, máquinas virtuais ou físicas](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) que executam o Windows Server 2012 R2 ou posteriormente ou o Windows 8.1 ou posterior

> [!NOTE]
> As aplicações dos clientes (por exemplo, WPF, Windows Forms ou UWP) não são suportadas.

Se ativou o Snapshot Debugger mas não está a ver fotografias, consulte o nosso guia de [resolução de problemas.](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)

## <a name="grant-permissions"></a>Conceder permissões

O acesso a instantâneos está protegido pelo controlo de acesso baseado em funções (RBAC). Para inspecionar uma imagem instantânea, primeiro deve ser adicionado ao papel necessário por um proprietário de subscrição.

> [!NOTE]
> Proprietários e contribuintes não têm automaticamente este papel. Se querem ver instantâneos, têm de se juntar ao papel.

Os proprietários de `Application Insights Snapshot Debugger` subscrições devem atribuir a função aos utilizadores que inspecionem as imagens. Esta função pode ser atribuída a utilizadores ou grupos individuais pelos proprietários de subscrições para o recurso Target Application Insights ou o seu grupo de recursos ou subscrição.

1. Navegue para o recurso Application Insights no portal Azure.
1. Clique em **Controlo de acesso (IAM)**.
1. Clique no botão de atribuição de **funções +Adicionar.**
1. Selecione **Application Insights Snapshot Debugger** da lista de abandono de **papéis.**
1. Procure e introduza um nome para o utilizador adicionar.
1. Clique no botão **Guardar** para adicionar o utilizador à função.


> [!IMPORTANT]
> Os instantâneos podem potencialmente conter informações pessoais e outras informações sensíveis em valores variáveis e parâmetros.

## <a name="view-snapshots-in-the-portal"></a>Ver snapshots no Portal

Depois de ter ocorrido uma exceção na sua aplicação e de ter sido criado um instantâneo, deverá ter imagens para visualizar. Pode levar de 5 a 10 minutos a partir de uma exceção que ocorre a um instantâneo pronto e visível a partir do portal. Para visualizar instantâneos, no painel **'Falha',** selecione o botão **Operações** ao visualizar o separador **Operações** ou selecione o botão **Exceções** ao visualizar o separador **Exceções:**

![Página de Falhas](./media/snapshot-debugger/failures-page.png)

Selecione uma operação ou exceção no painel certo para abrir o painel de detalhes de **transação** de ponta a ponta e, em seguida, selecione o evento de exceção. Se estiver disponível um instantâneo para a exceção dada, aparece um botão **Open Debug Snapshot** no painel direito com detalhes para [a exceção](../../azure-monitor/app/asp-net-exceptions.md).

![Abra o botão Debug Snapshot em exceção](./media/snapshot-debugger/e2e-transaction-page.png)

Na vista Debug Snapshot, você vê uma pilha de chamadas e um painel de variáveis. Quando selecionar os quadros da pilha de chamadas no painel de pilhas de chamada, pode ver variáveis e parâmetros locais para essa chamada de função no painel de variáveis.

![Ver Debug Snapshot no portal](./media/snapshot-debugger/open-snapshot-portal.png)

As imagens podem incluir informações sensíveis, e por padrão não são veríveis. Para ver imagens, deve `Application Insights Snapshot Debugger` ter o papel que lhe foi atribuído.

## <a name="view-snapshots-in-visual-studio-2017-enterprise-or-above"></a>Ver Snapshots no Visual Studio 2017 Enterprise ou acima
1. Clique no botão Download `.diagsession` **Snapshot** para descarregar um ficheiro, que pode ser aberto pela Visual Studio Enterprise.

2. Para abrir `.diagsession` o ficheiro, é necessário instalar o componente Snapshot Debugger Visual Studio. O componente Snapshot Debugger é um componente necessário da carga de trabalho ASP.net no Estúdio Visual e pode ser selecionado a partir da lista de Componentes Individuais no instalador do Estúdio Visual. Se estiver a utilizar uma versão do Visual Studio antes do Visual Studio 2017 versão 15.5, terá de instalar a extensão do [Visual Studio Marketplace](https://aka.ms/snapshotdebugger).

3. Depois de abrir o ficheiro snapshot, aparece a página Minidump Debugging no Estúdio Visual. Clique no **Código Gerido de Debug** para começar a depurar o instantâneo. O instantâneo abre-se para a linha de código onde a exceção foi lançada para que possa desincomodá-lo o estado atual do processo.

    ![Ver instantâneo de depuração no Estúdio Visual](./media/snapshot-debugger/open-snapshot-visualstudio.png)

O instantâneo descarregado inclui quaisquer ficheiros de símbolos que tenham sido encontrados no seu servidor de aplicações web. Estes ficheiros de símbolos são necessários para associar dados instantâneos com código fonte. Para aplicações de Serviço de Aplicações, certifique-se de permitir a implementação de símbolos quando publicar as suas aplicações web.

## <a name="how-snapshots-work"></a>Como as fotos funcionam

O Snapshot Collector é implementado como processador de [telemetria Insights de Aplicação](../../azure-monitor/app/configuration-with-applicationinsights-config.md#telemetry-processors-aspnet)Insights . Quando a sua aplicação funciona, o Processador de Telemetria snapshot Collector é adicionado ao pipeline de telemetria da sua aplicação.
Cada vez que a sua aplicação chama [TrackException,](../../azure-monitor/app/asp-net-exceptions.md#exceptions)o Snapshot Collector calcula um ID de problema do tipo de exceção que está a ser lançado e do método de lançamento.
Cada vez que a sua aplicação chama TrackException, um contador é incrementado para o ID problema apropriado. Quando o contador `ThresholdForSnapshotting` atinge o valor, o ID problema é adicionado a um Plano de Recolha.

O Snapshot Collector também monitoriza as exceções à medida que são lançadas ao subscrever o evento [AppDomain.CurrentDomain.FirstChanceException.](https://docs.microsoft.com/dotnet/api/system.appdomain.firstchanceexception) Quando o evento dispara, a identificação problemática da exceção é calculada e comparada com as Ids problemáticas no Plano de Recolha.
Se houver uma correspondência, então é criada uma imagem do processo de execução. O instantâneo é atribuído a um identificador único e a exceção é carimbada com esse identificador. Após a retorna do manipulador FirstChanceException, a exceção lançada é processada normalmente. Eventualmente, a exceção atinge novamente o método TrackException onde, juntamente com o identificador de instantâneo, é reportado ao Application Insights.

O processo principal continua a decorrer e a servir o tráfego aos utilizadores com pouca interrupção. Entretanto, a fotografia é transmitida ao processo snapshot uploader. O Uploader Snapshot cria um minidump e envia-o para Application Insights juntamente com qualquer símbolo relevante (.pdb) ficheiros.

> [!TIP]
> - Um instantâneo de processo é um clone suspenso do processo de execução.
> - Criar o instantâneo leva cerca de 10 a 20 milissegundos.
> - O valor `ThresholdForSnapshotting` padrão para 1. Este é também o valor mínimo. Por isso, a sua aplicação tem de desencadear a mesma exceção **duas vezes** antes de ser criado um instantâneo.
> - Pronto `IsEnabledInDeveloperMode` para verdade se quiser gerar instantâneos enquanto depura no Estúdio Visual.
> - A taxa de criação `SnapshotsPerTenMinutesLimit` instantânea é limitada pela configuração. Por padrão, o limite é um instantâneo a cada dez minutos.
> - Não podem ser enviados mais de 50 instantâneos por dia.

## <a name="limitations"></a>Limitações

O período de retenção de dados por defeito é de 15 dias. Para cada instância de Insights de Aplicação, é permitido um número máximo de 50 instantâneos por dia.

### <a name="publish-symbols"></a>Publicar símbolos
O Snapshot Debugger requer ficheiros de símbolos no servidor de produção para descodificar variáveis e fornecer uma experiência de depuração no Estúdio Visual.
A versão 15.2 (ou superior) do Visual Studio 2017 publica símbolos para lançamento saqueado por padrão quando publica no App Service. Em versões anteriores, é necessário adicionar `.pubxml` a seguinte linha ao ficheiro de perfil da publicação para que os símbolos sejam publicados no modo de lançamento:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Para a Azure Compute e outros tipos, certifique-se de que os ficheiros de `wwwroot/bin`símbolo estão na mesma pasta da aplicação principal .dll (normalmente, ) ou estão disponíveis no caminho atual.

> [!NOTE]
> Para obter mais informações sobre as diferentes opções de símbolos disponíveis consulte a documentação do [Estúdio Visual.](https://docs.microsoft.com/visualstudio/ide/reference/advanced-build-settings-dialog-box-csharp?view=vs-2019#output
) Para obter os melhores resultados, recomendamos a utilização de "Full", "Portable" ou "Embedded".

### <a name="optimized-builds"></a>Construções otimizadas
Em alguns casos, as variáveis locais não podem ser vistas em construções de libertação devido a otimizações que são aplicadas pelo compilador JIT.
No entanto, nos Serviços de Aplicações Azure, o Snapshot Collector pode desotimizar métodos de arremesso que fazem parte do seu Plano de Recolha.

> [!TIP]
> Instale a extensão do site de insights de aplicação no seu Serviço de Aplicações para obter suporte de desotimização.

## <a name="next-steps"></a>Passos seguintes
Ativar Insights de aplicação Snapshot Debugger para a sua aplicação:

* [Serviço de Aplicações do Azure](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Cloud Services do Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Serviço Azure Fabric](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas virtuais azure e conjuntos de escala de máquinas virtuais](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [No local máquinas virtuais ou físicas](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Além da aplicação Insights Snapshot Debugger:
 
* [Detete pontos de encaixe no seu código](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications) para obter instantâneos sem esperar por uma exceção.
* [Diagnosticar exceções nas suas aplicações web](../../azure-monitor/app/asp-net-exceptions.md) explica como tornar mais exceções visíveis aos Insights de Aplicação.
* [A Deteção Inteligente](../../azure-monitor/app/proactive-diagnostics.md) descobre automaticamente anomalias de desempenho.
