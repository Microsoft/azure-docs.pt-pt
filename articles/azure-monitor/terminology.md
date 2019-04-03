---
title: Atualizações de terminologia de Monitor do Azure | Documentos da Microsoft
description: Descreve as alterações de terminologia recentes feitas aos serviços de monitorização do Azure.
author: bwren
manager: carmonm
editor: tysonn
services: azure-monitor
documentationcenter: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2019
ms.author: bwren
ms.openlocfilehash: 8f645f7d569546a8362d0149806a2b4636567fd0
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2019
ms.locfileid: "58847997"
---
# <a name="azure-monitor-naming-and-terminology-changes"></a>Alterações do Azure de nomenclatura e terminologia de Monitor
Alterações significativas feitas para o Azure Monitor recentemente, com diferentes serviços que estão sendo consolidados para simplificar a monitorização para os clientes do Azure. Este artigo descreve o nome recente e as alterações de terminologia na documentação do Azure Monitor.

## <a name="february-2019---log-analytics-terminology"></a>Fevereiro de 2019 - terminologia do Log Analytics
Após a consolidação dos diferentes serviços em Azure Monitor, estamos a redirecioná-a próxima etapa, modificando a terminologia na nossa documentação para descrever melhor o serviço do Azure Monitor e os respetivos componentes diferentes. 

### <a name="log-analytics"></a>Log Analytics
Azure monitorizar registo de dados ainda são armazenado numa área de trabalho do Log Analytics e de que é ainda recolhido e analisado pelo serviço Log Analytics do mesmo, mas estamos a alterar o termo _do Log Analytics_ em vários lugares para _registos do Azure Monitor_ . Esse termo melhor reflete a sua função no Azure Monitor e fornece consistência melhor [métricas no Azure Monitor](platform/data-platform-metrics.md).

O termo _do log analytics_ agora aplica-se sobretudo para a página no portal do Azure utilizada para escrever e executar consultas e analisar dados de registo. É o equivalente funcional [Explorador de métricas](platform/metrics-charts.md), que é a página no portal do Azure utilizado para analisar dados de métrica.

### <a name="log-analytics-workspaces"></a>Áreas de trabalho do Log Analytics
[Áreas de trabalho](platform/manage-access.md) que contêm dados de registo no Azure Monitor ainda são referidos como áreas de trabalho do Log Analytics. O **do Log Analytics** menu no portal do Azure foi renomeada para **áreas de trabalho do Log Analytics** e é onde [criar novas áreas de trabalho](learn/quick-create-workspace.md) e configurar origens de dados. Analisar os registos e outros dados de monitorização no **do Azure Monitor** e configure a sua área de trabalho **áreas de trabalho do Log Analytics**.

### <a name="management-solutions"></a>Soluções de gestão
[Soluções de gestão](insights/solutions.md) foram renomeadas para _soluções de monitorização_, que melhor descreve sua funcionalidade.


## <a name="august-2018---consolidation-of-monitoring-services-into-azure-monitor"></a>Agosto de 2018 - consolidação dos serviços de monitorização no Azure Monitor
O log Analytics e Application Insights foram consolidado no Azure Monitor para fornecer uma única experiência integrada para a monitorização de recursos do Azure e de ambientes híbridos. Nenhuma funcionalidade foi removida estes serviços e os utilizadores podem executar os mesmos cenários em que eles completaram sempre sem perda ou o comprometimento de quaisquer funcionalidades.

Documentação para cada um desses serviços foi consolidada num único conjunto de conteúdo para o Azure Monitor. Este passo irá ajudar o leitor encontrar todo o conteúdo para um determinado cenário de monitorização numa única localização em vez de vários conjuntos de conteúdo de referência. À medida que o serviço consolidado evolui, o conteúdo irá tornar-se mais integrado.

Outras funcionalidades que foram consideradas parte do Log Analytics, tais como agentes e vistas também têm sido reposicionadas como recursos do Azure Monitor. Sua funcionalidade não mudou senão possíveis melhorias para a sua experiência no portal do Azure.


## <a name="april-2018---retirement-of-operations-management-suite-brand"></a>Abril de 2018 - marca de extinção do Operations Management Suite
Operations Management Suite (OMS) era um agrupamento dos seguintes serviços de gestão do Azure para fins de licenciamento:

- Application Insights
- Automatização do Azure
- Azure Backup
- Log Analytics
- Site Recovery

[Os novos preços foi introduzido para estes serviços](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/), e o agrupamento de OMS já não está disponível para novos clientes. Nenhum dos serviços que faziam parte do OMS foram alterados, exceto para a consolidação para o Azure Monitor descrito acima. 




## <a name="next-steps"></a>Passos Seguintes

- Leia uma [descrição geral do Azure Monitor](overview.md) que descreve a diferentes componentes e recursos.
- Saiba mais sobre o [transição do portal do OMS](../log-analytics/log-analytics-oms-portal-transition.md).