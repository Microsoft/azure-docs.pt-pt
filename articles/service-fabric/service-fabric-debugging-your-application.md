---
title: Depurar a sua aplicação no Visual Studio
description: Melhore a fiabilidade e o desempenho dos seus serviços desenvolvendo-os e depurando-os no Visual Studio num cluster de desenvolvimento local.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 0b7d08d610c883240abedc66c55abba64a74c8e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576320"
---
# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>Depurar a sua aplicação do Service Fabric com o Visual Studio
> [!div class="op_single_selector"]
> * [Estúdio Visual/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
>


## <a name="debug-a-local-service-fabric-application"></a>Depurar uma aplicação local de tecido de serviço
Pode economizar tempo e dinheiro implantando e depurando a sua aplicação Azure Service Fabric num cluster de desenvolvimento de computador local. O Visual Studio 2019 ou 2015 pode implementar a aplicação no cluster local e ligar automaticamente o depurar a todas as instâncias da sua aplicação. O Estúdio Visual tem de ser executado como Administrador para ligar o depurar.

1. Inicie um cluster de desenvolvimento local seguindo os passos na Configuração do [seu ambiente de desenvolvimento do tecido de serviço.](service-fabric-get-started.md)
2. Prima **F5** ou clique em **Debug**  >  **Start Debugging**.
   
    ![Screenshot que mostra o menu Debug.][startdebugging]
3. Desfaça os pontos de rutura no seu código e passe pela aplicação clicando nos comandos no menu **Debug.**
   
   > [!NOTE]
   > O Visual Studio anexa-se a todas as instâncias da sua aplicação. Enquanto está a passar pelo código, os pontos de rutura podem ser atingidos por vários processos que resultam em sessões simultâneas. Tente desativar os pontos de rutura depois de atingidos, fazendo com que cada ponto de rutura condicione o ID do fio ou utilizando eventos de diagnóstico.
   > 
   > 
4. A janela **Eventos de Diagnóstico** será aberta automaticamente para que possa ver eventos de diagnóstico em tempo real.
   
    ![Ver eventos de diagnóstico em tempo real][diagnosticevents]
5. Também pode abrir a janela **de Eventos de Diagnóstico** no Cloud Explorer.  Em **Tecido de Serviço,** clique com o botão direito de qualquer nó e escolha **ver rastreios de streaming**.
   
    ![Abra a janela de eventos de diagnóstico][viewdiagnosticevents]
   
    Se pretender filtrar os seus vestígios para um serviço ou aplicação específico, ative vestígios de streaming nesse serviço ou aplicação específico.
6. Os eventos de diagnóstico podem ser vistos no ficheiro **.cs ServiceEventSource** gerado automaticamente e são chamados do código de aplicação.
   
    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```
7. A janela **Eventos de Diagnóstico** suporta filtragem, pausa e inspeção de eventos em tempo real.  O filtro é uma simples pesquisa de cordas da mensagem do evento, incluindo o seu conteúdo.
   
    ![Filtrar, fazer uma pausa e retomar, ou inspecionar eventos em tempo real][diagnosticeventsactions]
8. Depurar serviços é como depurar qualquer outra aplicação. Normalmente, irá definir Breakpoints através do Visual Studio para facilitar a depuração. Apesar de as Coleções Fiáveis se replicarem em vários nós, ainda implementam o IEnumerable. Esta implementação significa que pode utilizar a Vista de Resultados no Estúdio Visual enquanto depura para ver o que guardou no seu interior. Para tal, estabeleça um ponto de rutura em qualquer lugar do seu código.
   
    ![Comece a depurar uma aplicação][breakpoint]


### <a name="running-a-script-as-part-of-debugging"></a>Executar um guião como parte da depuragem
Em certos cenários poderá ser necessário executar um script como parte de iniciar uma sessão de depuração (por exemplo, quando não utilizar os Serviços Predefinidos).

No Visual Studio, pode adicionar um ficheiro chamado **Start-Service.ps1** na pasta **Scripts** do projeto De aplicação de tecido de serviço (.sfproj). Este script será invocado após a aplicação ter sido criada no cluster local.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>Depurar uma aplicação de tecido de serviço remoto
Se as suas aplicações Service Fabric estiverem a funcionar num cluster de Tecido de Serviço em Azure, pode desativar remotamente estas aplicações, diretamente do Visual Studio.

> [!NOTE]
> A funcionalidade requer [O Tecido de Serviço SDK 2.0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) e [Azure SDK para .NET 2.9](https://azure.microsoft.com/downloads/).    

<!-- -->
> [!WARNING]
> A depuragem remota destina-se a cenários dev/teste e não deve ser utilizada em ambientes de produção, devido ao impacto nas aplicações em execução.

1. Navegue para o seu cluster no **Cloud Explorer.** Clique à direita e escolha **Enable Debugging**
   
    ![Permitir depuragem remota][enableremotedebugging]
   
    Esta ação iniciará o processo de permitir a extensão de depuração remota nos seus nós de cluster e configurações de rede necessárias.
2. Clique com o botão direito no nó de cluster no **Cloud Explorer** e escolha **Attach Debugger**
   
    ![Anexar depurar][attachdebugger]
3. No **Anexar para processar** diálogo, escolha o processo que pretende depurar e clique em **Anexar**
   
    ![Escolha o processo][chooseprocess]
   
    O nome do processo a que pretende anexar é igual ao nome do seu nome de montagem do projeto de serviço.
   
    O depurador liga-se a todos os nós que executam o processo.
   
   * No caso de estar a depurar um serviço apátrida, todas as instâncias do serviço em todos os nós fazem parte da sessão de depuração.
   * Se estiver a depurar um serviço estatal, apenas a réplica primária de qualquer partição estará ativa e, portanto, apanhada pelo depurante. Se a réplica primária se mover durante a sessão de depurar, o processamento dessa réplica continuará a fazer parte da sessão de depurar.
   * Para capturar apenas divisórias ou instâncias relevantes de um determinado serviço, pode utilizar pontos de rutura condicional para quebrar apenas uma partição ou instância específica.
     
     ![Ponto de rutura condicional][conditionalbreakpoint]
     
     > [!NOTE]
     > Atualmente não suportamos a depuração de um cluster de Tecido de Serviço com múltiplas instâncias do mesmo nome executável de serviço.
     > 
     > 
4. Assim que terminar de depurar a sua aplicação, pode desativar a extensão de depuração remota clicando no cluster no **Cloud Explorer** e escolher **Depurar**
   
    ![Desativar a depuração remota][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>Streaming de vestígios de um nó de cluster remoto
Também é possível transmitir vestígios diretamente de um nó de cluster remoto para o Estúdio Visual. Esta funcionalidade permite-lhe transmitir eventos de vestígios ETW, produzidos num nó de cluster de tecido de serviço.

> [!NOTE]
> Esta funcionalidade requer [O Tecido de Serviço SDK 2.0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) e [Azure SDK para .NET 2.9](https://azure.microsoft.com/downloads/).
> Esta funcionalidade suporta apenas clusters em execução em Azure.
> 
> 

<!-- -->
> [!WARNING]
> Os vestígios de streaming destinam-se a cenários dev/teste e não devem ser utilizados em ambientes de produção, devido ao impacto nas aplicações em execução.
> Num cenário de produção, deve contar com eventos de encaminhamento utilizando o Azure Diagnostics.

1. Navegue para o seu cluster no **Cloud Explorer.** Clique com o botão direito e escolha **Ativar os rastreios de streaming**
   
    ![Ativar vestígios de streaming remoto][enablestreamingtraces]
   
    Esta ação iniciará o processo de permitir a extensão de vestígios de streaming nos seus nós de cluster, bem como as configurações de rede necessárias.
2. Expandir o elemento **Nodes** no **Cloud Explorer,** clique com o nó de que pretende transmitir vestígios e escolha **Ver Rastreios de Streaming**
   
    ![Ver vestígios de streaming remoto][viewremotestreamingtraces]
   
    Repita o passo 2 para os nós que quiser ver vestígios. Cada fluxo de nós aparecerá numa janela dedicada.
   
    Agora pode ver os vestígios emitidos pela Service Fabric e os seus serviços. Se pretender filtrar os eventos para apenas apresentar uma aplicação específica, basta digitar o nome da aplicação no filtro.
   
    ![Visualização de vestígios de streaming][viewingstreamingtraces]
3. Uma vez que você é feito streaming traços do seu cluster, você pode desativar traços de streaming remoto, clicando no cluster no **Cloud Explorer** e escolher **Desativar os rastreios de streaming**
   
    ![Desativar vestígios de streaming remoto][disablestreamingtraces]

## <a name="next-steps"></a>Passos seguintes
* [Teste um serviço de tecido de serviço.](service-fabric-testability-overview.md)
* [Gerir as suas aplicações de Tecido de Serviço no Estúdio Visual.](service-fabric-manage-application-in-visual-studio.md)

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
