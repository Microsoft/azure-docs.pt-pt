---
title: Atualizações de terminologia do Azure Monitor | Microsoft Docs
description: Descreve as recentes alterações terminíries feitas nos serviços de monitorização do Azure.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: 99592544d97793f82db36e1d2cb33ba8c6af1c9c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100586965"
---
# <a name="azure-monitor-naming-and-terminology-changes"></a>Azure Monitor nomeação e alterações de terminologia
Recentemente foram feitas alterações significativas no Azure Monitor, com a consolidação de diferentes serviços para simplificar a monitorização dos clientes do Azure. Este artigo descreve alterações recentes de nome e terminologia na documentação do Azure Monitor.

## <a name="october-2019---diagnostic-log-to-resource-log"></a>Outubro 2019 - Registo de Diagnóstico para Registo de Recursos
Os "registos de diagnóstico" foram alterados para "registos de recursos" para melhor corresponderem ao que está realmente a ser recolhido. O termo "definições de diagnóstico" permanece o mesmo.  

## <a name="february-2019---log-analytics-terminology"></a>Fevereiro 2019 - Terminologia Log Analytics
Após a consolidação de diferentes serviços no âmbito do Azure Monitor, estamos a dar o próximo passo modificando a terminologia na nossa documentação para melhor descrever o serviço Azure Monitor e os seus diferentes componentes. 

### <a name="log-analytics"></a>Log Analytics
Os dados do registo do Azure Monitor ainda estão armazenados num espaço de trabalho do Log Analytics e ainda são recolhidos e analisados pelo mesmo serviço Log Analytics, mas estamos a alterar o termo _Log Analytics_ em muitos locais para _registos do Azure Monitor_. Este termo reflete melhor o seu papel no Azure Monitor e proporciona uma melhor consistência com [as métricas no Azure Monitor](essentials/data-platform-metrics.md).

O termo _"log analytics"_ aplica-se agora principalmente à página do portal Azure utilizada para escrever e executar consultas e analisar dados de registo. É o equivalente funcional do explorador de [métricas](essentials/metrics-charts.md), que é a página do portal Azure usada para analisar dados métricos.

### <a name="log-analytics-workspaces"></a>Áreas de trabalho do Log Analytics
[Os espaços de trabalho](logs/manage-access.md) que possuem dados de registo no Azure Monitor ainda são referidos como espaços de trabalho log analytics. O menu **Log Analytics** no portal Azure foi renomeado para log **analytics e** é onde cria novos espaços [de trabalho](logs/quick-create-workspace.md) e configura fontes de dados. Analise os seus registos e outros dados de monitorização no **Azure Monitor** e configuure o seu espaço de trabalho em **espaços de trabalho Log Analytics**.

### <a name="management-solutions"></a>Soluções de gestão
[As soluções de gestão](insights/solutions.md) foram renomeadas para _monitorizar soluções_, o que melhor descreve a sua funcionalidade.


## <a name="august-2018---consolidation-of-monitoring-services-into-azure-monitor"></a>Agosto 2018 - Consolidação dos serviços de monitorização no Azure Monitor
Log Analytics e Application Insights foram consolidados no Azure Monitor para proporcionar uma única experiência integrada para monitorizar os recursos do Azure e ambientes híbridos. Nenhuma funcionalidade foi removida destes serviços, e os utilizadores podem realizar os mesmos cenários que sempre completaram sem qualquer perda ou compromisso de quaisquer funcionalidades.

A documentação de cada um destes serviços foi consolidada num único conjunto de conteúdos para o Azure Monitor. Isto ajudará o leitor a encontrar todo o conteúdo para um determinado cenário de monitorização num único local, em vez de ter de fazer referência a vários conjuntos de conteúdos. À medida que o serviço consolidado evolui, o conteúdo tornar-se-á mais integrado.

Outras funcionalidades que foram consideradas parte do Log Analytics, tais como agentes e vistas, também foram reposicionadas como características do Azure Monitor. A funcionalidade não mudou para além de potenciais melhorias na sua experiência no portal Azure.


## <a name="april-2018---retirement-of-operations-management-suite-brand"></a>Abril 2018 - Reforma da Marca Suite de Gestão de Operações
O Suíte de Gestão de Operações (OMS) foi uma agregação dos seguintes serviços de gestão da Azure para efeitos de licenciamento:

- Application Insights
- Automatização do Azure
- Azure Backup
- Log Analytics
- Recuperação de sites

[Foram introduzidos novos preços para estes serviços](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/), e a agregação OMS já não está disponível para novos clientes. Nenhum dos serviços que faziam parte da OMS mudou, exceto a consolidação no Azure Monitor acima descrito. 




## <a name="next-steps"></a>Passos seguintes

- Leia uma [visão geral do Azure Monitor](overview.md) que descreve os seus diferentes componentes e funcionalidades.
- Conheça a [transição do portal OMS.](./logs/oms-portal-transition.md)
