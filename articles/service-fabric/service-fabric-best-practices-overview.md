---
title: Melhores práticas de aplicação do Service Fabric e cluster do Azure | Documentos da Microsoft
description: Melhores práticas para gerir clusters do Service Fabric e aplicações.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: jeanpaul.connock
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 06240ac08a12b67e95b4cb9b9a33fcca32de45a8
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/25/2019
ms.locfileid: "54914114"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Melhores práticas de aplicação do Service Fabric do Azure e de cluster

Para gerir clusters e aplicações do Azure Service Fabric com êxito, existem operações que é altamente recomendável que executar, para otimizar a fiabilidade do seu ambiente de produção; . executar operações definidas neste documento e selecione um dos nossos [modelos de Cluster do Service Fabric do Azure exemplos](https://github.com/Azure-Samples/service-fabric-cluster-templates) para começar a conceber a sua solução de produção ou modificar o modelo existente para incorporar essas práticas.

## <a name="security"></a>Segurança 

* [Práticas recomendadas para segurança](service-fabric-best-practices-security.md)

## <a name="networking"></a>Redes

* [Melhores práticas para funcionamento em rede](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>Planeamento de computação e dimensionamento

* [Melhores práticas de dimensionamento de computação](service-fabric-best-practices-capacity-scaling.md)
* [Planeamento da capacidade de computação](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)

## <a name="infrastructure-as-code"></a>Infraestrutura como código

* [Melhores práticas para implementar a infraestrutura como código](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>Monitorização e diagnóstico

* [Melhores práticas para monitorização de clusters e diagnóstico](service-fabric-best-practices-monitoring.md)

## <a name="checklist"></a>Lista de verificação

Depois de concluir todas as seções acima, certifique-se de que tiver integrado a todas as práticas recomendadas da lista de verificação de preparação de produção:
* [O serviço do Azure a lista de verificação de preparação de produção do Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>Passos Seguintes

* Crie um cluster em VMs ou computadores que executam o Windows Server: [Criação do cluster do Service Fabric para o Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Crie um cluster em VMs ou computadores que executam o Linux: [Criar um cluster do Linux](service-fabric-cluster-creation-via-portal.md)
* Resolução de problemas: [Guia de resolução de problemas do Service Fabric](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)