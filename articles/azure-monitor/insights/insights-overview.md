---
title: Visão geral dos Insights no Azure Monitor ! Microsoft Docs
description: Os insights fornecem uma experiência de monitorização personalizada no Azure Monitor para aplicações e serviços específicos. Este artigo fornece uma breve descrição de cada uma das informações que estão atualmente disponíveis.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/22/2019
ms.openlocfilehash: e3eefb97c85b3865abbf116b9912dc45f6db8ce8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87326873"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Visão geral dos Insights no Monitor Azure
Os insights fornecem uma experiência de monitorização personalizada para aplicações e serviços específicos. Armazenam dados na plataforma de dados do [Azure Monitor](../platform/data-platform.md) e aproveitam outras funcionalidades do Azure Monitor para análise e alerta, mas podem recolher dados adicionais e fornecer uma experiência única de utilizador no portal Azure. Informações de acesso a partir da secção **Insights** do menu Azure Monitor no portal Azure.


As secções seguintes fornecem uma breve descrição dos insights atualmente disponíveis no Azure Monitor. Consulte a documentação detalhada para mais detalhes sobre cada um.

## <a name="application-insights"></a>Application Insights
O Application Insights é um serviço de Gestão de Desempenho de Aplicações (APM) extensível para programadores Web em várias plataformas. Utilize-o para monitorizar a sua aplicação Web online. Funciona para aplicações em uma grande variedade de plataformas, incluindo .NET, Node.js e Java EE, hospedados no local, híbridos ou qualquer nuvem pública. Também se integra com o seu processo DevOps e tem pontos de ligação para uma variedade de ferramentas de desenvolvimento.

Ver [o que é Insights de Aplicação?](../app/app-insights-overview.md). .

![Application Insights](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>Azure Monitor para Contentores
O Azure Monitor para contentores monitoriza o desempenho das cargas de trabalho dos contentores implantadas em instâncias de contentores Azure ou em agrupamentos de Kubernetes geridos no Serviço Azure Kubernetes (AKS). Monitorizar os seus contentores é fundamental, especialmente quando se está a executar um cluster de produção, em escala, com múltiplas aplicações.

Consulte [o Monitor Azure para obter uma visão geral dos recipientes](./container-insights-overview.md).

![Azure Monitor para Contentores](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>Monitor Azure para grupos de recursos (pré-visualização)
O Azure Monitor para grupos de recursos ajuda a triagem e diagnóstico de quaisquer problemas que os seus recursos individuais encontrem, ao mesmo tempo que oferece contexto quanto à saúde e desempenho do grupo de recursos como um todo.

Consulte [os grupos de recursos monitor com O Monitor Azure (pré-visualização)](./resource-group-insights.md).

![Monitor Azure para grupos de recursos](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>Monitor Azure para VMs (pré-visualização)
O Azure Monitor para VMs monitoriza as máquinas virtuais (VM) e os conjuntos de dimensionamento de máquinas virtuais do Azure em escala. Analisa o desempenho e o estado de funcionamento das suas VMs do Windows e do Linux e monitoriza os respetivos processos e dependências noutros recursos e processos externos.

Veja [o que é O Monitor Azure para VMs?](vminsights-overview.md)

![Azure Monitor para VMs](media/insights-overview/vm-insights.png)

## <a name="azure-monitor-for-networks-preview"></a>Monitor Azure para redes (pré-visualização)
[O Azure Monitor for Networks](network-insights-overview.md) proporciona uma visão abrangente da saúde e métricas para todos os seus recursos de rede. A capacidade avançada de pesquisa ajuda-o a identificar dependências de recursos, permitindo cenários como identificar recursos que estão hospedando o seu website, simplesmente procurando o nome do seu site.

![Azure Monitor para Redes](media/insights-overview/network-insights.png)

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a [plataforma de dados Azure Monitor](../platform/data-platform.md) alavancada por insights.
* Conheça as [diferentes fontes de dados utilizadas pelo Azure Monitor](../platform/data-sources.md) e os diferentes tipos de dados recolhidos por cada uma das informações.

