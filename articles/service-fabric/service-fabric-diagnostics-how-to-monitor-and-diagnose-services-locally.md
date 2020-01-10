---
title: Depurar aplicativos Service Fabric do Azure no Windows
description: Saiba como monitorar e diagnosticar seus serviços escritos usando Microsoft Azure Service Fabric em um computador de desenvolvimento local.
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 8435bb82afddd0070679768bb8d22ad9290f2279
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464618"
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Monitorar e diagnosticar serviços em uma configuração de desenvolvimento de computador local
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
> 
> 

O monitoramento, a detecção, o diagnóstico e a solução de problemas permitem que os serviços continuem com o mínimo de interrupção para a experiência do usuário. Embora o monitoramento e o diagnóstico sejam críticos em um ambiente de produção implantado real, a eficiência dependerá da adoção de um modelo semelhante durante o desenvolvimento de serviços para garantir que eles funcionem quando você migrar para uma configuração do mundo real. Service Fabric facilita para os desenvolvedores de serviço a implementação de diagnósticos que podem funcionar diretamente entre as configurações de desenvolvimento local de um único computador e as configurações de cluster de produção reais.

## <a name="event-tracing-for-windows"></a>Rastreamento de Eventos para Windows
O ETW ( [rastreamento de eventos para Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) ) é a tecnologia recomendada para rastrear mensagens em Service Fabric. Alguns benefícios de usar o ETW são:

* **O ETW é rápido.** Ele foi criado como uma tecnologia de rastreamento que tem impacto mínimo sobre os tempos de execução de código.
* **O rastreamento ETW funciona diretamente entre ambientes de desenvolvimento locais e também configurações de cluster reais.** Isso significa que você não precisa reescrever seu código de rastreamento quando estiver pronto para implantar seu código em um cluster real.
* **Service Fabric código do sistema também usa o ETW para rastreamento interno.** Isso permite que você exiba os rastreamentos de aplicativo intercalados com Service Fabric rastreamentos do sistema. Ele também ajuda a entender com mais facilidade as sequências e as inter-relações entre o código do aplicativo e os eventos no sistema subjacente.
* **Há suporte interno em ferramentas do Service Fabric Visual Studio para exibir eventos ETW.** Os eventos ETW aparecem na exibição de eventos de diagnóstico do Visual Studio quando o Visual Studio é configurado corretamente com Service Fabric. 

## <a name="view-service-fabric-system-events-in-visual-studio"></a>Exibir Service Fabric eventos do sistema no Visual Studio
Service Fabric emite eventos ETW para ajudar os desenvolvedores de aplicativos a entender o que está acontecendo na plataforma. Se você ainda não tiver feito isso, vá em frente e siga as etapas em [criando seu primeiro aplicativo no Visual Studio](service-fabric-tutorial-create-dotnet-app.md). Essas informações ajudarão você a colocar um aplicativo em funcionamento com o Visualizador de eventos de diagnóstico mostrando as mensagens de rastreamento.

1. Se a janela eventos de diagnóstico não aparecer automaticamente, vá para a guia **Exibir** no Visual Studio, escolha **outras janelas** e, em seguida, **Visualizador de eventos de diagnóstico**.
2. Cada evento tem informações de metadados padrão que informam o nó, o aplicativo e o serviço do qual o evento é proveniente. Você também pode filtrar a lista de eventos usando a caixa **filtrar eventos** na parte superior da janela eventos. Por exemplo, você pode filtrar o **nome do nó** ou o **nome do serviço.** E, quando estiver examinando os detalhes do evento, você também pode pausar usando o botão **Pausar** na parte superior da janela eventos e retomar mais tarde sem perda de eventos.
   
   ![Visualizador de eventos de diagnóstico do Visual Studio](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

## <a name="add-your-own-custom-traces-to-the-application-code"></a>Adicionar seus próprios rastreamentos personalizados ao código do aplicativo
O Service Fabric modelos de projeto do Visual Studio contêm código de exemplo. O código mostra como adicionar rastreamentos ETW de código de aplicativo personalizado que aparecem no visualizador ETW do Visual Studio junto com rastreamentos do sistema de Service Fabric. A vantagem desse método é que os metadados são adicionados automaticamente aos rastreamentos e o Visualizador de eventos de diagnóstico do Visual Studio já está configurado para exibi-los.

Para projetos criados a partir dos **modelos de serviço** (com ou sem estado), basta procurar a implementação de `RunAsync`:

1. A chamada para `ServiceEventSource.Current.ServiceMessage` no método `RunAsync` mostra um exemplo de um rastreamento ETW personalizado do código do aplicativo.
2. No arquivo **ServiceEventSource.cs** , você encontrará uma sobrecarga para o método `ServiceEventSource.ServiceMessage` que deve ser usado para eventos de alta frequência devido a motivos de desempenho.

Para projetos criados com base nos **modelos de ator** (sem estado ou com estado):

1. Abra o arquivo **"ProjectName". cs** , em que *ProjectName* é o nome escolhido para seu projeto do Visual Studio.  
2. Localize o código `ActorEventSource.Current.ActorMessage(this, "Doing Work");` no método *DoWorkAsync* .  Este é um exemplo de um rastreamento ETW personalizado escrito do código do aplicativo.  
3. No arquivo **ActorEventSource.cs**, você encontrará uma sobrecarga para o método `ActorEventSource.ActorMessage` que deve ser usado para eventos de alta frequência devido a motivos de desempenho.

Depois de adicionar o rastreamento ETW personalizado ao seu código de serviço, você pode criar, implantar e executar o aplicativo novamente para ver seus eventos no Visualizador de eventos de diagnóstico. Se você depurar o aplicativo com **F5**, o Visualizador de eventos de diagnóstico será aberto automaticamente.

## <a name="next-steps"></a>Passos seguintes
O mesmo código de rastreamento que você adicionou ao seu aplicativo acima para diagnóstico local funcionará com ferramentas que você pode usar para exibir esses eventos ao executar seu aplicativo em um cluster do Azure. Confira estes artigos que discutem as diferentes opções para as ferramentas e descrevem como você pode configurá-las.

* [Como coletar logs com Diagnóstico do Azure](service-fabric-diagnostics-how-to-setup-wad.md)
* [Agregação e coleta de eventos usando EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md)

