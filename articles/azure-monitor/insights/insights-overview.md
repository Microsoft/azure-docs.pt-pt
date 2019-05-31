---
title: Descrição geral do Insights no Azure Monitor | Documentos da Microsoft
description: Insights proporcionar uma experiência de monitorização personalizada no Azure Monitor para determinados aplicativos e serviços. Este artigo fornece uma breve descrição de cada uma das informações que estão atualmente disponíveis.
services: azure-monitor
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: bwren
ms.openlocfilehash: 8cb39a174c570b7019e872d731f49252a9505406
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247233"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Descrição geral do Insights no Azure Monitor
Insights proporcionar uma experiência de monitorização personalizada para determinados aplicativos e serviços. Eles armazenam dados no [plataforma de dados do Azure Monitor](../platform/data-platform.md) e tirar partido das outras funcionalidades do Azure Monitor para análise e alerta, mas pode recolher dados adicionais e fornecer uma experiência de utilizador exclusivo no portal do Azure. Aceder a informações a partir da **Insights** secção do menu do Azure Monitor no portal do Azure.

As secções seguintes fornecem uma breve descrição das informações que estão atualmente disponíveis no Azure Monitor. Consulte a documentação detalhada para obter detalhes sobre cada.

## <a name="application-insights"></a>Application Insights
O Application Insights é um serviço de Gestão de Desempenho de Aplicações (APM) extensível para programadores Web em várias plataformas. Utilize-o para monitorizar a sua aplicação Web online. Ele funciona para aplicativos numa grande variedade de plataformas, incluindo .NET, node. js e Java EE, alojado no local, híbrido ou qualquer cloud pública. Também se integra com o seu processo de DevOps e tem pontos de ligação a uma variedade de ferramentas de desenvolvimento.

Ver [o que é o Application Insights?](../app/app-insights-overview.md).

![Application Insights](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>Monitor do Azure para contentores
O Azure Monitor para monitores de contentores o desempenho de cargas de trabalho do contentor implementada em qualquer uma das instâncias de contentor do Azure ou geridos clusters do Kubernetes alojados no Azure Kubernetes Service (AKS). Os contentores é fundamental monitorizar, especialmente quando estiver a executar um cluster de produção em escala, com várias aplicações.

Ver [Monitor do Azure para contentores-descrição geral](../insights/container-insights-overview.md).

![Monitor do Azure para contentores](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>O Azure Monitor para grupos de recursos (pré-visualização)
O Azure Monitor para grupos de recursos ajuda a Triar e diagnosticar quaisquer problemas que encontram os recursos individuais, oferecendo contexto relativamente ao estado de funcionamento e o desempenho do grupo de recursos como um todo.

Ver [monitorizar grupos de recursos com o Azure Monitor (pré-visualização)](../insights/resource-group-insights.md).

![O Azure Monitor para grupos de recursos](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>Monitor do Azure para VMs (pré-visualização)
Monitor do Azure para VMs monitoriza máquinas de virtuais do Azure (VM) e conjuntos de dimensionamento de máquinas virtuais em escala. Analisa o desempenho e o estado de funcionamento das suas VMs do Windows e do Linux e monitoriza os respetivos processos e dependências noutros recursos e processos externos.

Consulte [o que é o Azure Monitor para VMs?](vminsights-overview.md)

![Azure Monitor para VMs](media/insights-overview/vm-insights.png)

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre o [plataforma de dados do Azure Monitor](../platform/data-platform.md) aproveitado por informações.
* Saiba mais sobre os diferentes [origens de dados utilizadas pelo Azure Monitor](../platform/data-sources.md) e os diferentes tipos de dados recolhidos por cada uma das informações.
