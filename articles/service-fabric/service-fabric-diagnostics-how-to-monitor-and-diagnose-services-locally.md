---
title: Depurar o Azure Service Fabric aplicações no Windows | Documentos da Microsoft
description: Saiba como monitorizar e diagnosticar os serviços escritos usando o Microsoft Azure Service Fabric numa máquina de desenvolvimento local.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: edcc0631-ed2d-45a3-851d-2c4fa0f4a326
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: c0547fe817cf81adbf417bfad195a197a2071cc6
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56822364"
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Monitorizar e diagnosticar serviços numa configuração de desenvolvimento do computador local
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
> 
> 

Monitoramento, detetar e diagnosticar e resolução de problemas permitem para os serviços continuar com a mínima interrupção para a experiência do usuário. Enquanto a monitorização e diagnóstico é fundamental num ambiente de produção real de implementada, a eficiência irá depender de adotar um modelo semelhante durante o desenvolvimento de serviços para garantir que eles funcionam quando passar para um programa de configuração do mundo real. Service Fabric torna mais fácil para os desenvolvedores de serviço implementar o diagnóstico de forma totalmente integrada pode trabalhar em configurações de desenvolvimento local da única máquina e configurações de cluster de produção do mundo real.

## <a name="event-tracing-for-windows"></a>Rastreamento de eventos para Windows
[Rastreamento de eventos para Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) é a tecnologia recomendada para mensagens de rastreio no Service Fabric. Alguns dos benefícios de usando o ETW são:

* **ETW é rápida.** Ele foi criado como uma tecnologia de rastreamento que tenha um impacto mínimo nos tempos de execução de código.
* **Rastreamento ETW funciona perfeitamente em ambientes de desenvolvimento local e também as configurações de cluster do mundo real.** Isso significa que não precisa reescrever seus códigos de rastreamento, quando estiver pronto para implementar o código para um cluster real.
* **Código de sistema do Service Fabric também usa o ETW para rastreamento interno.** Isto permite-lhe ver os rastreios de aplicação intercalados com rastreios de sistema do Service Fabric. Ele também ajuda-o a compreender mais facilmente as sequências e interrelationships entre o código de aplicativo e eventos do sistema subjacente.
* **Não existe suporte incorporado nas ferramentas do Service Fabric Visual Studio para ver eventos do ETW.** Eventos do ETW aparecem na vista de eventos de diagnóstico do Visual Studio, assim que o Visual Studio está corretamente configurada com o Service Fabric. 

## <a name="view-service-fabric-system-events-in-visual-studio"></a>Ver eventos de sistema do Service Fabric no Visual Studio
Service Fabric emite eventos do ETW para o ajudar a compreender o que está acontecendo na plataforma de desenvolvedores de aplicativos. Se ainda não o fez, vá em frente e siga os passos em [criando sua primeira aplicação no Visual Studio](service-fabric-tutorial-create-dotnet-app.md). Essas informações ajudarão a uma aplicação em execução com o Visualizador de eventos de diagnóstico que mostra as mensagens de rastreio.

1. Se o diagnóstico de janela de eventos não mostra automaticamente, vá para o **View** separador no Visual Studio, escolha **Other Windows** e, em seguida **Visualizador de eventos de diagnóstico**.
2. Cada evento tem informações de metadados padrão que diz a o nó, a aplicação e o serviço que é proveniente do evento. Também pode filtrar a lista de eventos utilizando o **filtrar eventos** caixa na parte superior da janela de eventos. Por exemplo, poderá filtrar na **nome do nó** ou **nome do serviço.** E quando estiver olhando detalhes do evento, pode também colocar em pausa utilizando a **colocar em pausa** botão na parte superior da janela de eventos e retomar mais tarde sem nenhuma perda de eventos.
   
   ![Visualizador de eventos de diagnóstico do Visual Studio](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

## <a name="add-your-own-custom-traces-to-the-application-code"></a>Adicionar o seu próprio rastreios personalizados ao código do aplicativo
Os modelos de projeto do Service Fabric Visual Studio contêm código de exemplo. O código mostra como adicionar rastreios ETW de código do aplicativo personalizada que aparecem no Visualizador de ETW do Visual Studio juntamente com os rastreios de sistema do Service Fabric. A vantagem deste método é que metadados é adicionado automaticamente à rastreios e o Visual Studio Visualizador de eventos diagnóstico já está configurado para exibi-las.

Para projetos criados a partir da **modelos de serviço** (com ou sem estado) apenas procura o `RunAsync` implementação:

1. A chamada para `ServiceEventSource.Current.ServiceMessage` no `RunAsync` método mostra um exemplo de um rastreio ETW personalizada do código do aplicativo.
2. Na **ServiceEventSource.cs** arquivo, encontrará uma sobrecarga para o `ServiceEventSource.ServiceMessage` método que deve ser utilizado para eventos de elevada frequência devido a motivos de desempenho.

Para projetos criados a partir da **modelos de ator** (com ou sem estado):

1. Abra o **"ProjectName".-cs** ficheiro onde *ProjectName* é o nome que escolheu para o seu projeto do Visual Studio.  
2. Encontrar o código `ActorEventSource.Current.ActorMessage(this, "Doing Work");` no *DoWorkAsync* método.  Este é um exemplo de um rastreio ETW personalizado escrito a partir do código da aplicação.  
3. No ficheiro **ActorEventSource.cs**, encontrará uma sobrecarga para o `ActorEventSource.ActorMessage` método que deve ser utilizado para eventos de elevada frequência devido a motivos de desempenho.

Depois de adicionar o rastreamento do ETW personalizado ao seu código de serviço, pode criar, implementar e executar o aplicativo novamente para ver os seus eventos no Visualizador de eventos de diagnóstico. Se depurar o aplicativo com **F5**, Visualizador de eventos de diagnóstico será aberta automaticamente.

## <a name="next-steps"></a>Passos Seguintes
O mesmo código de rastreamento que adicionou à sua aplicação acima para obter um diagnóstico local irá funcionar com ferramentas que pode utilizar para ver esses eventos quando a execução da sua aplicação num cluster do Azure. Veja estes artigos que discutem as diferentes opções para as ferramentas e descrevem como pode configurá-los.

* [Como recolher registos com o diagnóstico do Azure](service-fabric-diagnostics-how-to-setup-wad.md)
* [Agregação de eventos e coleções com o EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md)

