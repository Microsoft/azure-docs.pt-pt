---
title: Detalhes de recolha de dados para soluções de gestão no Azure | Documentos da Microsoft
description: Soluções de gestão no Azure são um conjunto de dados, visualização e lógica de regras de aquisição que fornecem métricas articuladas em torno de uma área de problema específico.  Este artigo fornece uma lista de soluções de gestão disponíveis da Microsoft e detalhes sobre o método e a frequência de recolha de dados.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/26/2018
ms.author: bwren
ms.openlocfilehash: 6da2b63b7a9c8dc490bd30bb02ae6e7d932b2f7b
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59683924"
---
# <a name="data-collection-details-for-management-solutions-in-azure"></a>Detalhes de recolha de dados para soluções de gestão no Azure
Este artigo inclui uma lista dos [soluções de gestão](solutions.md) disponíveis da Microsoft com links para sua documentação detalhada.  Também fornece informações no seu método e a frequência de recolha de dados no Azure Monitor.  Pode utilizar as informações neste artigo, para identificar as diferentes soluções disponíveis e para compreender os requisitos de fluxo e a ligação de dados para soluções de gestão diferentes. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="list-of-management-solutions"></a>Lista de soluções de gestão

A tabela seguinte lista os [soluções de gestão](solutions.md) no Azure fornecido pela Microsoft. Uma entrada na coluna significa que a solução recolhe dados para o Azure Monitor usando esse método.  Se uma solução não tem colunas selecionadas, em seguida, escreve diretamente para o Azure Monitor de outro serviço do Azure. Siga a ligação para cada um para essa documentação detalhada para obter mais informações.

Explicações das colunas são os seguintes:

- **Agente de monitorização Microsoft** -agente usado no Windows e Linux para executar o pacote de gestão do SCOM e o gerenciamento de soluções do Azure. Nesta configuração, o agente está ligado diretamente para o Azure Monitor sem a ser ligado a um grupo de gestão do Operations Manager. 
- **Do Operations Manager** -agente idêntica, como o Microsoft monitoring agent. Nesta configuração, tem [ligado a um grupo de gestão do Operations Manager](../../azure-monitor/platform/om-agents.md) que está ligada ao Azure Monitor. 
-  **O armazenamento do Azure** -solução recolhe dados de uma conta de armazenamento do Azure. 
- **Gestor de operações necessárias?** -Um grupo de gestão do Operations Manager ligado é obrigatório para a recolha de dados da solução de gestão. 
- **Dados de agente do Operations Manager enviados por grupo de gestão** – se o agente está [ligadas a um grupo de gestão do SCOM](../../azure-monitor/platform/om-agents.md), em seguida, os dados são enviados para o Azure Monitor do servidor de gestão. Neste caso, o agente não precisa se conectar diretamente para o Azure Monitor. Se esta caixa não está selecionada, em seguida, dados são enviados do agente diretamente para o Azure Monitor, mesmo que o agente está ligado a um grupo de gestão do SCOM. Terá de conseguir comunicar com o Azure Monitor através da [gateway do Log Analytics](../../azure-monitor/platform/gateway.md).
- **Frequência de recolha** -Especifica a frequência que os dados são recolhidos pela solução de gestão. 



