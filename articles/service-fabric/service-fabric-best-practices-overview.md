---
title: Aplicação de tecido de serviço Azure e boas práticas de cluster
description: Boas práticas e considerações de design para gerir clusters, apps e serviços usando o Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: pepogors
ms.openlocfilehash: 86a02fd489ca0eec61b798db7136f963277f6c82
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86261085"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Aplicação de tecido de serviço Azure e boas práticas de cluster

Este artigo fornece links para as melhores práticas para a gestão de aplicações e clusters da Azure Service Fabric. Recomendamos vivamente que implemente estas práticas para otimizar a fiabilidade do seu ambiente de produção. Utilize um dos [modelos](https://github.com/Azure-Samples/service-fabric-cluster-templates) de cluster de tecido de serviço para começar a desenhar a sua solução de produção ou atualizar o seu modelo existente para incorporar estas práticas.

## <a name="security"></a>Segurança

* [Melhores práticas para a segurança](service-fabric-best-practices-security.md)

## <a name="networking"></a>Redes

* [Melhores práticas para networking](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>Planeamento e dimensionamento de computação

* [Melhores práticas para a escala de computação](service-fabric-best-practices-capacity-scaling.md)
* [Planeamento da capacidade de computação](./service-fabric-cluster-capacity.md)

## <a name="infrastructure-as-code"></a>Infraestrutura como código

* [Melhores práticas para implementar a infraestrutura como código](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>Monitorização e diagnóstico

* [Melhores práticas para monitorização e diagnóstico de clusters](service-fabric-best-practices-monitoring.md)

## <a name="application-design"></a>Design da aplicação

* [Melhores práticas para design de aplicações](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>Lista de Verificação

Depois de implementar as práticas sugeridas nas secções anteriores, certifique-se de que integrou todas as melhores práticas na lista de verificação de prontidão de produção:
* [Lista de verificação de prontidão de produção de tecido de serviço Azure](./service-fabric-production-readiness-checklist.md)

## <a name="next-steps"></a>Passos seguintes

* Criar um cluster em VMs ou computadores que executam o Windows Server: [Criação de cluster de tecido de serviço para windows server](service-fabric-cluster-creation-for-windows-server.md)
* Criar um cluster em VMs ou computadores que executam o Linux: [Criar um cluster Linux](service-fabric-cluster-creation-via-portal.md)
* Tecido de serviço de resolução [de problemas: guias de resolução de problemas](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
