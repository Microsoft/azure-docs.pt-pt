---
title: Inventário de soluções de monitoramento no Azure | Microsoft Docs
description: As soluções de monitoramento no Azure Monitor são uma coleção de lógica, visualização e regras de aquisição de dados que fornecem métricas dinamizadas em uma área de problema específica.  Este artigo fornece uma lista de soluções de monitoramento disponíveis na Microsoft e detalhes sobre seu método e a frequência de coleta de dados.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/26/2018
ms.openlocfilehash: 9b0140b843d7da1b892463bff7d6014a3f9584ad
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75402010"
---
# <a name="inventory-and-data-collection-details-for-monitoring-solutions-in-azure"></a>Detalhes de inventário e coleta de dados para soluções de monitoramento no Azure
As [soluções de monitoramento](solutions.md) aproveitam os serviços no Azure para fornecer informações adicionais sobre a operação de um determinado aplicativo ou serviço. Soluções de monitoramento normalmente coletam dados de log e fornecem consultas e exibições para analisar os dados coletados. Você pode adicionar soluções de monitoramento para Azure Monitor para quaisquer aplicativos e serviços que você usar. Eles estão normalmente disponíveis em nenhum dado de custo mas recolher invoca os custos de utilização.

Este artigo inclui uma lista de [soluções de montioring](solutions.md) disponíveis da Microsoft com links para sua documentação detalhada.  Ele também fornece informações sobre seu método e a frequência de coleta de dados em Azure Monitor.  Você pode usar as informações neste artigo para identificar as diferentes soluções disponíveis e para entender o fluxo de dados e os requisitos de conexão para diferentes soluções de monitoramento.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="list-of-monitoring-solutions"></a>Lista de soluções de monitoramento

A tabela a seguir lista as [soluções de monitoramento](solutions.md) no Azure fornecidas pela Microsoft. Uma entrada na coluna significa que a solução coleta dados em Azure Monitor usando esse método.  Se uma solução não tiver colunas selecionadas, ela gravará diretamente em Azure Monitor de outro serviço do Azure. Siga o link para cada um deles com sua documentação detalhada para obter mais informações.

As explicações das colunas são as seguintes:

- **Agente de monitoramento da Microsoft** -agente usado no Windows e Linux para executar o pacote de gerenciamento do SCOM e soluções de monitoramento do Azure. Nessa configuração, o agente é conectado diretamente a Azure Monitor sem ser conectado a um grupo de gerenciamento Operations Manager. 
- **Operations Manager** -agente idêntico ao Microsoft Monitoring Agent. Nessa configuração, ele está [conectado a um grupo de gerenciamento de Operations Manager](../platform/om-agents.md) que está conectado ao Azure monitor. 
-  **Armazenamento do Azure** -a solução coleta dados de uma conta de armazenamento do Azure. 
- **Operations Manager necessário?** -Um grupo de gerenciamento de Operations Manager conectado é necessário para a coleta de dados pela solução de monitoramento. 
- **Operations Manager dados do agente enviados por meio do grupo de gerenciamento** – se o agente estiver [conectado a um grupo de gerenciamento do SCOM](../platform/om-agents.md), os dados serão enviados para Azure monitor do servidor de gerenciamento. Nesse caso, o agente não precisa se conectar diretamente ao Azure Monitor. Se essa caixa não estiver selecionada, os dados serão enviados do agente diretamente para Azure Monitor mesmo que o agente esteja conectado a um grupo de gerenciamento do SCOM. Será necessário ser capaz de se comunicar com o Azure Monitor por meio do [Gateway de log Analytics](../platform/gateway.md).
- **Frequência de coleta** -especifica a frequência com que os dados são coletados pela solução de monitoramento. 