| **Solução de gestão** | **Plataforma** | **Agente de monitorização da Microsoft** | **Agente do Operations Manager** | **Armazenamento do Azure** | **Gestor de operações necessárias?** | **Dados de agente do Operations Manager enviados por grupo de gestão** | **Frequência de recolha** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Log analytics da atividade](../../azure-monitor/platform/collect-activity-logs.md) | Azure | | | | | | na notificação |
| [Avaliação do AD](../../azure-monitor/insights/ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dias |
| [Estado de Replicação do AD](../../azure-monitor/insights/ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5 dias |
| [Agente de Funcionamento de Agente](solution-agenthealth.md) | Windows e Linux | &#8226; | &#8226; | | | &#8226; | 1 minuto |
| [Gestão de alertas](../../azure-monitor/platform/alert-management-solution.md) (Nagios) |Linux |&#8226; | | | | |na chegada |
| [Gestão de alertas](../../azure-monitor/platform/alert-management-solution.md) (Zabbix) |Linux |&#8226; | | | | |1 minuto |
| [Gestão de alertas](../../azure-monitor/platform/alert-management-solution.md) (Operations Manager) |Windows | |&#8226; | |&#8226; |&#8226; |3 minutos |
| [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) | Azure | | | | | | n/d |
| [Conector do Application Insights (preterido)](../../azure-monitor/platform/app-insights-connector.md) | Azure | | | |  |  | na notificação |
| [Função de trabalho de híbrida de automatização](../../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | n/d |
| [Análise do Gateway de aplicação do Azure](../../azure-monitor/insights/azure-networking-analytics.md) | Azure |  |  |  |  |  | na notificação |
| **Solução de gestão** | **Plataforma** | **Agente de monitorização da Microsoft** | **Agente do Operations Manager** | **Armazenamento do Azure** | **Gestor de operações necessárias?** | **Dados de agente do Operations Manager enviados por grupo de gestão** | **Frequência de recolha** |
| [Análise de grupo de segurança de rede do Azure (preterido)](../../azure-monitor/insights/azure-networking-analytics.md) | Azure |  |  |  |  |  | na notificação |
| [Análise SQL do Azure (pré-visualização)](../../azure-monitor/insights/azure-sql.md) | Windows | | | | | | 1 minuto |
| [Cópia de segurança](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | na notificação |
| [Capacidade e desempenho (pré-visualização)](../../azure-monitor/insights/capacity-performance.md) |Windows |&#8226; |&#8226; | | |&#8226; |na chegada |
| [Monitorização de Alterações](../../automation/automation-change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |[Varia](../../automation/automation-change-tracking.md#change-tracking-data-collection-details) |
| [Monitorização de Alterações](../../automation/automation-change-tracking.md) |Linux |&#8226; | | | | |[Varia](../../automation/automation-change-tracking.md#change-tracking-data-collection-details) |
| [Contentores](../../azure-monitor/insights/containers.md) | Windows e Linux | &#8226; | &#8226; |  |  |  | 3 minutos |
| [Análise do Cofre de Chaves](../../azure-monitor/insights/azure-key-vault.md) |Windows | | | | | |na notificação |
| [Avaliação de Software Maligno](../../security-center/security-center-install-endpoint-protection.md) |Windows |&#8226; |&#8226; | | |&#8226; |hora a hora |
| [Monitor de Desempenho da Rede](../../azure-monitor/insights/network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | Os handshakes TCP em 5 segundos, dados enviados a cada 3 minutos |
| [Análise do Office 365 (pré-visualização)](solution-office-365.md) |Windows | | | | | |na notificação |
| **Solução de gestão** | **Plataforma** | **Agente de monitorização da Microsoft** | **Agente do Operations Manager** | **Armazenamento do Azure** | **Gestor de operações necessárias?** | **Dados de agente do Operations Manager enviados por grupo de gestão** | **Frequência de recolha** |
| [Análise do Service Fabric](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |Windows | | |&#8226; | | |5 minutos |
| [Mapa do Serviço](../../azure-monitor/insights/service-map.md) | Windows e Linux | &#8226; | &#8226; |  |  |  | 15 segundos |
| [Avaliação do SQL](../../azure-monitor/insights/sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dias |
| [SurfaceHub](../../azure-monitor/insights/surface-hubs.md) |Windows |&#8226; | | | | |na chegada |
| [Avaliação do System Center Operations Manager (pré-visualização)](../../azure-monitor/insights/scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | sete dias |
| [Gestão de Atualizações](../../automation/automation-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |pelo menos 2 vezes por dia e 15 minutos depois de instalar uma atualização |
| [Atualizar Preparação](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2 dias |
| [Monitorização de VMware (preterido)](../../azure-monitor/insights/vmware.md) | Linux | &#8226; |  |  |  |  | 3 minutos |
| [Ligar dados 2.0 (pré-visualização)](../../azure-monitor/insights/wire-data.md) |Windows (2012 R2 / 8.1 ou posterior) |&#8226; |&#8226; | | | | 1 minuto |




## <a name="next-steps"></a>Passos Seguintes
* Saiba como [criar consultas](../../azure-monitor/log-query/log-query-overview.md) para analisar os dados recolhidos por soluções de gestão.
