---
title: Depurador de instantâneos de Azure Application Insights para aplicações de .NET | Documentos da Microsoft
description: Depure instantâneos são automaticamente recolhidos quando exceções forem lançadas em aplicações de .NET de produção
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: brahmnes
ms.date: 08/06/2019
ms.author: mbullwin
ms.openlocfilehash: 02d72ab877577e97592dfdd763a58cb01b201d8b
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839362"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Instantâneos de depuração com exceções em aplicações .NET
Quando ocorre uma exceção, é possível recolher automaticamente um instantâneo de depuração da sua aplicação web em direto. O instantâneo mostra o estado do código-fonte e variáveis no momento que a exceção foi acionada. Snapshot Debugger (pré-visualização) [do Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) monitoriza a telemetria de exceção da sua aplicação web. Recolhe os instantâneos em suas exceções jogando em cima, de modo a que tem as informações necessárias diagnosticar problemas na produção. Incluir o [pacote de NuGet do recoletor de instantâneos](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) na sua aplicação e, opcionalmente, configure parâmetros de coleção no [applicationinsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). Os instantâneos são apresentadas na [exceções](../../azure-monitor/app/asp-net-exceptions.md) no portal do Application Insights.

Pode ver instantâneos de depuração no portal para visualizar a pilha de chamadas e inspecionar as variáveis em cada frame de pilha de chamadas. Para obter uma experiência de depuração mais potente com o código-fonte, abra instantâneos com o Visual Studio 2019 Enterprise. No Visual Studio, pode também [definir Snappoints interativamente tirar instantâneos](https://aka.ms/snappoint) sem esperar por uma exceção.

Depure instantâneos são armazenados durante sete dias. Esta política de retenção está definida numa base por aplicação. Se precisar de aumentar este valor, pode pedir um aumento ao abrir um incidente de suporte no portal do Azure.

## <a name="enable-application-insights-snapshot-debugger-for-your-application"></a>Habilitar Application Insights Depurador de Instantâneos para seu aplicativo
Coleção de instantâneos está disponível para:
* Aplicações de .NET framework e o ASP.NET com o .NET Framework 4.5 ou posterior.
* Aplicações de .NET core 2.0 e ASP.NET Core 2.0 em execução no Windows.

São suportados os seguintes ambientes:

* [Serviço de Aplicações do Azure](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Serviços de nuvem do Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) executando a família de sistemas operacionais 4 ou posterior
* [Serviços de Service Fabric do Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) em execução no Windows Server 2012 R2 ou posterior
* [Máquinas virtuais do Azure e conjuntos de dimensionamento de máquinas virtuais](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) que executam o Windows Server 2012 R2 ou posterior
* [Máquinas virtuais ou físicas locais](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) que executam o Windows Server 2012 R2 ou posterior

> [!NOTE]
> Não são suportadas aplicações de cliente (por exemplo, WPF, formulários Windows ou UWP).

Se você tiver habilitado Depurador de Instantâneos, mas não estiver vendo instantâneos, consulte nosso [Guia de solução de problemas](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

## <a name="grant-permissions"></a>Conceder permissões

Acesso a instantâneos está protegido pelo controlo de acesso baseado em funções (RBAC). Para inspecionar um instantâneo, deve primeiro ser adicionado à função necessária por um proprietário da subscrição.

> [!NOTE]
> Os proprietários e contribuintes não têm automaticamente esta função. Se pretende ver instantâneos, têm de adicionar-se para a função.

Proprietários de subscrições devem atribuir a `Application Insights Snapshot Debugger` função aos utilizadores que fará uma checagem instantâneos. Esta função pode ser atribuída a utilizadores individuais ou grupos por proprietários de subscrições para o destino do recurso do Application Insights ou o grupo de recursos ou subscrição.

1. Navegue para o recurso do Application Insights no portal do Azure.
1. Clique em **controlo de acesso (IAM)** .
1. Clique nas **+ adicionar atribuição de função** botão.
1. Selecione **Snapshot Debugger do Application Insights** partir do **funções** na lista pendente.
1. Procure e introduza um nome para o utilizador adicione.
1. Clique nas **guardar** botão para adicionar o utilizador à função.


> [!IMPORTANT]
> Instantâneos potencialmente podem conter informações confidenciais pessoais e outras valores de variável e o parâmetro.

## <a name="view-snapshots-in-the-portal"></a>Exibir instantâneos no portal

Depois que uma exceção tiver ocorrido em seu aplicativo e um instantâneo tiver sido criado, você deverá ter instantâneos para exibir. Pode levar de 5 a 10 minutos a partir de uma exceção que ocorre em um instantâneo pronto e visível no Portal. Para exibir instantâneos, no painel **falha** , selecione o botão **operações** ao exibir a guia **operações** ou selecione o botão **exceções** ao exibir a guia **exceções** :

![Página falhas](./media/snapshot-debugger/failures-page.png)

Selecione uma operação ou exceção no painel direito para abrir o painel de **detalhes de transação de ponta a ponta** e, em seguida, selecione o evento de exceção. Se um instantâneo estiver disponível para a exceção determinada, um botão **abrir instantâneo de depuração** aparecerá no painel direito com detalhes para a [exceção](../../azure-monitor/app/asp-net-exceptions.md).

![Botão de abrir instantâneo de depuração na exceção](./media/snapshot-debugger/e2e-transaction-page.png)

Na vista de instantâneo de depuração, verá uma pilha de chamadas e um painel de variáveis. Quando seleciona quadros de pilha de chamadas no painel de pilha de chamada, pode ver variáveis locais e parâmetros para essa função chamam-se no painel de variáveis.

![Ver instantâneo de depuração no portal](./media/snapshot-debugger/open-snapshot-portal.png)

Instantâneos podem incluir informações confidenciais e, por predefinição não estão visíveis. Para ver os instantâneos, tem de ter o `Application Insights Snapshot Debugger` função atribuída a.

## <a name="view-snapshots-in-visual-studio-2017-enterprise-or-above"></a>Exibir instantâneos no Visual Studio 2017 Enterprise ou superior
1. Clique no botão **baixar instantâneo** para baixar um `.diagsession` arquivo, que pode ser aberto por Visual Studio Enterprise.

2. Para abrir o `.diagsession` arquivo, você precisa ter o componente depurador de instantâneos Visual Studio instalado. O componente Depurador de Instantâneos é um componente necessário da carga de trabalho ASP.net no Visual Studio e pode ser selecionado na lista de componentes individuais no instalador do Visual Studio. Se você estiver usando uma versão do Visual Studio anterior ao Visual Studio 2017 versão 15,5, será necessário instalar a extensão do [Visual Studio Marketplace](https://aka.ms/snapshotdebugger).

3. Depois de abrir o ficheiro de instantâneo, é apresentada a página de Minidespejo depuração no Visual Studio. Clique em **depurar código gerenciado** para iniciar a depuração do instantâneo. O instantâneo é aberta a linha de código em que a exceção foi acionada, para que pode depurar o estado atual do processo.

    ![Ver instantâneo de depuração no Visual Studio](./media/snapshot-debugger/open-snapshot-visualstudio.png)

O instantâneo transferido inclui os arquivos de símbolo que foram encontrados no seu servidor de aplicativos web. Esses arquivos de símbolo são necessárias para associar dados de instantâneos de código-fonte. Para aplicações de serviço de aplicações, certifique-se ativar a implementação de símbolo quando publicar as suas aplicações web.

## <a name="how-snapshots-work"></a>Como funcionam os instantâneos

O Recoletor de instantâneos é implementado como um [processador de telemetria do Application Insights](../../azure-monitor/app/configuration-with-applicationinsights-config.md#telemetry-processors-aspnet). Quando executa a aplicação, o processador de telemetria do Recoletor de instantâneos é adicionado ao pipeline de telemetria do seu aplicativo.
Sempre que suas chamadas de aplicativo [TrackException](../../azure-monitor/app/asp-net-exceptions.md#exceptions), o Recoletor de instantâneos computa um ID de problema do tipo de exceção que está a ser emitida e o método throwing.
Sempre que seu aplicativo chama TrackException, um contador é incrementado para o ID de problema apropriado. Quando o contador de atinge o `ThresholdForSnapshotting` valor, o ID do problema é adicionado a um plano de coleção.

O Recoletor de instantâneos também monitora exceções, como eles são acionados por subscrever a [AppDomain.CurrentDomain.FirstChanceException](https://docs.microsoft.com/dotnet/api/system.appdomain.firstchanceexception) eventos. Quando esse evento é acionado, o ID do problema da exceção é calculado e comparado com os IDs de problema no plano de coleção.
Se existir uma correspondência, em seguida, é criado um instantâneo do processo em execução. O instantâneo é atribuído um identificador exclusivo e a exceção está marcada com esse identificador. Depois do manipulador de FirstChanceException retorna, a exceção lançada é processada como normal. Eventualmente, a exceção atinge novamente o método de TrackException onde, juntamente com o identificador de instantâneos, será reportado para o Application Insights.

O processo principal continua a executar e a servir o tráfego para os utilizadores com pouca interrupção. Enquanto isso, o instantâneo é transferido para o processo de carregador de instantâneo. O carregador de instantâneo cria um minidespejo e carrega-o para o Application Insights, juntamente com quaisquer arquivos de símbolo relevantes (. pdb).

> [!TIP]
> - Um instantâneo de processo é um clone suspenso do processo em execução.
> - Criar o instantâneo demora cerca de 10 a 20 milissegundos.
> - O valor predefinido para `ThresholdForSnapshotting` é 1. Isso também é o valor mínimo. Por conseguinte, a aplicação tem de acionar a mesma exceção **duas vezes** antes da criação de um instantâneo.
> - Definir `IsEnabledInDeveloperMode` como verdadeira se pretender gerar instantâneos durante a depuração no Visual Studio.
> - A taxa de criação de instantâneos está limitada pelo `SnapshotsPerTenMinutesLimit` definição. Por predefinição, o limite é um instantâneo de cada dez minutos.
> - Podem ser carregados não mais de 50 instantâneos por dia.

## <a name="limitations"></a>Limitações

O período de retenção de dados padrão é de 15 dias. Para cada instância de Application Insights, um número máximo de 50 instantâneos é permitido por dia.

### <a name="publish-symbols"></a>Publicar símbolos
O Snapshot Debugger requer arquivos de símbolo no servidor de produção para descodificar variáveis e fornecer uma experiência de depuração no Visual Studio.
Versão 15.2 (ou superior) do Visual Studio 2017 publica símbolos para as compilações de versão por predefinição quando publica no serviço de aplicações. Nas versões anteriores, terá de adicionar a seguinte linha ao seu perfil de publicação `.pubxml` para que os símbolos são publicados no modo de versão do ficheiro:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Para a computação do Azure e outros tipos, certifique-se de que os arquivos de símbolo estão na mesma pasta do ficheiro. dll do aplicativo principal (normalmente, `wwwroot/bin`) ou estão disponíveis no caminho atual.

> [!NOTE]
> Para obter mais informações sobre as diferentes opções de símbolo que estão disponíveis [, consulte a](https://docs.microsoft.com/visualstudio/ide/reference/advanced-build-settings-dialog-box-csharp?view=vs-2019#output
)documentação do Visual Studio. Para obter melhores resultados, recomendamos o uso de "completo", "portátil" ou "incorporado".

### <a name="optimized-builds"></a>Compilações otimizadas
Em alguns casos, não não possível ver variáveis locais, nas compilações de versão, devido a otimizações que são aplicadas pelo compilador JIT.
No entanto, nos serviços de aplicações do Azure, o Recoletor de instantâneos pode deoptimize lançando métodos que fazem parte do seu plano de coleção.

> [!TIP]
> Instale a extensão de Site do Application Insights no seu serviço de aplicações para obter suporte deoptimization.

## <a name="next-steps"></a>Passos Seguintes
Habilite Depurador de Instantâneos Application Insights para seu aplicativo:

* [Serviço de Aplicações do Azure](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Serviços em Nuvem do Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Serviços de Service Fabric do Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas virtuais do Azure e conjuntos de dimensionamento de máquinas virtuais](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas virtuais ou físicas locais](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Além Application Insights Depurador de Instantâneos:
 
* [Definir snappoints em seu código](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications) obter instantâneos sem esperar por uma exceção.
* [Diagnosticar exceções nas suas aplicações web](../../azure-monitor/app/asp-net-exceptions.md) explica como tornar as exceções mais visíveis para o Application Insights.
* [Deteção inteligente](../../azure-monitor/app/proactive-diagnostics.md) Deteta automaticamente anomalias de desempenho.
