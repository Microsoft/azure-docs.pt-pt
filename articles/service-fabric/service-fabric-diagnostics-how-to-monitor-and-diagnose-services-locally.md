---
title: Aplicativos de tecido de serviço Debug Azure no Windows
description: Saiba como monitorizar e diagnosticar os seus serviços escritos utilizando o Microsoft Azure Service Fabric numa máquina de desenvolvimento local.
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 58319b47c78a85b4f06c2c834db20f6c42cc1939
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86247426"
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Monitorizar e diagnosticar serviços numa configuração de desenvolvimento do computador local
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
> 
> 

A monitorização, deteção, diagnóstico e resolução de problemas permitem que os serviços continuem com o mínimo de perturbação da experiência do utilizador. Embora a monitorização e os diagnósticos sejam fundamentais num ambiente de produção implantado real, a eficiência dependerá da adoção de um modelo semelhante durante o desenvolvimento de serviços para garantir que funcionam quando se muda para uma configuração real. O Service Fabric facilita a implementação de diagnósticos que podem funcionar perfeitamente tanto em configurações de desenvolvimento local de máquina única como em configurações de clusters de produção no mundo real.

## <a name="event-tracing-for-windows"></a>Rastreio de eventos para windows
[Event Tracing for Windows](/windows/win32/etw/event-tracing-portal) (ETW) é a tecnologia recomendada para rastrear mensagens em Tecido de Serviço. Alguns benefícios da utilização da ETW são:

* **ETW é rápido.** Foi construído como uma tecnologia de rastreio que tem um impacto mínimo nos tempos de execução de códigos.
* **O rastreio da ETW funciona perfeitamente em ambientes de desenvolvimento local e também em configurações de clusters do mundo real.** Isto significa que não tens de reescrever o teu código de rastreio quando estás pronto para implantar o teu código num aglomerado real.
* **O código do sistema de tecido de serviço também utiliza a ETW para rastreio interno.** Isto permite-lhe visualizar os vestígios da sua aplicação intercalados com vestígios do sistema Service Fabric. Também o ajuda a entender mais facilmente as sequências e interrações entre o seu código de aplicação e eventos no sistema subjacente.
* **Existe suporte incorporado nas ferramentas do Service Fabric Visual Studio para visualizar eventos DA ETW.** Os eventos ETW aparecem na visão de Eventos de Diagnóstico do Estúdio Visual uma vez que o Visual Studio está corretamente configurado com o Service Fabric. 

## <a name="view-service-fabric-system-events-in-visual-studio"></a>Ver eventos do sistema de tecido de serviço em Estúdio Visual
A Service Fabric emite eventos DATW para ajudar os desenvolvedores de aplicações a entender o que está a acontecer na plataforma. Se ainda não o fez, siga os passos na Criação da [sua primeira aplicação no Visual Studio.](service-fabric-tutorial-create-dotnet-app.md) Estas informações irão ajudá-lo a obter uma aplicação em funcionamento com o Visualizador de Eventos de Diagnóstico mostrando as mensagens de rastreio.

1. Se a janela de eventos de diagnóstico não aparecer automaticamente, vá ao separador **Ver** no Estúdio Visual, escolha **Outros Windows** e, em seguida, **Visualização de Eventos de Diagnóstico**.
2. Cada evento tem informações padrão de metadados que lhe dizem o nó, aplicação e serviço de onde o evento está vindo. Também pode filtrar a lista de eventos utilizando a caixa de **eventos Filter** no topo da janela de eventos. Por exemplo, pode filtrar **o nome do nó** ou o nome de **serviço.** E quando estiver a analisar os detalhes do evento, também pode parar usando o botão **Pausa** no topo da janela de eventos e retomar mais tarde sem qualquer perda de eventos.
   
   ![Visualizador de eventos de diagnóstico de estúdio visual](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

## <a name="add-your-own-custom-traces-to-the-application-code"></a>Adicione os seus próprios traços personalizados ao código de aplicação
Os modelos de projeto do Service Fabric Visual Studio contêm código de amostra. O código mostra como adicionar traços de código de aplicação personalizados ETW que aparecem no visualizador EtW do Estúdio Visual ao lado de vestígios do sistema do Service Fabric. A vantagem deste método é que os metadados são automaticamente adicionados a vestígios, e o Visualizador de Eventos de Diagnóstico do Estúdio Visual já está configurado para os exibir.

Para projetos **criados** a partir dos modelos de serviço (apátridas ou stateful) basta procurar a `RunAsync` implementação:

1. A chamada para `ServiceEventSource.Current.ServiceMessage` o método mostra um exemplo de um traço `RunAsync` ETW personalizado do código de aplicação.
2. No ficheiro **ServiceEventSource.cs,** encontrará uma sobrecarga para o `ServiceEventSource.ServiceMessage` método que deve ser utilizado para eventos de alta frequência devido a razões de desempenho.

Para projetos **criados** a partir dos modelos de ator (apátridas ou stateful):

1. Abra o ficheiro **"ProjectName" .cs** onde o *ProjectName* é o nome que escolheu para o seu projeto de Estúdio Visual.  
2. Encontre o código `ActorEventSource.Current.ActorMessage(this, "Doing Work");` no método *DoWorkAsync.*  Este é um exemplo de um traço ETW personalizado escrito a partir do código de aplicação.  
3. No ficheiro **ActorEventSource.cs,** encontrará uma sobrecarga para o `ActorEventSource.ActorMessage` método que deve ser usado para eventos de alta frequência devido a razões de desempenho.

Depois de adicionar rastreios ETW personalizados ao seu código de serviço, pode construir, implementar e executar novamente a aplicação para ver o(s) do seu(s) eventos no(s) Visualização de Eventos de Diagnóstico. Se depurar a aplicação com **F5,** o Visualizador de Eventos de Diagnóstico abrir-se-á automaticamente.

## <a name="next-steps"></a>Passos seguintes
O mesmo código de rastreio que adicionou à sua aplicação acima para diagnósticos locais funcionará com ferramentas que pode usar para visualizar estes eventos ao executar a sua aplicação num cluster Azure. Confira estes artigos que discutem as diferentes opções para as ferramentas e descreva como pode configurar as ferramentas.

* [Como recolher registos com diagnósticos Azure](./service-fabric-diagnostics-event-aggregation-wad.md)
* [Agregação e recolha de eventos usando EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md)
