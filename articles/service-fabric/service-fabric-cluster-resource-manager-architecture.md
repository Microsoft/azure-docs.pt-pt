---
title: Arquitetura gestora de recursos
description: Uma visão geral e informação arquitetónica sobre o serviço azure service Fabric Cluster Resource Manager.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 94ed906533d108081d620e9b183ecfee249d85ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551697"
---
# <a name="cluster-resource-manager-architecture-overview"></a>Visão geral da arquitetura do gestor de recursos cluster
O Gestor de Recursos de Cluster de Tecidos de Serviço é um serviço central que funciona no cluster. Gere o estado desejado dos serviços no cluster, nomeadamente no que diz respeito ao consumo de recursos e a quaisquer regras de colocação. 

Para gerir os recursos do seu cluster, o Gestor de Recursos de Cluster de Tecidos de Serviço deve ter várias informações:

- Quais os serviços atualmente existentes
- O consumo atual (ou padrão) de recursos de cada serviço 
- A capacidade restante do cluster 
- A capacidade dos nós no aglomerado 
- A quantidade de recursos consumidos em cada nó

O consumo de recursos de um determinado serviço pode mudar ao longo do tempo, e os serviços geralmente se preocupam com mais de um tipo de recurso. Através de diferentes serviços, pode haver recursos físicos e físicos reais a ser medidos. Os serviços podem rastrear métricas físicas como a memória e o consumo de disco. Mais frequentemente, os serviços podem preocupar-se com métricas lógicas - coisas como "WorkQueueDepth" ou "TotalRequests". As métricas lógicas e físicas podem ser usadas no mesmo aglomerado. As métricas podem ser partilhadas em muitos serviços ou ser específicas de um determinado serviço.

## <a name="other-considerations"></a>Outras considerações
Os proprietários e operadores do cluster podem ser diferentes dos autores de serviços e aplicações, ou no mínimo são as mesmas pessoas que usam chapéus diferentes. Quando desenvolves a tua aplicação, sabes algumas coisas sobre o que ela requer. Tem uma estimativa dos recursos que irá consumir e de como devem ser implantados diferentes serviços. Por exemplo, o nível web precisa de ser executado em nós expostos à Internet, enquanto os serviços de base de dados não devem. Como outro exemplo, os serviços web são provavelmente limitados pela CPU e pela rede, enquanto os serviços de nível de dados se preocupam mais com o consumo de memória e disco. No entanto, a pessoa que está a lidar com um incidente no local ao vivo para esse serviço em produção, ou que está a gerir uma atualização para o serviço tem um trabalho diferente a fazer, e requer diferentes ferramentas. 

Tanto o cluster como os serviços são dinâmicos:

- O número de nós no cluster pode crescer e diminuir
- Nós de diferentes tamanhos e tipos podem ir e vir
- Os serviços podem ser criados, removidos e alterar as suas desejadas alocações de recursos e regras de colocação
- Upgrades ou outras operações de gestão podem passar pelo cluster na aplicação a níveis de infraestrutura
- Falhas podem acontecer a qualquer momento.

## <a name="cluster-resource-manager-components-and-data-flow"></a>Componentes do gestor de recursos de cluster e fluxo de dados
O Cluster Resource Manager tem de acompanhar os requisitos de cada serviço e o consumo de recursos por cada objeto de serviço dentro desses serviços. O Cluster Resource Manager tem duas partes conceptuais: agentes que funcionam em cada nó e um serviço tolerante a falhas. Os agentes de cada nó rastreiam relatórios de carga dos serviços, agregam-nos e reportam-nos periodicamente. O serviço Cluster Resource Manager agrega toda a informação dos agentes locais e reage com base na sua configuração atual.

Vejamos o seguinte diagrama:

<center>

![Arquitetura de Equilíbrio de Recursos][Image1]
</center>

Durante o tempo de execução, há muitas mudanças que podem acontecer. Por exemplo, digamos que a quantidade de recursos que alguns serviços consomem alterações, alguns serviços falham, e alguns nós se juntam e saem do cluster. Todas as alterações num nó são agregadas e periodicamente enviadas para o serviço cluster Resource Manager (1,2) onde são agregadas novamente, analisadas e armazenadas. A cada poucos segundos esse serviço olha para as alterações e determina se são necessárias quaisquer ações (3). Por exemplo, poderia notar que alguns nós vazios foram adicionados ao aglomerado. Como resultado, decide transferir alguns serviços para esses nós. O Gestor de Recursos cluster também poderia notar que um nó específico está sobrecarregado, ou que certos serviços falharam ou foram eliminados, libertando recursos em outros lugares.

Vamos ver o seguinte diagrama e ver o que acontece a seguir. Digamos que o Gestor de Recursos de Cluster determina que as mudanças são necessárias. Coordena com outros serviços do sistema (em particular o Failover Manager) para efazer as alterações necessárias. Em seguida, os comandos necessários são enviados para os nódosos apropriados (4). Por exemplo, digamos que o Gestor de Recursos reparou que o Node5 estava sobrecarregado, e por isso decidiu mudar o serviço B do Nó 5 para o Node4. No final da reconfiguração (5), o cluster é assim:

<center>

![Arquitetura de Equilíbrio de Recursos][Image2]
</center>

## <a name="next-steps"></a>Passos seguintes
- O Cluster Resource Manager tem muitas opções para descrever o cluster. Para saber mais sobre eles, confira este artigo sobre [a descrição de um cluster de Tecido de Serviço](./service-fabric-cluster-resource-manager-cluster-description.md)
- Os deveres primários do Gestor de Recursos cluster estão a reequilibrar o cluster e a impor as regras de colocação. Para obter mais informações sobre a configuração destes comportamentos, consulte [o equilíbrio do seu cluster De Serviço Tecido](./service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png