| **Solução de monitoramento** | **Plataforma** | **Agente de monitoramento da Microsoft** | **Agente de Operations Manager** | **Armazenamento do Azure** | **Operations Manager necessário?** | **Operations Manager dados do agente enviados por meio do grupo de gerenciamento** | **Frequência de coleta** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Log Analytics de atividades](../platform/activity-log-collect.md) | Azure | | | | | | na notificação |
| [Avaliação do AD](ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dias |
| [Estado de Replicação do AD](ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5 days |
| [Agente de Funcionamento de Agente](solution-agenthealth.md) | Windows e Linux | &#8226; | &#8226; | | | &#8226; | um minuto |
| [Gerenciamento de alertas](../platform/alert-management-solution.md) (Nagios) |Linux |&#8226; | | | | |na chegada |
| [Gerenciamento de alertas](../platform/alert-management-solution.md) (Zabbix) |Linux |&#8226; | | | | |um minuto |
| [Gerenciamento de alertas](../platform/alert-management-solution.md) (Operations Manager) |Windows | |&#8226; | |&#8226; |&#8226; |3 minutos |
| [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) | Azure | | | | | | n/d |
| [Conector do Application Insights (preterido)](../platform/app-insights-connector.md) | Azure | | | |  |  | na notificação |
| [Hybrid Worker de Automação](../../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | n/d |
| [Análise do Gateway de aplicação do Azure](azure-networking-analytics.md) | Azure |  |  |  |  |  | na notificação |
| **Solução de monitoramento** | **Plataforma** | **Agente de monitoramento da Microsoft** | **Agente de Operations Manager** | **Armazenamento do Azure** | **Operations Manager necessário?** | **Operations Manager dados do agente enviados por meio do grupo de gerenciamento** | **Frequência de coleta** |
| [Análise do Grupo de Segurança de Rede do Azure (preterido)](azure-networking-analytics.md) | Azure |  |  |  |  |  | na notificação |
| [Análise SQL do Azure (pré-visualização)](azure-sql.md) | Windows | | | | | | um minuto |
| [Cópia de segurança](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | na notificação |
| [Capacidade e Desempenho (versão prévia)](capacity-performance.md) |Windows |&#8226; |&#8226; | | |&#8226; |na chegada |
| [Monitorização de Alterações](../../automation/change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |[consoante](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Monitorização de Alterações](../../automation/change-tracking.md) |Linux |&#8226; | | | | |[consoante](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Contentores](containers.md) | Windows e Linux | &#8226; | &#8226; |  |  |  | 3 minutos |
| [Análise do Cofre de Chaves](azure-key-vault.md) |Windows | | | | | |na notificação |
| [Avaliação de Software Maligno](../../security-center/security-center-install-endpoint-protection.md) |Windows |&#8226; |&#8226; | | |&#8226; |por hora |
| [Monitor de Desempenho da Rede](network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | Handshakes TCP a cada 5 segundos, dados enviados a cada 3 minutos |
| [Análise do Office 365 (versão prévia)](solution-office-365.md) |Windows | | | | | |na notificação |
| **Solução de monitoramento** | **Plataforma** | **Agente de monitoramento da Microsoft** | **Agente de Operations Manager** | **Armazenamento do Azure** | **Operations Manager necessário?** | **Operations Manager dados do agente enviados por meio do grupo de gerenciamento** | **Frequência de coleta** |
| [Análise do Service Fabric](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |Windows | | |&#8226; | | |5 minutos |
| [Mapa do Serviço](service-map.md) | Windows e Linux | &#8226; | &#8226; |  |  |  | 15 segundos |
| [Avaliação do SQL](sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dias |
| [SurfaceHub](surface-hubs.md) |Windows |&#8226; | | | | |na chegada |
| [Avaliação do System Center Operations Manager (versão prévia)](scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | sete dias |
| [Gestão de Atualizações](../../automation/automation-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |pelo menos 2 vezes por dia e 15 minutos após a instalação de uma atualização |
| [Atualizar Preparação](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2 dias |
| [Monitoramento do VMware (preterido)](vmware.md) | Linux | &#8226; |  |  |  |  | 3 minutos |
| [Wire Data 2.0 (versão prévia)](wire-data.md) |Windows (2012 R2/8,1 ou posterior) |&#8226; |&#8226; | | | | um minuto |




## <a name="next-steps"></a>Passos seguintes
* Saiba como [instalar e usar soluções de monitoramento](solutions.md).
* Saiba como [criar consultas](../log-query/log-query-overview.md) para analisar os dados coletados por soluções de monitoramento.
