---
title: Especificar métricas e configurações de colocação
description: Aprenda a descrever um serviço de Tecido de Serviço especificando métricas, restrições de colocação e outras políticas de colocação.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: d4dcd319000edb204ba188ed14b4c797dba5cd38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610102"
---
# <a name="configuring-cluster-resource-manager-settings-for-service-fabric-services"></a>Configurar as definições do gestor de recursos de cluster para serviços de tecido de serviço
O Gestor de Recursos de Cluster de Tecidos de Serviço permite um controlo fino sobre as regras que regem cada serviço individual. Cada serviço nomeado pode especificar regras para a forma como deve ser atribuído no cluster. Cada serviço nomeado também pode definir o conjunto de métricas que quer reportar, incluindo a importância que eles têm para esse serviço. A configuração dos serviços divide-se em três tarefas diferentes:

1. Configurar os constrangimentos de colocação
2. Configuração de métricas
3. Configurar políticas avançadas de colocação e outras regras (menos comuns)

## <a name="placement-constraints"></a>Restrições de colocação
Os constrangimentos de colocação são usados para controlar quais os nós do cluster em que um serviço pode realmente funcionar. Tipicamente uma instância de serviço nomeada em particular ou todos os serviços de um dado tipo limitado a funcionar em um determinado tipo de nó. Os constrangimentos de colocação são extensíveis. Pode definir qualquer conjunto de propriedades por tipo de nó e, em seguida, selecione para eles com constrangimentos ao criar serviços. Também pode alterar as restrições de colocação de um serviço enquanto estiver em funcionamento. Isto permite-lhe responder a alterações no cluster ou aos requisitos do serviço. As propriedades de um dado nó também podem ser atualizadas dinamicamente no cluster. Mais informações sobre os constrangimentos de colocação e como configurá-los podem ser encontrados [neste artigo](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

## <a name="metrics"></a>Métricas
As métricas são o conjunto de recursos que um determinado serviço nomeado precisa. A configuração métrica de um serviço inclui quanto desse recurso cada réplica apátrida ou instância apátrida desse serviço consome por padrão. As métricas também incluem um peso que indica a importância do equilíbrio dessa métrica para esse serviço, caso sejam necessárias compensações.

## <a name="advanced-placement-rules"></a>Regras avançadas de colocação
Existem outros tipos de regras de colocação que são úteis em cenários menos comuns. Alguns exemplos incluem:
- Constrangimentos que ajudam com aglomerados geograficamente distribuídos
- Certas arquiteturas de aplicação

Outras regras de colocação são configuradas através de Correlações ou Políticas.

## <a name="next-steps"></a>Passos seguintes
- As métricas são como o Manjedoura de Recursos de Cluster de Tecido de Serviço gere o consumo e a capacidade no cluster. Para saber mais sobre métricas e como configurá-las, confira [este artigo](service-fabric-cluster-resource-manager-metrics.md)
- A affinity é um modo que pode configurar para os seus serviços. Não é comum, mas se precisar, pode aprender [aqui.](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
- Existem muitas regras de colocação diferentes que podem ser configuradas no seu serviço para lidar com cenários adicionais. Você pode descobrir sobre essas diferentes políticas de colocação [aqui](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
- Comece do início e obtenha uma introdução ao Gestor de Recursos de Cluster de [Tecidos de Serviço](service-fabric-cluster-resource-manager-introduction.md)
- Para saber como o Cluster Resource Manager gere e equilibra a carga no cluster, confira o artigo sobre [a carga](service-fabric-cluster-resource-manager-balancing.md) de equilíbrio
- O Cluster Resource Manager tem muitas opções para descrever o cluster. Para saber mais sobre eles, confira este artigo sobre [a descrição de um cluster de Tecido de Serviço](service-fabric-cluster-resource-manager-cluster-description.md)
