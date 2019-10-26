---
title: Depurador de Instantâneos de informações do Aplicativo Azure para aplicativos .NET | Microsoft Docs
description: Instantâneos de depuração são coletados automaticamente quando exceções são lançadas em aplicativos .NET de produção
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/23/2019
ms.reviewer: brahmnes
ms.openlocfilehash: 33a9db5fa9f31a0c4548ecdeb6c0ca2f12ac8246
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899785"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Depurar instantâneos em exceções em aplicativos .NET
Quando ocorre uma exceção, você pode coletar automaticamente um instantâneo de depuração de seu aplicativo Web em tempo real. O instantâneo mostra o estado do código-fonte e as variáveis no momento em que a exceção foi lançada. A Depurador de Instantâneos (visualização) no [aplicativo Azure insights](../../azure-monitor/app/app-insights-overview.md) monitora a telemetria de exceção do seu aplicativo Web. Ele coleta instantâneos em suas exceções de lançamento mais alto para que você tenha as informações necessárias para diagnosticar problemas na produção. Inclua o [pacote NuGet do coletor de instantâneos](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) em seu aplicativo e, opcionalmente, configure os parâmetros de coleção em [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). Os instantâneos aparecem em [exceções](../../azure-monitor/app/asp-net-exceptions.md) no portal de Application insights.

