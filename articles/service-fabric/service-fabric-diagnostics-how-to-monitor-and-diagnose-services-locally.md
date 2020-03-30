---
title: Aplicativos de tecido de serviço Debug Azure no Windows
description: Aprenda a monitorizar e diagnosticar os seus serviços escritos usando o Microsoft Azure Service Fabric numa máquina de desenvolvimento local.
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 8435bb82afddd0070679768bb8d22ad9290f2279
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258515"
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Monitorizar e diagnosticar serviços numa configuração de desenvolvimento do computador local
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
> 
> 

A monitorização, deteção, diagnóstico e resolução de problemas permitem que os serviços continuem com a mínima perturbação na experiência do utilizador. Embora a monitorização e os diagnósticos sejam cruciais num ambiente de produção implementado, a eficiência dependerá da adoção de um modelo semelhante durante o desenvolvimento de serviços para garantir que funcionam quando se muda para uma configuração real. O Service Fabric facilita que os desenvolvedores de serviços implementem diagnósticos que possam funcionar perfeitamente em conjuntos de desenvolvimento local de máquina única e configurações de clusters de produção no mundo real.

## <a name="event-tracing-for-windows"></a>Rastreio de eventos para janelas
O rastreio de [eventos para Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) é a tecnologia recomendada para rastrear mensagens em Tecido de Serviço. Alguns benefícios da utilização de ETW são:

* **ETW é rápido.** Foi construído como uma tecnologia de rastreio que tem o mínimo impacto nos tempos de execução de códigos.
* **O rastreio da ETW funciona perfeitamente em ambientes de desenvolvimento local e também em conjuntos de clusters do mundo real.** Isto significa que não precisa reescrever o seu código de rastreio quando estiver pronto para implementar o seu código para um aglomerado real.
* **O código do sistema service Fabric também utiliza ETW para rastreio interno.** Isto permite-lhe visualizar os vestígios da sua aplicação interisolados com os vestígios do sistema De Serviço Fabric. Também o ajuda a entender mais facilmente as sequências e inter-relações entre o seu código de aplicação e eventos no sistema subjacente.
* **Existe suporte incorporado nas ferramentas do Estúdio Visual de Tecido de Serviço para visualizar eventos DaTW.** Os eventos ETW aparecem na vista de Eventos de Diagnóstico do Estúdio Visual uma vez que o Estúdio Visual está corretamente configurado com tecido de serviço. 

## <a name="view-service-fabric-system-events-in-visual-studio"></a>Ver eventos do sistema de tecido de serviço em Estúdio Visual
Service Fabric emite eventos Da ETW para ajudar os desenvolvedores de aplicações a entender o que está a acontecer na plataforma. Se ainda não o fez, siga os passos na Criação da [sua primeira aplicação no Visual Studio](service-fabric-tutorial-create-dotnet-app.md). Esta informação irá ajudá-lo a obter uma aplicação em funcionamento com o Visualizador de Eventos de Diagnóstico mostrando as mensagens de rastreio.

1. Se a janela de eventos de diagnóstico não aparecer automaticamente, vá ao separador **Ver** em Estúdio Visual, escolha **Outras Janelas** e, em **seguida, Diagnostic Events Viewer**.
2. Cada evento tem informações padrão de metadados que lhe dizem o nó, aplicação e serviço de que o evento está vindo. Também pode filtrar a lista de eventos utilizando a caixa de **eventos Filter** na parte superior da janela de eventos. Por exemplo, pode filtrar o nome do **nó** ou o nome do **serviço.** E quando você está olhando para os detalhes do evento, você também pode parar usando o botão **Pausa** na parte superior da janela de eventos e retomar mais tarde sem qualquer perda de eventos.
   
   ![Visualizal Studio Diagnostics Events Viewer](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

## <a name="add-your-own-custom-traces-to-the-application-code"></a>Adicione os seus próprios vestígios personalizados ao código de aplicação
Os modelos do projeto Service Fabric Visual Studio contêm código de amostra. O código mostra como adicionar os traços de código de aplicação personalizados ETW que aparecem no espectador Visual Studio ETW ao lado de vestígios de sistema do Service Fabric. A vantagem deste método é que os metadados são adicionados automaticamente aos vestígios, e o VisualizaStudio Diagnostic Events Viewer já está configurado para os exibir.

Para projetos **criados** a partir dos modelos de `RunAsync` serviço (apátridas ou apátridas) basta pesquisar a implementação:

1. A chamada `ServiceEventSource.Current.ServiceMessage` para `RunAsync` o método mostra um exemplo de um traço ETW personalizado do código de aplicação.
2. No ficheiro **ServiceEventSource.cs,** encontrará uma sobrecarga `ServiceEventSource.ServiceMessage` para o método que deve ser utilizado para eventos de alta frequência devido a razões de desempenho.

Para projetos **criados** a partir dos modelos de ator (apátrida ou apátrida):

1. Abra o ficheiro **"ProjectName".cs** onde *o ProjectName* é o nome que escolheu para o seu projeto Visual Studio.  
2. Encontre o `ActorEventSource.Current.ActorMessage(this, "Doing Work");` código no método *DoWorkAsync.*  Este é um exemplo de um traço ETW personalizado escrito a partir do código de aplicação.  
3. No ficheiro **ActorEventSource.cs,** encontrará uma sobrecarga para o `ActorEventSource.ActorMessage` método que deve ser usado para eventos de alta frequência devido a razões de desempenho.

Depois de adicionar o rastreio personalizado da ETW ao seu código de serviço, pode construir, implementar e executar novamente a aplicação para ver o seu(s) evento no Visualizador de Eventos de Diagnóstico. Se depurar a aplicação com **F5,** o Visualizador de Eventos de Diagnóstico abrirá automaticamente.

## <a name="next-steps"></a>Passos seguintes
O mesmo código de rastreio que adicionou à sua aplicação acima para diagnósticos locais funcionará com ferramentas que pode usar para visualizar estes eventos ao executar a sua aplicação num cluster Azure. Confira estes artigos que discutem as diferentes opções para as ferramentas e descreva como pode configurar.

* [Como recolher registos com diagnósticos Azure](service-fabric-diagnostics-how-to-setup-wad.md)
* [Agregação e recolha de eventos usando eventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md)

