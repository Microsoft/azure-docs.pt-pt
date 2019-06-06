---
title: Depurar a sua aplicação no Visual Studio | Documentos da Microsoft
description: Melhore a fiabilidade e desempenho dos seus serviços ao desenvolver e depurá-las no Visual Studio num cluster de desenvolvimento local.
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
ms.openlocfilehash: 682059914b5d86f5e670e373a4acf3e4ac6246ba
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428224"
---
# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>Depurar a sua aplicação do Service Fabric com o Visual Studio
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
>


## <a name="debug-a-local-service-fabric-application"></a>Depurar uma aplicação de Service Fabric local
Pode economizar tempo e dinheiro ao implementar e depurar a aplicação do Azure Service Fabric num cluster de desenvolvimento do computador local. Visual Studio 2019 ou 2015, pode implementar a aplicação no cluster local e ligar automaticamente o depurador a todas as instâncias da sua aplicação. Visual Studio tem de ser executado como administrador para ligar o depurador.

1. Iniciar um cluster de desenvolvimento local ao seguir os passos em [como configurar o ambiente de desenvolvimento do Service Fabric](service-fabric-get-started.md).
2. Prima **F5** ou clique em **depurar** > **iniciar depuração**.
   
    ![Começar a depurar uma aplicação][startdebugging]
3. Defina pontos de interrupção em seu código e percorrer a aplicação ao clicar em comandos do **depurar** menu.
   
   > [!NOTE]
   > Visual Studio anexa a todas as instâncias da sua aplicação. Enquanto estamos saindo por meio de código, os pontos de interrupção podem ser atingidos por vários processos, resultando em sessões simultâneas. Tente desativar os pontos de interrupção depois que estiverem a atingir, fazendo cada ponto de interrupção condicional no ID da thread ou ao utilizar eventos de diagnóstico.
   > 
   > 
4. O **eventos de diagnóstico** janela será aberta automaticamente para que pode ver eventos de diagnóstico em tempo real.
   
    ![Ver eventos de diagnóstico em tempo real][diagnosticevents]
5. Também pode abrir o **eventos de diagnóstico** janela no Cloud Explorer.  Sob **Service Fabric**, clique com o botão direito qualquer nó e escolha **rastreios de transmissão em fluxo do modo de exibição**.
   
    ![Abra a janela de eventos de diagnóstico][viewdiagnosticevents]
   
    Se pretender filtrar os rastreios para uma aplicação ou serviço específico, povolit trasy streamování nesse serviço específico ou aplicação.
