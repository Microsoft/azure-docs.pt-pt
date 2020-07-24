---
title: Controlo de Segurança Azure - Registo e Monitorização
description: Registo e monitorização do controlo de segurança do Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 82114164d70eae71678e70ff2bdb7ea44a54d4cd
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87076312"
---
# <a name="security-control-logging-and-monitoring"></a>Controlo de Segurança: Registo e Monitorização

O registo e monitorização de segurança centra-se em atividades relacionadas com a ativação, aquisição e armazenamento de registos de auditoria para os serviços Azure.

## <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizar fontes de sincronização de tempo aprovadas

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2.1 | 6.1 | Microsoft |

A Microsoft mantém fontes de tempo para recursos Azure, no entanto, tem a opção de gerir as definições de sincronização de tempo para os seus recursos computativos.

- [Como configurar a sincronização do tempo para os recursos computativos do Azure Windows](https://docs.microsoft.com/azure/virtual-machines/windows/time-sync)

- [Como configurar a sincronização temporal para os recursos computativos Azure Linux](https://docs.microsoft.com/azure/virtual-machines/linux/time-sync)

## <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2,2 | 6.5, 6.6 | Customer |

Ingerir registos via Azure Monitor para agregar dados de segurança gerados por dispositivos de ponto final, recursos de rede e outros sistemas de segurança. Dentro do Azure Monitor, utilize o Log Analytics Workspace(s) para consultar e realizar análises, e utilize contas de armazenamento Azure para armazenamento a longo prazo/arquivo.

Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros. 

- [Como embarcar Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [Como recolher registos e métricas da plataforma com o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

- [Como recolher registos internos de anfitriões da Azure Virtual Machine com o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm)

- [Como começar com o Azure Monitor e a integração do SIEM de terceiros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

## <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2.3 | 6.2, 6.3 | Customer |

Ativar Definições de Diagnóstico em recursos Azure para acesso a registos de auditoria, segurança e diagnóstico. Os registos de atividade, que estão automaticamente disponíveis, incluem fonte de evento, data, utilizador, marcação de tempo, endereços de origem, endereços de destino e outros elementos úteis.

- [Como recolher registos e métricas da plataforma com o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

- [Compreenda o registo e diferentes tipos de registo em Azure](https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview)

## <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2,4 | 6.2, 6.3 | Customer |

Se o recurso computacional for propriedade da Microsoft, então a Microsoft é responsável pela monitorização do mesmo. Se o recurso computacional é propriedade da sua organização, é da sua responsabilidade monitorizá-lo. Pode utilizar o Centro de Segurança Azure para monitorizar o SISTEMA. Os dados recolhidos pelo Security Center do sistema operativo incluem o tipo e versão de SISTEMA, OS (Registos de Eventos do Windows), processos de execução, nome da máquina, endereços IP e registados no utilizador. O Agente Descodudo de Log também recolhe ficheiros de despejo de acidentes.

- [Como recolher registos internos de anfitriões da Azure Virtual Machine com o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm)

- [Compreender a recolha de dados do Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection)

## <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2.5 | 6.4 | Customer |

Dentro do Azure Monitor, desa um período de retenção do espaço de trabalho do Log Analytics de acordo com os regulamentos de conformidade da sua organização. Utilize contas de armazenamento Azure para armazenamento a longo prazo/arquivo.

- [Alterar o período de retenção de dados em Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

- [Como configurar a política de retenção para os registos de conta de armazenamento Azure](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)

## <a name="26-monitor-and-review-logs"></a>2.6: Monitor e revisão de Registos

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2,6 | 6.7 | Customer |

Analise e monitorize registos para comportamento anómalo e reveja regularmente os resultados. Utilize o log analytics workspace do Azure Monitor para rever registos e efetuar consultas nos dados de registo.

Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros. 

- [Como embarcar Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [Compreender log analytics workspace](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

- [Como realizar consultas personalizadas no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

## <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2.7 | 6.8 | Customer |

Utilize o Centro de Segurança Azure com log analytics workspace para monitorizar e alertar sobre a atividade anómala encontrada em registos de segurança e eventos.

Em alternativa, pode ativar e a bordo dados para a Azure Sentinel.

- [Como embarcar Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [Como gerir alertas no Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

- [Como alertar nos dados de registo de registo de registos de registos](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

## <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a sessão anti-malware

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2,8 | 8.6 | Customer |

Ativar a recolha de eventos antimalware para máquinas virtuais Azure e Serviços cloud.

- [Como configurar o Microsoft Antimalware para Máquinas Virtuais](/powershell/module/servicemanagement/azure.service/set-azurevmmicrosoftantimalwareextension?view=azuresmps-4.0.0)

- [Como configurar o Microsoft Antimalware para serviços cloud](/powershell/module/servicemanagement/azure.service/set-azureserviceantimalwareextension?view=azuresmps-4.0.0)

- [Compreenda o Antimalware da Microsoft](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

## <a name="29-enable-dns-query-logging"></a>2.9: Ativar a sessão de consulta de DNS

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2.9 | 8.7 | Customer |

Implemente uma solução de terceiros a partir do Azure Marketplace para a solução de registo de DNS conforme as suas organizações precisam.  

## <a name="210-enable-command-line-audit-logging"></a>2.10: Permitir a exploração de auditoria de linha de comando

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2,10 | 8.8 | Customer |

Utilize o Agente de Monitorização do Microsoft em todas as máquinas virtuais suportadas do Azure Windows para registar o evento de criação de processos e o campo CommandLine.   Para máquinas virtuais suportadas Azure Linux, pode configurar manualmente o registo de consolas por nó e utilizar o Syslog para armazenar os dados.  Além disso, utilize o espaço de trabalho do Log Analytics do Azure Monitor para rever registos e realizar consultas em dados registados a partir de máquinas Virtuais Azure. 

- [Data collection in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier) (Recolha de dados no Centro de Segurança do Azure)

- [Como realizar consultas personalizadas no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

- [Syslog data sources in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-syslog) (Origens de dados de Syslog no Azure Monitor)


## <a name="next-steps"></a>Passos seguintes

- Ver o próximo Controlo de Segurança: [Identidade e Controlo de Acesso](security-control-identity-access-control.md)