---
title: Especificar definições de métricas e posicionamento no Azure Service Fabric | Documentos da Microsoft
description: Saiba como descrever um serviço do Service Fabric com a especificação de métricas, restrições de posicionamento e outras políticas de colocação.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 16e135c1-a00a-4c6f-9302-6651a090571a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 55d75bb0bae4bf3feb370b7fdf4d80dc43bdc0ca
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56736894"
---
# <a name="configuring-cluster-resource-manager-settings-for-service-fabric-services"></a>Configurar definições de Gestor de recursos de cluster para serviços do Service Fabric
O Gestor de recursos de Cluster do Service Fabric permite que um controle refinado sobre as regras que regem a cada indivíduo a chamada de serviço. Cada serviço com nome, pode especificar regras para a forma como deve ser alocado no cluster. Cada serviço com nome também pode definir o conjunto de métricas que quer para o relatório, incluindo como eles são importantes para esse serviço. Configurar os serviços divide em três tarefas diferentes:

1. Configurar restrições de posicionamento
2. Configurar métricas
3. Configurar políticas de colocação avançada e de outras regras (menos comum)

## <a name="placement-constraints"></a>Restrições de posicionamento
Restrições de posicionamento são utilizadas para controlar que nós no cluster de um serviço, na verdade, podem executar no. Normalmente, um determinado denominado instância de serviço ou de todos os serviços de um determinado tipo restringida para serem executadas num determinado tipo de nó. Restrições de posicionamento são extensíveis. Pode definir qualquer conjunto de propriedades por tipo de nó e, em seguida, selecione para os mesmos com restrições durante a criação de serviços. Também pode alterar as restrições de posicionamento de um serviço enquanto estiver em execução. Isto permite-lhe responder a alterações no cluster ou os requisitos do serviço. Também é possível atualizar as propriedades de um determinado nó dinamicamente no cluster. Podem encontrar mais informações sobre restrições de posicionamento e como configurá-las no [neste artigo](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

## <a name="metrics"></a>Métricas
As métricas são o conjunto de recursos que precisa de um determinado serviço com nome. Configuração da métrica de um serviço inclui quanto desse recurso cada réplica com monitoração de estado ou de uma instância desse serviço sem estado consome por predefinição. Métricas incluem também um peso que indica qual a importância balanceamento essa métrica para esse serviço, caso as compensações são necessárias.

## <a name="advanced-placement-rules"></a>Regras de colocação avançada
Existem outros tipos de regras de colocação, que são úteis em cenários menos comuns. Alguns exemplos incluem:
- Restrições que ajudam na clusters distribuídos geograficamente
- Determinadas arquiteturas de aplicações

Outras regras de colocação são configuradas por meio de correlações ou políticas.

## <a name="next-steps"></a>Passos Seguintes
- As métricas são como o Service Fabric Cluster Resource Manager gere o consumo e a capacidade do cluster. Para saber mais sobre métricas e como configurá-las, confira [neste artigo](service-fabric-cluster-resource-manager-metrics.md)
- A afinidade é um modo que pode configurar para os seus serviços. Não é comum, mas se precisar de saber sobre ele [aqui](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
- Existem muitas regras de colocação diferentes que podem ser configuradas no seu serviço para manipular os cenários adicionais. Pode obter informações sobre essas políticas de colocação diferentes [aqui](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
- Começar do início e [obtenha uma introdução para o Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
- Para obter informações sobre como o Gestor de recursos de Cluster gere e faz o balanceamento de carga no cluster, consulte o artigo no [balanceamento de carga](service-fabric-cluster-resource-manager-balancing.md)
- O Gestor de recursos do Cluster tem muitas opções para descrever o cluster. Para obter mais informações sobre ele, visite este artigo no [descrever um cluster do Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