6. Os eventos de diagnóstico que podem ser vistos no automaticamente gerado **ServiceEventSource.cs** de ficheiros e são chamados do código de aplicação.
   
    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```
7. O **eventos de diagnóstico** oferece suporte a janela de filtragem, colocar em pausa e inspecionar os eventos em tempo real.  O filtro é uma pesquisa de cadeia de caracteres simples de mensagem de evento, incluindo o respetivo conteúdo.
   
    ![Filtrar, colocar em pausa e retomar ou inspecionar eventos em tempo real][diagnosticeventsactions]
8. Depuração de serviços é como qualquer outro aplicativo de depuração. Normalmente, definirá pontos de interrupção através do Visual Studio para a depuração fácil. Apesar das coleções fiáveis replicar em vários nós, ainda que implementam IEnumerable. Essa implementação significa que pode usar a exibição dos resultados no Visual Studio enquanto depurar para ver o que armazenou dentro. Para fazê-lo, defina um ponto de interrupção em qualquer lugar no seu código.
   
    ![Começar a depurar uma aplicação][breakpoint]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>Depurar um aplicativo remoto do Service Fabric
Se estiver a executar seus aplicativos do Service Fabric num cluster do Service Fabric no Azure, pode depurar remotamente esses aplicativos, diretamente a partir do Visual Studio.

> [!NOTE]
> A funcionalidade requer [Service Fabric SDK 2.0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) e [Azure SDK para .NET 2.9](https://azure.microsoft.com/downloads/).    
> 
> 

<!-- -->
> [!WARNING]
> Depuração remota deve para cenários de desenvolvimento/teste e não deve ser utilizada em ambientes de produção, devido ao impacto nas aplicações em execução.
> 
> 

1. Navegue para o seu cluster no **Cloud Explorer**. Com o botão direito e escolher **ativar a depuração**
   
    ![Ativar a depuração remota][enableremotedebugging]
   
    Esta ação irá iniciar o processo de ativação a extensão de depuração remota em nós de cluster e configurações de rede necessária.
2. Com o botão direito do nó de cluster **Cloud Explorer**e escolha **anexar o depurador**
   
    ![Anexar o depurador][attachdebugger]
3. Na **anexar a processo** caixa de diálogo, escolha o processo que deseja depurar e clique em **Attach**
   
    ![Processo de escolha][chooseprocess]
   
    O nome do processo que pretende anexar a, é igual ao nome do seu nome de assembly do projeto de serviço.
   
    O depurador se ligará a todos os nós de execução do processo.
   
   * No caso em que estiver a depurar um serviço sem estado, todas as instâncias do serviço em todos os nós fazem parte da sessão de depuração.
   * Se estiver a depurar um serviço com estado, a réplica primária de qualquer partição será Active Directory e, portanto, é capturado pelo depurador. Se a réplica primária for movido durante a sessão de depuração, o processamento dessa réplica continuará a ser parte da sessão de depuração.
   * Para capturar apenas as partições relevantes ou instâncias de um determinado serviço, pode utilizar pontos de interrupção condicionais para interromper apenas uma partição específica ou uma instância.
     
     ![Ponto de interrupção condicional][conditionalbreakpoint]
     
     > [!NOTE]
     > Atualmente não suportamos a depuração de um cluster do Service Fabric com várias instâncias do mesmo nome do serviço executável.
     > 
     > 
4. Depois de concluir a depurar a aplicação, pode desativar a extensão de depuração remota clicando com o cluster no **Cloud Explorer** e escolha **desativar a depuração**
   
    ![Desativar a depuração remota][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>Transmissão em fluxo rastreios a partir de um nó de cluster remoto
Também pode para rastreios de fluxo diretamente a partir de um nó de cluster remoto para o Visual Studio. Esta funcionalidade permite-lhe para eventos de rastreio ETW de fluxo, produzidos um nó de cluster do Service Fabric.

> [!NOTE]
> Esta funcionalidade requer [Service Fabric SDK 2.0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) e [Azure SDK para .NET 2.9](https://azure.microsoft.com/downloads/).
> Esta funcionalidade suporta apenas clusters em execução no Azure.
> 
> 

<!-- -->
> [!WARNING]
> Rastreios de transmissão em fluxo deve para cenários de desenvolvimento/teste e não deve ser utilizada em ambientes de produção, devido ao impacto nas aplicações em execução.
> Num cenário de produção, deve contar com reencaminhamento de eventos com o diagnóstico do Azure.
> 
> 

1. Navegue para o seu cluster no **Cloud Explorer**. Com o botão direito e escolher **ativar rastreios de transmissão em fluxo**
   
    ![Ativar remoto trasy streamování][enablestreamingtraces]
   
    Esta ação irá iniciar o processo de ativação a extensão de rastreios de transmissão em fluxo em nós de cluster, bem como configurações de rede necessária.
2. Expanda a **nós** elemento **Cloud Explorer**, com o botão direito no nó que pretende rastreios a partir do stream e escolha **rastreios de transmissão em fluxo do Vista**
   
    ![Vista remoto rastreios de transmissão em fluxo][viewremotestreamingtraces]
   
    Repita o passo 2 para os nós à medida que pretende ver rastreios a partir de. Cada fluxo de nós aparecerá numa janela dedicada.
   
    Está agora pode ver os rastreios emitidos pelo Service Fabric e seus serviços. Se pretender filtrar os eventos para mostrar apenas uma aplicação específica, simplesmente digite o nome do aplicativo no filtro.
   
    ![Visualização de rastreios de transmissão em fluxo][viewingstreamingtraces]
3. Quando tiver terminado trasy streamování do seu cluster, pode desativar remoto trasy streamování, clicando com o cluster no **Cloud Explorer** e escolha **desativar rastreios de transmissão em fluxo**
   
    ![Desativar remoto trasy streamování][disablestreamingtraces]

## <a name="next-steps"></a>Passos Seguintes
* [Testar um serviço do Service Fabric](service-fabric-testability-overview.md).
* [Gerir as suas aplicações do Service Fabric no Visual Studio](service-fabric-manage-application-in-visual-studio.md).

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
