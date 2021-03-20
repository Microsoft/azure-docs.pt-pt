---
title: Arquitetura do Gestor de Recursos
description: Uma visão geral e informação arquitetónica sobre o serviço Azure Service Fabric Cluster Resource Manager.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 0aff55810508fedcf354fba3d9fca9f7a402029b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94685839"
---
# <a name="cluster-resource-manager-architecture-overview"></a>Visão geral da arquitetura do gestor de recursos do cluster
O Service Fabric Cluster Resource Manager é um serviço central que funciona no cluster. Gere o estado desejado dos serviços no cluster, nomeadamente no que diz respeito ao consumo de recursos e quaisquer regras de colocação. 

Para gerir os recursos no seu cluster, o Gestor de Recursos do Cluster de Tecido de Serviço deve ter várias informações:

- Quais serviços existem atualmente
- Consumo atual (ou padrão) de recursos de cada serviço 
- A capacidade restante do cluster 
- A capacidade dos nóns no cluster 
- A quantidade de recursos consumidos em cada nó

O consumo de recursos de um determinado serviço pode mudar ao longo do tempo, e os serviços geralmente se preocupam com mais de um tipo de recurso. Em diferentes serviços, pode haver recursos físicos e físicos reais a serem medidos. Os serviços podem rastrear métricas físicas como a memória e o consumo de discos. Mais comummente, os serviços podem preocupar-se com métricas lógicas - coisas como "WorkQueueDepth" ou "TotalRequests". As métricas lógicas e físicas podem ser usadas no mesmo aglomerado. As métricas podem ser partilhadas em muitos serviços ou específicas de um determinado serviço.

## <a name="other-considerations"></a>Outras considerações
Os proprietários e operadores do cluster podem ser diferentes dos autores de serviços e aplicações, ou no mínimo são as mesmas pessoas que usam chapéus diferentes. Quando desenvolves a tua aplicação, sabes algumas coisas sobre o que ela requer. Tem uma estimativa dos recursos que irá consumir e de como os diferentes serviços devem ser implementados. Por exemplo, o nível web precisa de funcionar em nós expostos à Internet, enquanto os serviços de base de dados não devem. Como outro exemplo, os serviços web são provavelmente limitados pela CPU e rede, enquanto os serviços de nível de dados se preocupam mais com a memória e o consumo de discos. No entanto, a pessoa que está a lidar com um incidente no local para esse serviço em produção, ou que está a gerir uma atualização para o serviço tem um trabalho diferente a fazer, e requer diferentes ferramentas. 

Tanto o cluster como os serviços são dinâmicos:

- O número de nós no cluster pode crescer e encolher
- Nódes de diferentes tamanhos e tipos podem ir e vir
- Os serviços podem ser criados, removidos e alterar as suas alocações de recursos e regras de colocação
- Upgrades ou outras operações de gestão podem passar pelo cluster na aplicação a níveis de infraestrutura
- Falhas podem acontecer a qualquer momento.

## <a name="cluster-resource-manager-components-and-data-flow"></a>Componentes do gestor de recursos do cluster e fluxo de dados
O Cluster Resource Manager tem de acompanhar os requisitos de cada serviço e o consumo de recursos por cada objeto de serviço dentro desses serviços. O Cluster Resource Manager tem duas partes conceptuais: agentes que funcionam em cada nó e um serviço tolerante a falhas. Os agentes em cada nó rastreiam relatórios de carga de serviços, agregando-os, e reportá-los periodicamente. O serviço Cluster Resource Manager agrega toda a informação dos agentes locais e reage com base na sua configuração atual.

Vejamos o seguinte diagrama:

<center>

![Diagrama que mostra o serviço Cluster Resource Manager agrega toda a informação dos agentes locais e reage com base na sua configuração atual.][Image1]
</center>

Durante o tempo de funcionamento, há muitas mudanças que podem acontecer. Por exemplo, digamos a quantidade de recursos que alguns serviços consomem alterações, alguns serviços falham, e alguns nós juntam-se e saem do cluster. Todas as alterações num nó são agregadas e periodicamente enviadas para o serviço Cluster Resource Manager (1,2) onde são novamente agregadas, analisadas e armazenadas. A cada poucos segundos que o serviço olha para as alterações e determina se são necessárias algumas ações (3). Por exemplo, poderia notar que alguns nós vazios foram adicionados ao cluster. Como resultado, decide mover alguns serviços para esses nós. O Gestor de Recursos do Cluster também poderia notar que um nó particular está sobrecarregado, ou que certos serviços falharam ou foram eliminados, libertando recursos em outros lugares.

Vamos ver o seguinte diagrama e ver o que acontece a seguir. Digamos que o Gestor de Recursos do Cluster determina que as mudanças são necessárias. Coordena com outros serviços de sistema (em particular o Gestor de Failover) para fazer as alterações necessárias. Em seguida, os comandos necessários são enviados para os nóns apropriados (4). Por exemplo, digamos que o Gestor de Recursos reparou que o Node5 estava sobrecarregado, e por isso decidiu transferir o serviço B de Node5 para Node4. No final da reconfiguração (5), o cluster é assim:

<center>

![Arquitetura do Equilibrador de Recursos][Image2]
</center>

## <a name="next-steps"></a>Passos seguintes
- O Cluster Resource Manager tem muitas opções para descrever o cluster. Para saber mais sobre eles, confira este artigo sobre [a descrição de um cluster de Tecido de Serviço](./service-fabric-cluster-resource-manager-cluster-description.md)
- As funções primárias do Cluster Resource Manager são reequilibrar o cluster e impor as regras de colocação. Para obter mais informações sobre a configuração destes comportamentos, consulte [o equilíbrio do seu cluster de Tecido de Serviço](./service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png
