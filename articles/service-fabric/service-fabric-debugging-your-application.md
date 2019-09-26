---
title: Depurar seu aplicativo no Visual Studio | Microsoft Docs
description: Melhore a confiabilidade e o desempenho de seus serviços desenvolvendo e Depurando-os no Visual Studio em um cluster de desenvolvimento local.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: cb888532-bcdb-4e47-95e4-bfbb1f644da4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: b9ab36343dd11bdb5bd7cc1adcf2c8b1b971dab3
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300688"
---
# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>Depurar seu aplicativo Service Fabric usando o Visual Studio
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
>


## <a name="debug-a-local-service-fabric-application"></a>Depurar um aplicativo de Service Fabric local
Você pode economizar tempo e dinheiro Implantando e Depurando seu aplicativo de Service Fabric do Azure em um cluster de desenvolvimento de computador local. O Visual Studio 2019 ou 2015 pode implantar o aplicativo no cluster local e conectar automaticamente o depurador a todas as instâncias do seu aplicativo. O Visual Studio deve ser executado como administrador para conectar o depurador.

1. Inicie um cluster de desenvolvimento local seguindo as etapas em [configurando seu ambiente de desenvolvimento de Service Fabric](service-fabric-get-started.md).
2. Pressione **F5** ou clique em **depurar** > **Iniciar Depuração**.
   
    ![Iniciar a depuração de um aplicativo][startdebugging]
3. Defina pontos de interrupção em seu código e percorra o aplicativo clicando em comandos no menu **depurar** .
   
   > [!NOTE]
   > O Visual Studio é anexado a todas as instâncias do seu aplicativo. Enquanto você está percorrendo o código, os pontos de interrupção podem ser atingidos por vários processos, resultando em sessões simultâneas. Tente desabilitar os pontos de interrupção depois que eles forem atingidos, tornando cada ponto de interrupção condicional na ID do thread ou usando eventos de diagnóstico.
   > 
   > 
4. A janela **eventos de diagnóstico** será aberta automaticamente para que você possa exibir eventos de diagnóstico em tempo real.
   
    ![Exibir eventos de diagnóstico em tempo real][diagnosticevents]
5. Você também pode abrir a janela **eventos de diagnóstico** no Cloud Explorer.  Em **Service Fabric**, clique com o botão direito do mouse em qualquer nó e escolha **Exibir rastreamentos de streaming**.
   
    ![Abrir a janela eventos de diagnóstico][viewdiagnosticevents]
   
    Se você quiser filtrar os rastreamentos para um serviço ou aplicativo específico, habilite os rastreamentos de streaming nesse serviço ou aplicativo específico.
