---
title: Inventário de soluções de monitorização no Azure Microsoft Docs
description: As soluções de monitorização no Monitor Azure são uma coleção de regras de lógica, visualização e aquisição de dados que fornecem métricas focadas numa determinada área problemática.  Este artigo fornece uma lista de soluções de monitorização disponíveis da Microsoft e detalhes sobre o seu método e frequência de recolha de dados.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/26/2018
ms.openlocfilehash: 7b88d957bce45bf518fc77584f1691de8010459a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663135"
---
# <a name="inventory-and-data-collection-details-for-monitoring-solutions-in-azure"></a>Inventário e detalhes de recolha de dados para soluções de monitorização em Azure
[A monitorização](solutions.md) de soluções de alavancagem de serviços em Azure para fornecer informações adicionais sobre o funcionamento de uma determinada aplicação ou serviço. As soluções de monitorização normalmente recolhem dados de registo e fornecem consultas e pontos de vista para analisar os dados recolhidos. Pode adicionar soluções de monitorização ao Azure Monitor para quaisquer aplicações e serviços que utilize. Normalmente estão disponíveis sem custos, mas recolhem dados que podem invocar taxas de utilização.

Este artigo inclui uma lista de soluções de [montioring](solutions.md) disponíveis na Microsoft com links para a sua documentação detalhada.  Também fornece informações sobre o seu método e frequência de recolha de dados no Monitor Azure.  Pode utilizar a informação neste artigo para identificar as diferentes soluções disponíveis e para compreender os requisitos de fluxo de dados e ligação para diferentes soluções de monitorização.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="list-of-monitoring-solutions"></a>Lista de soluções de monitorização

A tabela seguinte lista as [soluções de monitorização](solutions.md) em Azure fornecidas pela Microsoft. Uma entrada na coluna significa que a solução recolhe dados no Monitor Azure utilizando este método.  Se uma solução não tiver colunas selecionadas, escreve diretamente para o Monitor Azure a partir de outro serviço Azure. Siga o link para cada um à sua documentação detalhada para obter mais informações.

As explicações das colunas são as seguintes:

- Agente de **monitorização** da Microsoft - Agente utilizado no Windows e Linux para executar o pacote de gestão a partir do SCOM e soluções de monitorização do Azure. Nesta configuração, o agente está ligado diretamente ao Monitor Azure sem estar ligado a um grupo de gestão de Gestor de Operações. 
- **Diretor de Operações** - Agente idêntico como agente de monitorização da Microsoft. Nesta configuração, está ligado a um grupo de gestão de Gestor de [Operações](../platform/om-agents.md) que está ligado ao Monitor Azure. 
-  **Azure Storage** - Solução recolhe dados de uma conta de armazenamento Azure. 
- **Gestor de Operações necessário?** - É necessário um grupo de gestão de Gestão de Operações ligado para a recolha de dados através da solução de monitorização. 
- Dados do **agente de operações enviados através** de um grupo de gestão - Se o agente estiver ligado a um grupo de [gestão SCOM,](../platform/om-agents.md)então os dados são enviados para o Monitor De Operações a partir do servidor de gestão. Neste caso, o agente não precisa de se ligar diretamente ao Monitor Azure. Se esta caixa não for selecionada, os dados são enviados do agente diretamente para o Monitor Azure, mesmo que o agente esteja ligado a um grupo de gestão SCOM. Terá de ser capaz de comunicar ao Monitor Azure através do [portal Log Analytics](../platform/gateway.md).
- **Frequência** de recolha - Especifica a frequência que os dados são recolhidos pela solução de monitorização. 