Pode ver instantâneos de depuração no portal para visualizar a pilha de chamadas e inspecionar as variáveis em cada frame de pilha de chamadas. Para obter uma experiência de depuração mais potente com o código-fonte, abra instantâneos com o Visual Studio 2019 Enterprise. No Visual Studio, você também pode [definir Snappoints para fazer instantâneos de forma interativa](https://aka.ms/snappoint) sem esperar por uma exceção.

Os instantâneos de depuração são armazenados por 15 dias. Essa política de retenção é definida em uma base por aplicativo. Se você precisar aumentar esse valor, poderá solicitar um aumento abrindo um caso de suporte na portal do Azure.

## <a name="enable-application-insights-snapshot-debugger-for-your-application"></a>Habilitar Application Insights Depurador de Instantâneos para seu aplicativo
A coleta de instantâneos está disponível para:
* Aplicativos .NET Framework e ASP.NET em execução .NET Framework 4,5 ou posterior.
* Aplicativos .NET Core 2,0 e ASP.NET Core 2,0 em execução no Windows.

Há suporte para os seguintes ambientes:

* [App Service do Azure](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Serviços de nuvem do Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) executando a família de sistemas operacionais 4 ou posterior
* [Serviços de Service Fabric do Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) em execução no Windows Server 2012 R2 ou posterior
* [Máquinas virtuais do Azure e conjuntos de dimensionamento de máquinas virtuais](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) que executam o Windows Server 2012 R2 ou posterior
* [Máquinas virtuais ou físicas locais](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) que executam o Windows Server 2012 R2 ou posterior

> [!NOTE]
> Não há suporte para aplicativos cliente (por exemplo, WPF, Windows Forms ou UWP).

Se você tiver habilitado Depurador de Instantâneos, mas não estiver vendo instantâneos, consulte nosso [Guia de solução de problemas](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

## <a name="grant-permissions"></a>Conceder permissões

O acesso a instantâneos é protegido pelo RBAC (controle de acesso baseado em função). Para inspecionar um instantâneo, você deve primeiro ser adicionado à função necessária por um proprietário de assinatura.

> [!NOTE]
> Os proprietários e colaboradores não têm essa função automaticamente. Se quiserem exibir instantâneos, eles deverão ser adicionados à função.

Os proprietários da assinatura devem atribuir a função de `Application Insights Snapshot Debugger` a usuários que inspecionarão instantâneos. Essa função pode ser atribuída a usuários individuais ou grupos por proprietários de assinatura para o recurso de Application Insights de destino ou seu grupo de recursos ou assinatura.

1. Navegue até o recurso de Application Insights no portal do Azure.
1. Clique em **Controlo de acesso (IAM)** .
1. Clique no botão **+ Adicionar atribuição de função** .
1. Selecione **Application Insights depurador de instantâneos** na lista suspensa **funções** .
1. Procure e insira um nome para o usuário a ser adicionado.
1. Clique no botão **salvar** para adicionar o usuário à função.


> [!IMPORTANT]
> Os instantâneos podem conter, potencialmente, outras informações pessoais e confidenciais em valores de parâmetros e variáveis.

## <a name="view-snapshots-in-the-portal"></a>Exibir instantâneos no portal

Depois que uma exceção tiver ocorrido em seu aplicativo e um instantâneo tiver sido criado, você deverá ter instantâneos para exibir. Pode levar de 5 a 10 minutos a partir de uma exceção que ocorre em um instantâneo pronto e visível no Portal. Para exibir instantâneos, no painel **falha** , selecione o botão **operações** ao exibir a guia **operações** ou selecione o botão **exceções** ao exibir a guia **exceções** :

![Página falhas](./media/snapshot-debugger/failures-page.png)

Selecione uma operação ou exceção no painel direito para abrir o painel de **detalhes de transação de ponta a ponta** e, em seguida, selecione o evento de exceção. Se um instantâneo estiver disponível para a exceção determinada, um botão **abrir instantâneo de depuração** aparecerá no painel direito com detalhes para a [exceção](../../azure-monitor/app/asp-net-exceptions.md).

![Abrir botão depurar instantâneo na exceção](./media/snapshot-debugger/e2e-transaction-page.png)

No modo de exibição instantâneo de depuração, você vê uma pilha de chamadas e um painel variáveis. Ao selecionar quadros da pilha de chamadas no painel pilha de chamadas, você pode exibir variáveis locais e parâmetros para essa chamada de função no painel variáveis.

![Exibir instantâneo de depuração no portal](./media/snapshot-debugger/open-snapshot-portal.png)

Os instantâneos podem incluir informações confidenciais e, por padrão, não são visíveis. Para exibir instantâneos, você deve ter a função `Application Insights Snapshot Debugger` atribuída a você.

## <a name="view-snapshots-in-visual-studio-2017-enterprise-or-above"></a>Exibir instantâneos no Visual Studio 2017 Enterprise ou superior
1. Clique no botão **baixar instantâneo** para baixar um arquivo de `.diagsession`, que pode ser aberto pelo Visual Studio Enterprise.

2. Para abrir o arquivo `.diagsession`, você precisa ter o componente Depurador de Instantâneos Visual Studio instalado. O componente Depurador de Instantâneos é um componente necessário da carga de trabalho ASP.net no Visual Studio e pode ser selecionado na lista de componentes individuais no instalador do Visual Studio. Se você estiver usando uma versão do Visual Studio anterior ao Visual Studio 2017 versão 15,5, será necessário instalar a extensão do [Visual Studio Marketplace](https://aka.ms/snapshotdebugger).

3. Depois de abrir o arquivo de instantâneo, a página depuração de minidespejo no Visual Studio é exibida. Clique em **depurar código gerenciado** para iniciar a depuração do instantâneo. O instantâneo é aberto na linha de código em que a exceção foi lançada para que você possa depurar o estado atual do processo.

    ![Exibir instantâneo de depuração no Visual Studio](./media/snapshot-debugger/open-snapshot-visualstudio.png)

O instantâneo baixado inclui todos os arquivos de símbolo que foram encontrados no servidor de aplicativos da Web. Esses arquivos de símbolo são necessários para associar dados de instantâneos ao código-fonte. Para aplicativos do serviço de aplicativo, certifique-se de habilitar a implantação de símbolo ao publicar seus aplicativos Web.

## <a name="how-snapshots-work"></a>Como funcionam os instantâneos

O Snapshot Collector é implementado como um [processador de Application insights Telemetry](../../azure-monitor/app/configuration-with-applicationinsights-config.md#telemetry-processors-aspnet). Quando o aplicativo é executado, o processador de telemetria Snapshot Collector é adicionado ao pipeline de telemetria do aplicativo.
Cada vez que seu aplicativo chama [trackexception](../../azure-monitor/app/asp-net-exceptions.md#exceptions), o snapshot Collector COMPUTA uma ID do problema do tipo de exceção que está sendo gerada e o método de lançamento.
Cada vez que seu aplicativo chama Trackexception, um contador é incrementado para a ID de problema apropriada. Quando o contador atinge o valor de `ThresholdForSnapshotting`, a ID do problema é adicionada a um plano de coleta.

O Snapshot Collector também monitora exceções à medida que elas são geradas assinando o evento [AppDomain. CurrentDomain. FirstChanceException](https://docs.microsoft.com/dotnet/api/system.appdomain.firstchanceexception) . Quando esse evento é acionado, a ID do problema da exceção é computada e comparada com as IDs do problema no plano de coleta.
Se houver uma correspondência, um instantâneo do processo em execução será criado. O instantâneo recebe um identificador exclusivo e a exceção é carimbada com esse identificador. Depois que o manipulador FirstChanceException retorna, a exceção gerada é processada como normal. Eventualmente, a exceção atinge o método Trackexception novamente, em que ele, junto com o identificador de instantâneo, é relatado para Application Insights.

O processo principal continua executando e atendendo o tráfego aos usuários com pouca interrupção. Enquanto isso, o instantâneo é entregue ao processo de carregador de instantâneo. O carregador de instantâneo cria um minidespejo e o carrega para Application Insights junto com qualquer arquivo de símbolo (. pdb) relevante.

> [!TIP]
> - Um instantâneo de processo é um clone suspenso do processo em execução.
> - A criação do instantâneo leva cerca de 10 a 20 milissegundos.
> - O valor padrão para `ThresholdForSnapshotting` é 1. Esse também é o valor mínimo. Portanto, seu aplicativo deve disparar a mesma exceção **duas vezes** antes de um instantâneo ser criado.
> - Defina `IsEnabledInDeveloperMode` como true se você quiser gerar instantâneos durante a depuração no Visual Studio.
> - A taxa de criação do instantâneo é limitada pela configuração de `SnapshotsPerTenMinutesLimit`. Por padrão, o limite é um instantâneo a cada dez minutos.
> - Não é possível carregar mais de 50 instantâneos por dia.

## <a name="limitations"></a>Limitações

O período de retenção de dados padrão é de 15 dias. Para cada instância de Application Insights, um número máximo de 50 instantâneos é permitido por dia.

### <a name="publish-symbols"></a>Publicar símbolos
O Depurador de Instantâneos requer que arquivos de símbolo no servidor de produção decodifiquem variáveis e forneçam uma experiência de depuração no Visual Studio.
A versão 15,2 (ou superior) do Visual Studio 2017 publica símbolos para Builds de versão por padrão quando ele é publicado no serviço de aplicativo. Em versões anteriores, você precisa adicionar a seguinte linha ao seu perfil de publicação `.pubxml` arquivo para que os símbolos sejam publicados no modo de versão:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Para a computação do Azure e outros tipos, verifique se os arquivos de símbolo estão na mesma pasta do Application. dll principal (normalmente, `wwwroot/bin`) ou estão disponíveis no caminho atual.

> [!NOTE]
> Para obter mais informações sobre as diferentes opções de símbolo que estão disponíveis, consulte a [documentação do Visual Studio](https://docs.microsoft.com/visualstudio/ide/reference/advanced-build-settings-dialog-box-csharp?view=vs-2019#output
). Para obter melhores resultados, recomendamos o uso de "completo", "portátil" ou "incorporado".

### <a name="optimized-builds"></a>Builds otimizados
Em alguns casos, as variáveis locais não podem ser exibidas em compilações de versão devido a otimizações que são aplicadas pelo compilador JIT.
No entanto, nos serviços de Azure App, o Snapshot Collector pode desotimizar os métodos de lançamento que fazem parte de seu plano de coleta.

> [!TIP]
> Instale o Application Insights a extensão de site em seu serviço de aplicativo para obter suporte à desotimização.

## <a name="next-steps"></a>Passos seguintes
Habilite Depurador de Instantâneos Application Insights para seu aplicativo:

* [App Service do Azure](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Serviços de Service Fabric do Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas virtuais do Azure e conjuntos de dimensionamento de máquinas virtuais](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas virtuais ou físicas locais](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Além Application Insights Depurador de Instantâneos:
 
* [Defina snappoints em seu código](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications) para obter instantâneos sem esperar por uma exceção.
* [Diagnosticar exceções em seus aplicativos Web](../../azure-monitor/app/asp-net-exceptions.md) explica como tornar mais exceções visíveis para Application insights.
* A [detecção inteligente](../../azure-monitor/app/proactive-diagnostics.md) descobre automaticamente anomalias de desempenho.
