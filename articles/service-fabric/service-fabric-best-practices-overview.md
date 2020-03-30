---
title: Aplicação azure service fabric e boas práticas de cluster
description: Boas práticas e considerações de design para a gestão de clusters, apps e serviços usando o Tecido de Serviço Azure.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: pepogors
ms.openlocfilehash: 46601fd91fccb5bd0866bd999cc7643c37c6d582
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551782"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Aplicação azure service fabric e boas práticas de cluster

Este artigo fornece ligações às melhores práticas para a gestão de aplicações e clusters azure Service Fabric. Recomendamos vivamente que implemente estas práticas para otimizar a fiabilidade do seu ambiente de produção. Utilize um dos [modelos](https://github.com/Azure-Samples/service-fabric-cluster-templates) de cluster service Fabric para começar a desenhar a sua solução de produção, ou atualizar o seu modelo existente para incorporar estas práticas.

## <a name="security"></a>Segurança

* [Boas práticas para a segurança](service-fabric-best-practices-security.md)

## <a name="networking"></a>Redes

* [Boas práticas para networking](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>Planeamento e dimensionamento de computação

* [Boas práticas para a escala de cálculo](service-fabric-best-practices-capacity-scaling.md)
* [Planeamento da capacidade da computação](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)

## <a name="infrastructure-as-code"></a>Infraestrutura como código

* [Boas práticas para a implementação de infraestruturas como código](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>Monitorização e diagnóstico

* [Boas práticas para monitorização e diagnóstico de clusters](service-fabric-best-practices-monitoring.md)

## <a name="application-design"></a>Design da aplicação

* [Boas práticas para design de aplicações](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>Lista de Verificação

Depois de implementar as práticas sugeridas nas secções anteriores, certifique-se de que integrou todas as melhores práticas na lista de verificação de prontidão de produção:
* [Lista de verificação de prontidão de produção de tecido de serviço Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>Passos seguintes

* Criar um cluster em VMs ou computadores que executam o Windows Server: [Criação](service-fabric-cluster-creation-for-windows-server.md) de cluster de tecido de serviço para Windows Server
* Criar um cluster em VMs ou computadores em execução linux: [Criar um cluster Linux](service-fabric-cluster-creation-via-portal.md)
* Tecido de serviço de resolução de [problemas: guias de resolução de problemas](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)