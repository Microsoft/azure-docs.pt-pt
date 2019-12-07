---
title: Azure Monitor atualizações de terminologia | Microsoft Docs
description: Descreve as alterações de terminologia recentes feitas nos serviços de monitoramento do Azure.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: 01d4c8b3b803c5fce4fe24a5c96e649212fc4dca
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894355"
---
# <a name="azure-monitor-naming-and-terminology-changes"></a>Azure Monitor as alterações de nomenclatura e terminologia
Foram feitas alterações significativas para Azure Monitor recentemente, com diferentes serviços sendo consolidados para simplificar o monitoramento de clientes do Azure. Este artigo descreve as alterações recentes de nome e terminologia na documentação Azure Monitor.

## <a name="october-2019---diagnostic-log-to-resource-log"></a>Outubro de 2019-log de diagnóstico para o log de recursos
"Logs de diagnóstico" foram alterados para "logs de recursos" para melhor correspondência do que realmente está sendo coletado. O termo "configurações de diagnóstico" permanece o mesmo.  

## <a name="february-2019---log-analytics-terminology"></a>Terminologia de fevereiro de 2019-Log Analytics
Após a consolidação de diferentes serviços em Azure Monitor, estamos fazendo a próxima etapa modificando a terminologia em nossa documentação para descrever melhor o serviço de Azure Monitor e seus componentes diferentes. 

### <a name="log-analytics"></a>Log Analytics
Azure Monitor dados de log ainda são armazenados em um espaço de trabalho Log Analytics e ainda são coletados e analisados pelo mesmo serviço de Log Analytics, mas estamos alterando o termo _log Analytics_ em muitos locais para _Azure monitor logs_. Esse termo reflete melhor sua função no Azure Monitor e fornece maior consistência com as [métricas em Azure monitor](platform/data-platform-metrics.md).

O termo _log Analytics_ agora se aplica principalmente à página no portal do Azure usado para gravar e executar consultas e analisar dados de log. É o equivalente funcional do [Metrics Explorer](platform/metrics-charts.md), que é a página no portal do Azure usado para analisar dados de métrica.

### <a name="log-analytics-workspaces"></a>Áreas de trabalho do Log Analytics
Os [espaços de trabalho](platform/manage-access.md) que mantêm dados de log no Azure monitor ainda são chamados de espaços de trabalho log Analytics. O menu **log Analytics** na portal do Azure foi renomeado para **log Analytics espaços de trabalho** e é onde você [cria novos espaços de trabalho](learn/quick-create-workspace.md) e configura fontes de dados. Analise seus logs e outros dados de monitoramento em **Azure monitor** e configure seu espaço de trabalho em **log Analytics espaços de trabalho**.

### <a name="management-solutions"></a>Soluções de gestão
As [soluções de gerenciamento](insights/solutions.md) foram renomeadas para _monitorar soluções_, o que descreve melhor sua funcionalidade.


## <a name="august-2018---consolidation-of-monitoring-services-into-azure-monitor"></a>Agosto de 2018-consolidação dos serviços de monitoramento no Azure Monitor
Log Analytics e Application Insights foram consolidados no Azure Monitor para fornecer uma experiência integrada única para monitorar recursos do Azure e ambientes híbridos. Nenhuma funcionalidade foi removida desses serviços, e os usuários podem executar os mesmos cenários que sempre concluíram sem perda ou comprometimento de quaisquer recursos.

A documentação de cada um desses serviços foi consolidada em um único conjunto de conteúdo para Azure Monitor. Isso ajudará o leitor a localizar todo o conteúdo de um cenário de monitoramento específico em um único local, em vez de ter que fazer referência a vários conjuntos de conteúdo. À medida que o serviço consolidado evoluir, o conteúdo se tornará mais integrado.

Outros recursos que foram considerados parte do Log Analytics, como agentes e exibições, também foram reposicionados como recursos de Azure Monitor. Sua funcionalidade não mudou além das possíveis melhorias em sua experiência na portal do Azure.


## <a name="april-2018---retirement-of-operations-management-suite-brand"></a>Abril de 2018-desativação da marca do Operations Management Suite
O OMS (Operations Management Suite) foi um agrupamento dos seguintes serviços de gerenciamento do Azure para fins de licenciamento:

- Estatísticas das Aplicações
- Automatização do Azure
- Azure Backup
- Log Analytics
- Recuperação de Site

[Novos preços foram introduzidos para esses serviços](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/), e o agrupamento do OMS não está mais disponível para novos clientes. Nenhum dos serviços que faziam parte do OMS foi alterado, exceto pela consolidação em Azure Monitor descrito acima. 




## <a name="next-steps"></a>Passos seguintes

- Leia uma [visão geral de Azure monitor](overview.md) que descreve seus diferentes componentes e recursos.
- Saiba mais sobre a [transição do portal do OMS](../log-analytics/log-analytics-oms-portal-transition.md).
