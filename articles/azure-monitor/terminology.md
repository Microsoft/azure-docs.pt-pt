---
title: Atualizações da terminologia do Azure Monitor Microsoft Docs
description: Descreve as recentes alterações de terminologia feitas aos serviços de monitorização do Azure.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: 0c5e1096b0780d2f9e50389f772e63344935012b
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393457"
---
# <a name="azure-monitor-naming-and-terminology-changes"></a>Nomeação do Monitor Azure e mudanças de terminologia
Recentemente, foram feitas alterações significativas ao Azure Monitor, com diferentes serviços a serem consolidados de forma a simplificar a monitorização dos clientes do Azure. Este artigo descreve mudanças recentes de nome e terminologia na documentação do Monitor Do Azure.

## <a name="october-2019---diagnostic-log-to-resource-log"></a>Outubro 2019 - Registo de Diagnóstico ao Registo de Recursos
Os "registos de diagnóstico" foram alterados para "registos de recursos" para melhor corresponder ao que está realmente a ser recolhido. O termo "definições de diagnóstico" permanece o mesmo.  

## <a name="february-2019---log-analytics-terminology"></a>Fevereiro 2019 - Terminologia log Analytics
Após a consolidação de diferentes serviços no âmbito do Azure Monitor, estamos a dar o próximo passo, modificando a terminologia na nossa documentação para melhor descrever o serviço Azure Monitor e os seus diferentes componentes. 

### <a name="log-analytics"></a>Log Analytics
Os dados de registo do Azure Monitor ainda são armazenados num espaço de trabalho do Log Analytics e ainda são recolhidos e analisados pelo mesmo serviço Log Analytics, mas estamos a mudar o termo _Log Analytics_ em muitos locais para _registos do Monitor Azure._ Este termo reflete melhor o seu papel no Monitor Azure e proporciona uma melhor consistência com [métricas no Monitor Azure.](platform/data-platform-metrics.md)

O termo _log analytics_ agora se aplica principalmente à página no portal Azure usado para escrever e executar consultas e analisar dados de registo. É o equivalente funcional do explorador de [métricas,](platform/metrics-charts.md)que é a página do portal Azure usado para analisar dados métricos.

### <a name="log-analytics-workspaces"></a>Áreas de trabalho do Log Analytics
[Os espaços](platform/manage-access.md) de trabalho que detêm dados de registo no Monitor Azure ainda são referidos como espaços de trabalho de Log Analytics. O menu **Log Analytics** no portal Azure foi renomeado para espaços de trabalho **log Analytics** e é onde você cria novos espaços de [trabalho](learn/quick-create-workspace.md) e configura fontes de dados. Analise os seus registos e outros dados de monitorização no **Monitor Azure** e configure o seu espaço de trabalho em espaços de **trabalho de Log Analytics**.

### <a name="management-solutions"></a>Soluções de gestão
[As soluções](insights/solutions.md) de gestão foram renomeadas para soluções de _monitorização,_ que melhor descreve a sua funcionalidade.


## <a name="august-2018---consolidation-of-monitoring-services-into-azure-monitor"></a>Agosto de 2018 - Consolidação dos serviços de monitorização no Azure Monitor
Log Analytics e Application Insights foram consolidados no Azure Monitor para proporcionar uma única experiência integrada para monitorizar os recursos azure e ambientes híbridos. Nenhuma funcionalidade foi removida destes serviços, e os utilizadores podem realizar os mesmos cenários que sempre completaram sem perda ou compromisso de quaisquer funcionalidades.

A documentação para cada um destes serviços foi consolidada num único conjunto de conteúdos para o Azure Monitor. Isto ajudará o leitor a encontrar todos os conteúdos para um determinado cenário de monitorização num único local, em vez de ter de fazer referência a múltiplos conjuntos de conteúdos. À medida que o serviço consolidado evolui, o conteúdo tornar-se-á mais integrado.

Outras funcionalidades que foram consideradas parte do Log Analytics, como agentes e vistas, também foram reposicionadas como características do Monitor Azure. A sua funcionalidade não mudou além de potenciais melhorias à sua experiência no portal Azure.


## <a name="april-2018---retirement-of-operations-management-suite-brand"></a>Abril 2018 - Reforma da Marca Suite de Gestão de Operações
A Suite de Gestão de Operações (OMS) foi uma agregação dos seguintes serviços de gestão azure para fins de licenciamento:

- Application Insights
- Automatização do Azure
- Azure Backup
- Log Analytics
- Site Recovery

[Foram introduzidos novos preços para estes serviços](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/), e a agregação de OMS já não está disponível para novos clientes. Nenhum dos serviços que faziam parte da OMS mudou, com exceção da consolidação no Monitor Azure acima descrito. 




## <a name="next-steps"></a>Passos seguintes

- Leia uma [visão geral do Monitor Azure](overview.md) que descreve os seus diferentes componentes e funcionalidades.
- Conheça a [transição do portal OMS.](../log-analytics/log-analytics-oms-portal-transition.md)
