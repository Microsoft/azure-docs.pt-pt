---
title: Práticas recomendadas de aplicativo e Cluster Service Fabric do Azure
description: Práticas recomendadas e considerações de design para gerenciar clusters, aplicativos e serviços usando o Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: pepogors
ms.openlocfilehash: 46601fd91fccb5bd0866bd999cc7643c37c6d582
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551782"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Práticas recomendadas de aplicativo e Cluster Service Fabric do Azure

Este artigo fornece links para as práticas recomendadas para o gerenciamento de clusters e aplicativos do Azure Service Fabric. É altamente recomendável que você implemente essas práticas para otimizar a confiabilidade do seu ambiente de produção. Use um dos [modelos de cluster Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates) para começar a projetar sua solução de produção ou atualize seu modelo existente para incorporar essas práticas.

## <a name="security"></a>Segurança

* [Práticas recomendadas de segurança](service-fabric-best-practices-security.md)

## <a name="networking"></a>Funcionamento em Rede

* [Práticas recomendadas para rede](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>Planeamento e dimensionamento de computação

* [Práticas recomendadas para o dimensionamento de computação](service-fabric-best-practices-capacity-scaling.md)
* [Planejamento de capacidade de computação](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)

## <a name="infrastructure-as-code"></a>Infraestrutura como código

* [Práticas recomendadas para implementar a infraestrutura como código](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>Monitorização e diagnóstico

* [Práticas recomendadas para monitoramento e diagnóstico de cluster](service-fabric-best-practices-monitoring.md)

## <a name="application-design"></a>Design da aplicação

* [Práticas recomendadas para design de aplicativos](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>Lista de Verificação

Depois de implementar as práticas sugeridas nas seções anteriores, verifique se você integrou todas as práticas recomendadas na lista de verificação de preparação de produção:
* [Lista de verificação de preparação de produção do Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>Passos seguintes

* Criar um cluster em VMs ou computadores que executam o Windows Server: [Service Fabric a criação de cluster para o Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Criar um cluster em VMs ou computadores que executam [o Linux: criar um cluster do Linux](service-fabric-cluster-creation-via-portal.md)
* Solucionar problemas Service Fabric: [guias de solução de problemas](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)