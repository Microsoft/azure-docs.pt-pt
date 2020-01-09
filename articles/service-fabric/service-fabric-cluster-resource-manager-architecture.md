---
title: Arquitetura do Gerenciador de recursos
description: Uma visão geral do e informações arquitetônicas sobre o serviço do Gerenciador de recursos de Cluster Service Fabric do Azure.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 94ed906533d108081d620e9b183ecfee249d85ca
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551697"
---
# <a name="cluster-resource-manager-architecture-overview"></a>Visão geral da arquitetura do Gerenciador de recursos de cluster
O Gerenciador de recursos de Cluster Service Fabric é um serviço central que é executado no cluster. Ele gerencia o estado desejado dos serviços no cluster, particularmente em relação ao consumo de recursos e a qualquer regra de posicionamento. 

Para gerenciar os recursos em seu cluster, o Gerenciador de recursos de Cluster Service Fabric deve ter várias informações:

- Quais serviços existem atualmente
- Consumo de recursos atual (ou padrão) de cada serviço 
- A capacidade restante do cluster 
- A capacidade dos nós no cluster 
- A quantidade de recursos consumidos em cada nó

O consumo de recursos de um determinado serviço pode mudar ao longo do tempo, e os serviços geralmente se preocupam com mais de um tipo de recurso. Em diferentes serviços, pode haver recursos físicos e físicos reais sendo medidos. Os serviços podem rastrear métricas físicas, como consumo de memória e disco. Mais comumente, os serviços podem se preocupar com métricas lógicas – coisas como "WorkQueueDepth" ou "TotalRequests". As métricas lógica e física podem ser usadas no mesmo cluster. As métricas podem ser compartilhadas entre vários serviços ou ser específicas para um serviço específico.

## <a name="other-considerations"></a>Outras considerações
Os proprietários e os operadores do cluster podem ser diferentes dos autores do serviço e do aplicativo ou, no mínimo, são as mesmas pessoas que desgastam diferentes chapéus. Ao desenvolver seu aplicativo, você sabe algumas coisas sobre o que ele exige. Você tem uma estimativa dos recursos que ele consumirá e de como os serviços diferentes devem ser implantados. Por exemplo, a camada da Web precisa ser executada em nós expostos à Internet, enquanto os serviços de banco de dados não devem. Como outro exemplo, os serviços Web são provavelmente restritos pela CPU e pela rede, enquanto os serviços da camada de dados se preocupam mais com o consumo de memória e disco. No entanto, a pessoa que trata um incidente de site ativo para esse serviço em produção ou que está gerenciando uma atualização para o serviço tem um trabalho diferente a ser feito e requer ferramentas diferentes. 

O cluster e os serviços são dinâmicos:

- O número de nós no cluster pode crescer e reduzir
- Nós de diferentes tamanhos e tipos podem vir e ir
- Os serviços podem ser criados, removidos e alterar as alocações de recursos desejadas e as regras de posicionamento
- Atualizações ou outras operações de gerenciamento podem passar pelo cluster no aplicativo em níveis de infraestrutura
- As falhas podem ocorrer a qualquer momento.

## <a name="cluster-resource-manager-components-and-data-flow"></a>Componentes do Gerenciador de recursos de cluster e fluxo de dados
O Gerenciador de recursos de cluster precisa controlar os requisitos de cada serviço e o consumo de recursos por cada objeto de serviço dentro desses serviços. O Gerenciador de recursos de cluster tem duas partes conceituais: agentes que são executados em cada nó e um serviço tolerante a falhas. Os agentes em cada nó controlam a carga de relatórios de serviços, agregam-os e os relatam periodicamente. O serviço do Gerenciador de recursos de cluster agrega todas as informações dos agentes locais e reage com base em sua configuração atual.

Vamos examinar o diagrama a seguir:

<center>

![arquitetura do balanceador de recursos][Image1]
</center>

Durante o tempo de execução, há muitas alterações que podem acontecer. Por exemplo, digamos que a quantidade de recursos que alguns serviços consomem alteram, alguns serviços falham e alguns nós ingressam e deixam o cluster. Todas as alterações em um nó são agregadas e enviadas periodicamente para o serviço do Gerenciador de recursos de cluster (1, 2), em que são agregadas novamente, analisadas e armazenadas. A cada poucos segundos, o serviço examina as alterações e determina se as ações são necessárias (3). Por exemplo, ele pode observar que alguns nós vazios foram adicionados ao cluster. Como resultado, ele decide mover alguns serviços para esses nós. O Gerenciador de recursos de cluster também pode observar que um nó específico está sobrecarregado ou que determinados serviços falharam ou foram excluídos, liberando recursos em outro lugar.

Vamos examinar o diagrama a seguir e ver o que acontece em seguida. Digamos que o Gerenciador de recursos de cluster determine que as alterações são necessárias. Ele coordena com outros serviços do sistema (em particular a Gerenciador de Failover) para fazer as alterações necessárias. Em seguida, os comandos necessários são enviados para os nós apropriados (4). Por exemplo, digamos que o Gerenciador de recursos tenha notado que o Nó5 estava sobrecarregado e, portanto, decidiu mover o serviço B de Nó5 para Nó4. No final da reconfiguração (5), o cluster tem esta aparência:

<center>

![arquitetura do balanceador de recursos][Image2]
</center>

## <a name="next-steps"></a>Passos seguintes
- O Gerenciador de recursos de cluster tem muitas opções para descrever o cluster. Para saber mais sobre eles, confira este artigo sobre como [descrever um cluster Service Fabric](./service-fabric-cluster-resource-manager-cluster-description.md)
- As principais tarefas do Gerenciador de recursos de cluster estão reequilibrando o cluster e impondo regras de posicionamento. Para obter mais informações sobre como configurar esses comportamentos, consulte [balanceamento do cluster de Service Fabric](./service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png
