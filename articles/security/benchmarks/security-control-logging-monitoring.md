---
title: Controlo de Segurança Azure - Exploração madeireira e monitorização
description: Exploração e monitorização do controlo de segurança azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: e58df0997c62a131a3c9987369f8e076a62d6654
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81408350"
---
# <a name="security-control-logging-and-monitoring"></a>Controlo de Segurança: Exploração e Monitorização

O registo e monitorização de segurança centra-se em atividades relacionadas com a viabilizar, adquirir e armazenar registos de auditoria para os serviços Azure.

## <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilize fontes de sincronização do tempo aprovadas

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2.1 | 6.1 | Microsoft |

No entanto, a Microsoft mantém fontes de tempo para os recursos do Azure, no entanto, tem a opção de gerir as definições de sincronização de tempo para os seus recursos computacionais.

- [Como configurar a sincronização do tempo para os recursos computacionais do Azure Windows](https://docs.microsoft.com/azure/virtual-machines/windows/time-sync)

- [Como configurar a sincronização do tempo para os recursos computacionais do Azure Linux](https://docs.microsoft.com/azure/virtual-machines/linux/time-sync)

## <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central de registos de segurança

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2,2 | 6.5, 6.6 | Cliente |

Ingerir logs via Azure Monitor para agregar dados de segurança gerados por dispositivos de ponto final, recursos de rede e outros sistemas de segurança. Dentro do Monitor Azure, utilize log analytics workspace(s) para consultar e realizar análises, e utilize contas de armazenamento Azure para armazenamento a longo prazo/arquivo.

Em alternativa, pode ativar e embarcar dados para o Azure Sentinel ou um SIEM de terceiros. 

- [Como embarcar no Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [Como recolher registos e métricas da plataforma com o Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

- [Como recolher registos internos de anfitriões da Máquina Virtual Azure com o Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm)

- [Como começar com o Azure Monitor e a integração siem de terceiros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

## <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditoria aos recursos do Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2.3 | 6.2, 6.3 | Cliente |

Ativar Definições de Diagnóstico nos recursos do Azure para acesso a registos de auditoria, segurança e diagnóstico. Os registos de atividade, que estão automaticamente disponíveis, incluem fonte de evento, data, utilizador, carimbo de tempo, endereços de origem, endereços de destino e outros elementos úteis.

- [Como recolher registos e métricas da plataforma com o Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

- [Compreenda a exploração madeireira e diferentes tipos de log em Azure](https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview)

## <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2.4 | 6.2, 6.3 | Cliente |

Se o recurso computacional for propriedade da Microsoft, então a Microsoft é responsável pela sua monitorização. Se o recurso computacional é propriedade da sua organização, é da sua responsabilidade monitorá-lo. Pode utilizar o Centro de Segurança Azure para monitorizar o Sistema Operativo. Os dados recolhidos pelo Security Center do sistema operativo incluem o tipo e versão OS, OS (Windows Event Logs), processos de execução, nome da máquina, endereços IP e registos registados no utilizador. O Agente Delog Analytics também recolhe ficheiros de despejo de acidentes.

- [Como recolher registos internos de anfitriões da Máquina Virtual Azure com o Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm)

- [Compreender a recolha de dados do Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection)

## <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de registos de segurança

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2.5 | 6.4 | Cliente |

Dentro do Monitor Azure, detete o seu período de retenção do Espaço de Trabalho de Log Analytics de acordo com os regulamentos de conformidade da sua organização. Utilize contas de armazenamento Azure para armazenamento a longo prazo/arquivo.

- [Alterar o período de retenção de dados no Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

- [Como configurar a política de retenção para registos de conta de Armazenamento Azure](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)

## <a name="26-monitor-and-review-logs"></a>2.6: Monitore e reveja registos

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2,6 | 6.7 | Cliente |

Analise e monitorize os registos para comportamentos anómalos e reveja regularmente os resultados. Utilize o log analytics workspace do Azure Monitor para rever os registos e executar consultas nos dados de registo.

Em alternativa, pode ativar e embarcar dados para o Azure Sentinel ou um SIEM de terceiros. 

- [Como embarcar no Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [Compreender o espaço de trabalho de Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

- [Como realizar consultas personalizadas no Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

## <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Ativar alertas para atividades anómalas

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2.7 | 6.8 | Cliente |

Utilize o Azure Security Center com log analytics Workspace para monitorização e alerta sobre a atividade anómala encontrada em registos e eventos de segurança.

Em alternativa, pode ativar e embarcar dados para o Azure Sentinel.

- [Como embarcar no Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [Como gerir alertas no Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

- [Como alertar sobre os dados de registo de análise de registo](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

## <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a exploração anti-malware

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2,8 | 8.6 | Cliente |

Ativar a recolha de eventos antimalware para Máquinas Virtuais Azure e Serviços cloud.

- [Como configurar o Microsoft Antimalware para Máquinas Virtuais](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmmicrosoftantimalwareextension?view=azuresmps-4.0.0)

- [Como configurar o Microsoft Antimalware para serviços em nuvem](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureserviceantimalwareextension?view=azuresmps-4.0.0)

- [Compreender o Antimalware da Microsoft](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

## <a name="29-enable-dns-query-logging"></a>2.9: Ativar a exploração de consultas dNS

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2.9 | 8.7 | Cliente |

Implemente uma solução de terceiros do Azure Marketplace para a solução de registo DNS de acordo com as suas organizações.  

## <a name="210-enable-command-line-audit-logging"></a>2.10: Ativar a exploração de auditoria da linha de comando

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2,10 | 8.8 | Cliente |

Utilize o Microsoft Monitoring Agent em todas as máquinas virtuais suportadas do Azure Windows para registar o evento de criação de processos e o campo CommandLine.   Para máquinas Bluee Linux virtuais suportadas, pode configurar manualmente o registo de consolas numa base por nó e utilizar o Syslog para armazenar os dados.  Além disso, utilize o espaço de trabalho do Azure Monitor's Log Analytics para rever os registos e realizar consultas em dados registados a partir de máquinas Azure Virtual. 

- [Data collection in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier) (Recolha de dados no Centro de Segurança do Azure)

- [Como realizar consultas personalizadas no Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

- [Syslog data sources in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-syslog) (Origens de dados de Syslog no Azure Monitor)


## <a name="next-steps"></a>Passos seguintes

- Consulte o próximo Controlo de Segurança: [Controlo de Identidade e Acesso](security-control-identity-access-control.md)