6. Os eventos de diagnóstico podem ser vistos no arquivo **ServiceEventSource.cs** gerado automaticamente e são chamados do código do aplicativo.
   
    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```
7. A janela **eventos de diagnóstico** dá suporte à filtragem, pausa e inspeção de eventos em tempo real.  O filtro é uma pesquisa de cadeia de caracteres simples da mensagem de evento, incluindo seu conteúdo.
   
    ![Filtrar, pausar e retomar ou inspecionar eventos em tempo real][diagnosticeventsactions]
8. Os serviços de depuração são como a depuração de qualquer outro aplicativo. Normalmente, você definirá pontos de interrupção por meio do Visual Studio para fácil depuração. Embora as coleções confiáveis repliquem em vários nós, elas ainda implementam IEnumerable. Essa implementação significa que você pode usar a exibição de resultados no Visual Studio enquanto depura para ver o que você armazenou dentro. Para fazer isso, defina um ponto de interrupção em qualquer lugar no seu código.
   
    ![Iniciar a depuração de um aplicativo][breakpoint]


### <a name="running-a-script-as-part-of-debugging"></a>Executando um script como parte da depuração
Em determinados cenários, talvez seja necessário executar um script como parte do início de uma sessão de depuração (por exemplo, quando não estiver usando serviços padrão).

No Visual Studio, você pode adicionar um arquivo chamado **Start-Service. ps1** na pasta **scripts** do projeto de aplicativo Service Fabric (. sfproj). Esse script será invocado depois que o aplicativo tiver sido criado no cluster local.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>Depurar um aplicativo de Service Fabric remoto
Se seus aplicativos Service Fabric estiverem em execução em um Cluster Service Fabric no Azure, você poderá depurar esses aplicativos remotamente, diretamente do Visual Studio.

> [!NOTE]
> O recurso requer [Service Fabric sdk 2,0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) e [SDK do Azure para .NET 2,9](https://azure.microsoft.com/downloads/).    
> 
> 

<!-- -->
> [!WARNING]
> A depuração remota destina-se a cenários de desenvolvimento/teste e não a ser usada em ambientes de produção, devido ao impacto nos aplicativos em execução.
> 
> 

1. Navegue até o cluster no **Cloud Explorer**. Clique com o botão direito do mouse e escolha **Habilitar depuração**
   
    ![Habilitar depuração remota][enableremotedebugging]
   
    Essa ação iniciará o processo de habilitar a extensão de depuração remota em seus nós de cluster e as configurações de rede necessárias.
2. Clique com o botão direito do mouse no nó de cluster no **Cloud Explorer**e escolha **anexar depurador**
   
    ![Anexar depurador][attachdebugger]
3. Na caixa de diálogo **anexar ao processo** , escolha o processo que você deseja depurar e clique em **anexar**
   
    ![Escolher processo][chooseprocess]
   
    O nome do processo ao qual você deseja anexar é igual ao nome do seu nome de assembly do projeto de serviço.
   
    O depurador será anexado a todos os nós que executam o processo.
   
   * No caso em que você está depurando um serviço sem estado, todas as instâncias do serviço em todos os nós fazem parte da sessão de depuração.
   * Se você estiver depurando um serviço com estado, somente a réplica primária de qualquer partição estará ativa e, portanto, capturada pelo depurador. Se a réplica primária for movida durante a sessão de depuração, o processamento dessa réplica ainda fará parte da sessão de depuração.
   * Para capturar apenas as partições ou instâncias relevantes de um determinado serviço, você pode usar pontos de interrupção condicionais para interromper apenas uma partição ou instância específica.
     
     ![Ponto de interrupção condicional][conditionalbreakpoint]
     
     > [!NOTE]
     > No momento, não há suporte para a depuração de um Cluster Service Fabric com várias instâncias do mesmo nome do executável do serviço.
     > 
     > 
4. Depois de concluir a depuração do aplicativo, você pode desabilitar a extensão de depuração remota clicando com o botão direito do mouse no cluster no **Cloud Explorer** e escolhendo **Desabilitar depuração**
   
    ![Desabilitar depuração remota][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>Streaming de rastreamentos de um nó de cluster remoto
Você também é capaz de transmitir rastreamentos diretamente de um nó de cluster remoto para o Visual Studio. Esse recurso permite que você transmita eventos de rastreamento ETW, produzidos em um nó de Cluster Service Fabric.

> [!NOTE]
> Este recurso requer [Service Fabric sdk 2,0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) e [SDK do Azure para .NET 2,9](https://azure.microsoft.com/downloads/).
> Esse recurso só dá suporte a clusters em execução no Azure.
> 
> 

<!-- -->
> [!WARNING]
> Os rastreamentos de streaming destinam-se a cenários de desenvolvimento/teste e não a serem usados em ambientes de produção, devido ao impacto nos aplicativos em execução.
> Em um cenário de produção, você deve contar com o encaminhamento de eventos usando Diagnóstico do Azure.
> 
> 

1. Navegue até o cluster no **Cloud Explorer**. Clique com o botão direito do mouse e escolha **habilitar rastreamentos de streaming**
   
    ![Habilitar rastreamentos de streaming remoto][enablestreamingtraces]
   
    Essa ação iniciará o processo de habilitar a extensão de rastreamentos de streaming em seus nós de cluster, bem como as configurações de rede necessárias.
2. Expanda o elemento **nós** no **Cloud Explorer**, clique com o botão direito do mouse no nó do qual você deseja transmitir rastreamentos e escolha **Exibir rastreamentos de streaming**
   
    ![Exibir rastreamentos de streaming remoto][viewremotestreamingtraces]
   
    Repita a etapa 2 para quantos nós desejar ver os rastreamentos. Cada fluxo de nós aparecerá em uma janela dedicada.
   
    Agora você pode ver os rastreamentos emitidos por Service Fabric e seus serviços. Se você quiser filtrar os eventos para mostrar apenas um aplicativo específico, basta digitar o nome do aplicativo no filtro.
   
    ![Exibindo rastreamentos de streaming][viewingstreamingtraces]
3. Depois de concluir os rastreamentos de streaming do cluster, você poderá desabilitar os rastreamentos de streaming remoto clicando com o botão direito do mouse no cluster no **Cloud Explorer** e escolhendo **desabilitar rastreamentos de streaming**
   
    ![Desabilitar rastreamentos de streaming remoto][disablestreamingtraces]

## <a name="next-steps"></a>Passos seguintes
* [Testar um serviço de Service Fabric](service-fabric-testability-overview.md).
* [Gerencie seus aplicativos de Service Fabric no Visual Studio](service-fabric-manage-application-in-visual-studio.md).

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png
[enableremotedebugging]: ./media/service-fabric-debugging-your-application/enableremotedebugging.png
[attachdebugger]: ./media/service-fabric-debugging-your-application/attachdebugger.png
[chooseprocess]: ./media/service-fabric-debugging-your-application/chooseprocess.png
[conditionalbreakpoint]: ./media/service-fabric-debugging-your-application/conditionalbreakpoint.png
[disableremotedebugging]: ./media/service-fabric-debugging-your-application/disableremotedebugging.png
[enablestreamingtraces]: ./media/service-fabric-debugging-your-application/enablestreamingtraces.png
[viewingstreamingtraces]: ./media/service-fabric-debugging-your-application/viewingstreamingtraces.png
[viewremotestreamingtraces]: ./media/service-fabric-debugging-your-application/viewremotestreamingtraces.png
[disablestreamingtraces]: ./media/service-fabric-debugging-your-application/disablestreamingtraces.png
