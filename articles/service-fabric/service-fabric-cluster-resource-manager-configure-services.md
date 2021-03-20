---
title: Especificar métricas e configurações de colocação
description: Saiba como descrever um serviço de Tecido de Serviço especificando métricas, restrições de colocação e outras políticas de colocação.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: d4dcd319000edb204ba188ed14b4c797dba5cd38
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "75610102"
---
# <a name="configuring-cluster-resource-manager-settings-for-service-fabric-services"></a>Configurar definições de gestor de recursos de cluster para serviços de Tecido de Serviço
O Service Fabric Cluster Resource Manager permite um controlo fino sobre as regras que regem cada serviço nomeado individualmente. Cada serviço nomeado pode especificar regras de como deve ser alocado no cluster. Cada serviço nomeado também pode definir o conjunto de métricas que quer reportar, incluindo a sua importância para esse serviço. Os serviços de configuração dividem-se em três tarefas diferentes:

1. Configurar constrangimentos de colocação
2. Métricas de configuração
3. Configurar políticas avançadas de colocação e outras regras (menos comuns)

## <a name="placement-constraints"></a>Restrições de colocação
As restrições de colocação são usadas para controlar quais os nós no cluster que um serviço pode realmente funcionar. Tipicamente, uma instância de serviço nomeada ou todos os serviços de um determinado tipo limitado a funcionar em um determinado tipo de nó. As restrições de colocação são extensíveis. Pode definir qualquer conjunto de propriedades por tipo de nó e, em seguida, selecioná-las com restrições ao criar serviços. Também pode alterar as restrições de colocação de um serviço durante o seu funcionamento. Isto permite-lhe responder a alterações no cluster ou aos requisitos do serviço. As propriedades de um dado nó também podem ser atualizadas dinamicamente no cluster. Mais informações sobre restrições de colocação e como configurá-las podem ser encontradas [neste artigo](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

## <a name="metrics"></a>Métricas
As métricas são o conjunto de recursos que um determinado serviço precisa. A configuração métrica de um serviço inclui a quantidade desse recurso que cada réplica apátrida ou instância apátrida desse serviço consome por padrão. As métricas também incluem um peso que indica a importância do equilíbrio desta métrica para esse serviço, caso sejam necessárias trocas.

## <a name="advanced-placement-rules"></a>Regras avançadas de colocação
Existem outros tipos de regras de colocação que são úteis em cenários menos comuns. Alguns exemplos incluem:
- Constrangimentos que ajudam com clusters geograficamente distribuídos
- Certas arquiteturas de aplicações

Outras regras de colocação são configuradas através de correlações ou políticas.

## <a name="next-steps"></a>Passos seguintes
- As métricas são como a Manjedoura de Recursos do Cluster de Tecidos de Serviço gere o consumo e a capacidade no cluster. Para saber mais sobre métricas e como configurá-las, confira [este artigo](service-fabric-cluster-resource-manager-metrics.md)
- A affinity é um modo que pode configurar para os seus serviços. Não é comum, mas se precisar, pode aprender [aqui.](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
- Existem muitas regras de colocação diferentes que podem ser configuradas no seu serviço para lidar com cenários adicionais. Você pode descobrir sobre essas diferentes políticas de colocação [aqui](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
- Comece do início e [obtenha uma introdução ao Gestor](service-fabric-cluster-resource-manager-introduction.md) de Recursos do Cluster de Tecido de Serviço
- Para saber como o Cluster Resource Manager gere e equilibra a carga no cluster, confira o artigo sobre a [carga de equilíbrio](service-fabric-cluster-resource-manager-balancing.md)
- O Cluster Resource Manager tem muitas opções para descrever o cluster. Para saber mais sobre eles, confira este artigo sobre [a descrição de um cluster de Tecido de Serviço](service-fabric-cluster-resource-manager-cluster-description.md)
