---
title: Especificar configurações de métricas e posicionamento
description: Saiba como descrever um serviço de Service Fabric especificando métricas, restrições de posicionamento e outras políticas de posicionamento.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: d4dcd319000edb204ba188ed14b4c797dba5cd38
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610102"
---
# <a name="configuring-cluster-resource-manager-settings-for-service-fabric-services"></a>Definindo as configurações do Gerenciador de recursos de cluster para serviços de Service Fabric
O Service Fabric cluster Resource Manager permite um controle refinado sobre as regras que regem cada serviço nomeado individual. Cada serviço nomeado pode especificar regras de como deve ser alocado no cluster. Cada serviço nomeado também pode definir o conjunto de métricas que deseja relatar, incluindo a importância deles para esse serviço. A configuração de serviços divide-se em três tarefas diferentes:

1. Configurando restrições de posicionamento
2. Configurando métricas
3. Configurando políticas avançadas de posicionamento e outras regras (menos comuns)

## <a name="placement-constraints"></a>Restrições de posicionamento
As restrições de posicionamento são usadas para controlar em quais nós no cluster um serviço pode realmente ser executado. Normalmente, uma instância de serviço nomeada específica ou todos os serviços de um determinado tipo restrito para execução em um determinado tipo de nó. As restrições de posicionamento são extensíveis. Você pode definir qualquer conjunto de propriedades por tipo de nó e, em seguida, selecionar para elas com restrições ao criar serviços. Você também pode alterar as restrições de posicionamento de um serviço enquanto ele está em execução. Isso permite que você responda às alterações no cluster ou aos requisitos do serviço. As propriedades de um determinado nó também podem ser atualizadas dinamicamente no cluster. Mais informações sobre restrições de posicionamento e como configurá-las podem ser encontradas neste [artigo](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

## <a name="metrics"></a>Métricas
Métricas são o conjunto de recursos que um determinado serviço nomeado precisa. A configuração de métrica de um serviço inclui o quanto desse recurso cada réplica com estado ou instância sem estado desse serviço consome por padrão. As métricas também incluem um peso que indica a importância que o balanceamento dessa métrica é para esse serviço, caso as compensações sejam necessárias.

## <a name="advanced-placement-rules"></a>Regras avançadas de posicionamento
Há outros tipos de regras de posicionamento que são úteis em cenários menos comuns. Alguns exemplos incluem:
- Restrições que ajudam com clusters distribuídos geograficamente
- Determinadas arquiteturas de aplicativo

Outras regras de posicionamento são configuradas por meio de correlações ou políticas.

## <a name="next-steps"></a>Passos seguintes
- As métricas são como o Gerenciador de recursos de Cluster Service Fabric gerencia o consumo e a capacidade no cluster. Para saber mais sobre as métricas e como configurá-las, confira [Este artigo](service-fabric-cluster-resource-manager-metrics.md)
- A afinidade é um modo que você pode configurar para seus serviços. Não é comum, mas se você precisar dele, poderá aprender sobre ele [aqui](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
- Há muitas regras de posicionamento diferentes que podem ser configuradas em seu serviço para lidar com cenários adicionais. Você pode descobrir sobre essas políticas de posicionamento diferentes [aqui](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
- Comece desde o início e [obtenha uma introdução ao Gerenciador de recursos de Cluster Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
- Para saber como o Gerenciador de recursos de cluster gerencia e equilibra a carga no cluster, confira o artigo sobre [balanceamento](service-fabric-cluster-resource-manager-balancing.md) de carga
- O Gerenciador de recursos de cluster tem muitas opções para descrever o cluster. Para saber mais sobre eles, confira este artigo sobre como [descrever um cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
