---
title: Aplicação de tecido de serviço Azure e boas práticas de cluster
description: Boas práticas e considerações de design para gerir clusters, apps e serviços usando o Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: pepogors
ms.openlocfilehash: 46601fd91fccb5bd0866bd999cc7643c37c6d582
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75551782"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Aplicação de tecido de serviço Azure e boas práticas de cluster

Este artigo fornece links para as melhores práticas para a gestão de aplicações e clusters da Azure Service Fabric. Recomendamos vivamente que implemente estas práticas para otimizar a fiabilidade do seu ambiente de produção. Utilize um dos [modelos](https://github.com/Azure-Samples/service-fabric-cluster-templates) de cluster de tecido de serviço para começar a desenhar a sua solução de produção ou atualizar o seu modelo existente para incorporar estas práticas.

## <a name="security"></a>Segurança

* [Melhores práticas para a segurança](service-fabric-best-practices-security.md)

## <a name="networking"></a>Redes

* [Melhores práticas para networking](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>Planeamento e dimensionamento de computação

* [Melhores práticas para a escala de computação](service-fabric-best-practices-capacity-scaling.md)
* [Planeamento da capacidade de computação](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)

## <a name="infrastructure-as-code"></a>Infraestrutura como código

* [Melhores práticas para implementar a infraestrutura como código](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>Monitorização e diagnóstico

* [Melhores práticas para monitorização e diagnóstico de clusters](service-fabric-best-practices-monitoring.md)

## <a name="application-design"></a>Design da aplicação

* [Melhores práticas para design de aplicações](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>Lista de Verificação

Depois de implementar as práticas sugeridas nas secções anteriores, certifique-se de que integrou todas as melhores práticas na lista de verificação de prontidão de produção:
* [Lista de verificação de prontidão de produção de tecido de serviço Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>Próximos passos

* Criar um cluster em VMs ou computadores que executam o Windows Server: [Criação de cluster de tecido de serviço para windows server](service-fabric-cluster-creation-for-windows-server.md)
* Criar um cluster em VMs ou computadores que executam o Linux: [Criar um cluster Linux](service-fabric-cluster-creation-via-portal.md)
* Tecido de serviço de resolução [de problemas: guias de resolução de problemas](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)