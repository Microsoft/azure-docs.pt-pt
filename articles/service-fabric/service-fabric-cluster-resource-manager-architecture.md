---
title: Arquitetura do Resource Manager | Documentos da Microsoft
description: Uma visão geral da arquitetura do Service Fabric Cluster Resource Manager.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 6c4421f9-834b-450c-939f-1cb4ff456b9b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: b39f7bc31ed286ef4a894e9d49166cd305d9e905
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56736758"
---
# <a name="cluster-resource-manager-architecture-overview"></a>Descrição geral de arquitetura do resource manager de cluster
O Gestor de recursos de Cluster do Service Fabric é um serviço central que é executado no cluster. Gere o estado pretendido dos serviços do cluster, especialmente em relação ao consumo de recursos e quaisquer regras de colocação. 

Para gerir os recursos no seu cluster, o Gestor de recursos de Cluster do Service Fabric tem de ter várias partes de informações:

- Quais os serviços atualmente existe
- Cada serviço do atual (ou predefinido) o consumo de recursos 
- A capacidade restante do cluster 
- A capacidade de nós do cluster 
- A quantidade de recursos consumidos em cada nó

O consumo de recursos de um determinado serviço pode mudar ao longo do tempo e os serviços normalmente se preocupa com mais de um tipo de recurso. Em serviços diferentes, pode ser tanto real recursos físicos e físicos a ser medidos. Serviços podem controlar métricas físicas, como o consumo de memória e disco. Mais comum, os serviços podem cuidado sobre métricas da lógicas – coisas como "WorkQueueDepth" ou "TotalRequests". Métricas de lógicas e físicas podem ser utilizadas no mesmo cluster. As métricas podem ser partilhadas entre vários serviços, ou seja específico para um determinado serviço.

## <a name="other-considerations"></a>Outras considerações
Os proprietários e operadores do cluster podem ser diferentes dos autores de serviços e de aplicações ou, no mínimo são os mesmo chapéus diferentes wearing pessoas. Ao desenvolver seu aplicativo sabe algo sobre o que requer. Tem uma estimativa dos recursos que irão consumir e como devem ser implementados diferentes serviços. Por exemplo, a camada web tem de ser executado em nós exposto à Internet, enquanto os serviços de base de dados não devem. Como outro exemplo, os serviços web provavelmente estão limitados por CPU e da rede, enquanto o cuidado de serviços de camada de dados mais informações sobre o consumo de memória e disco. No entanto, a pessoa que lidar com incidentes de um site ativo para esse serviço em produção, ou que está a gerir uma atualização para o serviço tem um trabalho diferente para o fazer e requerer ferramentas distintas. 

O cluster e os serviços são dinâmicos:

- O número de nós do cluster pode aumentar e diminuir
- Nós de diferentes tamanhos e tipos podem voltar e ir
- Os serviços podem ser criados, removido e alterar as respetivas alocações de recursos desejados e regras de colocação
- As atualizações ou de outras operações de gestão podem distribuir por meio de cluster em que o aplicativo em níveis da infra-estrutura
- Falhas podem ocorrer a qualquer momento.

## <a name="cluster-resource-manager-components-and-data-flow"></a>Componentes de Gestor de recursos de cluster e o fluxo de dados
O Gestor de recursos do Cluster tem que controlar os requisitos de cada serviço e o consumo de recursos por cada objeto de serviço dentro desses serviços. O Gestor de recursos do Cluster tem duas partes de conceituais: agentes que são executadas em cada nó e um serviço tolerante a falhas. Os agentes em cada nó de carga de controlar relatórios a partir de serviços, agregação-los e periodicamente relatá-los. O serviço de Gestor de recursos de Cluster agrega todas as informações de agentes locais e reage com base na configuração atual.

Vamos examinar o diagrama seguinte:

<center>
![Arquitetura do Balanceador de recursos][Image1]
</center>

Durante o tempo de execução, há muitas alterações que poderiam acontecer. Por exemplo, vamos dizer a quantidade de recursos que consumam de alguns serviços for alterado, alguns serviços falham e alguns nós associação e o cluster. Todas as alterações num nó são agregadas e enviadas periodicamente para o serviço de Gestor de recursos de Cluster (1,2) onde estão novamente agregados, analisados e armazenados. Cada alguns segundos, que analisa as alterações de serviço e determina se todas as ações são necessárias (3). Por exemplo, poderia observe que alguns nós vazios foram adicionados ao cluster. Como resultado, ele decide mover alguns serviços para esses nós. O Gestor de recursos de Cluster também pode observar que um determinado nó está sobrecarregado ou que determinados serviços tenham falhou ou foi eliminados, liberando recursos em outro lugar.

Vamos examinar o diagrama seguinte e ver o que acontece em seguida. Digamos que o Gestor de recursos de Cluster determina que as alterações são necessárias. Coordena com outros serviços do sistema (em especial o Gestor de ativação pós-falha) para fazer as alterações necessárias. Em seguida, os comandos necessários são enviados para os nós adequados (4). Por exemplo, digamos que o Gestor de recursos observado que o Nó5 foi sobrecarregado e, então Decidi mover o serviço B de Nó5 para Nó4. No final da reconfiguração (5), o cluster fica assim:

<center>
![Arquitetura do Balanceador de recursos][Image2]
</center>

## <a name="next-steps"></a>Passos Seguintes
- O Gestor de recursos do Cluster tem muitas opções para descrever o cluster. Para obter mais informações sobre ele, visite este artigo no [descrever um cluster do Service Fabric](./service-fabric-cluster-resource-manager-cluster-description.md)
- Taxas de principal do Gestor de recursos de Cluster são reequilibrar o cluster e a imposição de regras de colocação. Para obter mais informações sobre como configurar esses comportamentos, consulte [balanceamento de cluster do Service Fabric](./service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png