| **Solução de monitorização** | **Plataforma** | **Agente de monitorização da Microsoft** | **Agente do Operations Manager** | **Armazenamento azure** | **Gestor de Operações necessário?** | **Dados do agente de operações enviados através de grupo de gestão** | **Frequência da recolha** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Análise de Registo de Atividades](../platform/activity-log-collect.md) | Azure | | | | | | a quando a notificação |
| [Avaliação do AD](ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dias |
| [Estado de Replicação do AD](ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5 dias |
| [Agente de Funcionamento de Agente](solution-agenthealth.md) | Windows e Linux | &#8226; | &#8226; | | | &#8226; | 1 minuto |
| [Gestão de Alerta](../platform/alert-management-solution.md) (Nagios) |Linux |&#8226; | | | | |à chegada |
| [Gestão de Alerta](../platform/alert-management-solution.md) (Zabbix) |Linux |&#8226; | | | | |1 minuto |
| [Gestão de Alerta](../platform/alert-management-solution.md) (Gestor de Operações) |Windows | |&#8226; | |&#8226; |&#8226; |3 minutos |
| [Recuperação do site azure](../../site-recovery/site-recovery-overview.md) | Azure | | | | | | n/d |
| [Conector insights de aplicação (Depreciado)](../platform/app-insights-connector.md) | Azure | | | |  |  | a quando a notificação |
| [Automação Hybrid Worker](../../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | n/d |
| [Azure Application Gateway Analytics](azure-networking-analytics.md) | Azure |  |  |  |  |  | a quando a notificação |
| **Solução de monitorização** | **Plataforma** | **Agente de monitorização da Microsoft** | **Agente do Operations Manager** | **Armazenamento azure** | **Gestor de Operações necessário?** | **Dados do agente de operações enviados através de grupo de gestão** | **Frequência da recolha** |
| [Azure Network Security Group Analytics (Deprecated)](azure-networking-analytics.md) | Azure |  |  |  |  |  | a quando a notificação |
| [Análise Azure SQL (Pré-visualização)](azure-sql.md) | Windows | | | | | | 1 minuto |
| [Backup](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | a quando a notificação |
| [Capacidade e Desempenho (Pré-visualização)](capacity-performance.md) |Windows |&#8226; |&#8226; | | |&#8226; |à chegada |
| [Monitorização de Alterações](../../automation/change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |[varia](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Monitorização de Alterações](../../automation/change-tracking.md) |Linux |&#8226; | | | | |[varia](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Contentores](containers.md) | Windows e Linux | &#8226; | &#8226; |  |  |  | 3 minutos |
| [Análise do Cofre de Chaves](azure-key-vault.md) |Windows | | | | | |a quando a notificação |
| [Avaliação de Software Maligno](../../security-center/security-center-install-endpoint-protection.md) |Windows |&#8226; |&#8226; | | |&#8226; |de hora em hora |
| [Monitor de Desempenho da Rede](network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | Apertos de mão tCP a cada 5 segundos, dados enviados a cada 3 minutos |
| [Office 365 Analytics (Pré-visualização)](solution-office-365.md) |Windows | | | | | |a quando a notificação |
| **Solução de monitorização** | **Plataforma** | **Agente de monitorização da Microsoft** | **Agente do Operations Manager** | **Armazenamento azure** | **Gestor de Operações necessário?** | **Dados do agente de operações enviados através de grupo de gestão** | **Frequência da recolha** |
| [Análise do Service Fabric](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |Windows | | |&#8226; | | |5 minutos |
| [Mapa de Serviços](service-map.md) | Windows e Linux | &#8226; | &#8226; |  |  |  | 15 segundos |
| [Avaliação do SQL](sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dias |
| [SurfaceHub](surface-hubs.md) |Windows |&#8226; | | | | |à chegada |
| [Avaliação do Gestor de Operações do Centro de Sistema (Pré-visualização)](scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | sete dias |
| [Gestão de Atualizações](../../automation/automation-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |pelo menos 2 vezes por dia e 15 minutos após a instalação de uma atualização |
| [Atualizar Preparação](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2 dias |
| [Monitorização VMware (Depreciada)](vmware.md) | Linux | &#8226; |  |  |  |  | 3 minutos |
| [Dados de Arame 2.0 (Pré-visualização)](wire-data.md) |Windows (2012 R2 / 8.1 ou posterior) |&#8226; |&#8226; | | | | 1 minuto |




## <a name="next-steps"></a>Passos seguintes
* Aprenda a [instalar e utilizar soluções de monitorização.](solutions.md)
* Aprenda a [criar consultas](../log-query/log-query-overview.md) para analisar os dados recolhidos através da monitorização de soluções.
