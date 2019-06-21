---
title: Melhores práticas de aplicação do Service Fabric e cluster do Azure | Documentos da Microsoft
description: Melhores práticas para gerir clusters do Service Fabric e aplicações.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/18/2019
ms.author: pepogors
ms.openlocfilehash: 5fdbd3f15b11e4c3975ca29627d5984382bcf049
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206792"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Melhores práticas de aplicação do Service Fabric do Azure e de cluster

Este artigo fornece ligações para as melhores práticas para a gestão de aplicações do Azure Service Fabric e clusters. É altamente recomendável que implementar essas práticas para otimizar a fiabilidade do seu ambiente de produção. Utilize um da [modelos de cluster do Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates) para começar a conceber a sua solução de produção ou atualizar o modelo existente para incorporar essas práticas.

## <a name="security"></a>Segurança

* [Práticas recomendadas para segurança](service-fabric-best-practices-security.md)

## <a name="networking"></a>Redes

* [Melhores práticas para funcionamento em rede](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>Planeamento e dimensionamento de computação

* [Melhores práticas de dimensionamento de computação](service-fabric-best-practices-capacity-scaling.md)
* [Planeamento da capacidade de computação](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)

## <a name="infrastructure-as-code"></a>Infraestrutura como código

* [Melhores práticas para implementar a infraestrutura como código](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>Monitorização e diagnóstico

* [Melhores práticas para monitorização de clusters e diagnóstico](service-fabric-best-practices-monitoring.md)

## <a name="application-design"></a>Design da aplicação

* [Melhores práticas para o design do aplicativo](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>Lista de verificação

Depois de implementar as práticas sugeridas nas secções anteriores, certifique-se de que tiver integrado a todas as melhores práticas da lista de verificação de preparação de produção:
* [O Azure Service Fabric produção preparação lista de verificação](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>Passos Seguintes

* Crie um cluster em VMs ou computadores que executam o Windows Server: [Criação do cluster do Service Fabric para o Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Crie um cluster em VMs ou computadores que executam o Linux: [Criar um cluster do Linux](service-fabric-cluster-creation-via-portal.md)
* Resolver problemas relacionados com o Service Fabric: [Guias de resolução de problemas](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)