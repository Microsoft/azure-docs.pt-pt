---
title: Controlo de Segurança Azure - Registo e Monitorização
description: Registo e monitorização do controlo de segurança do Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: ccfbb585ccf50366721925b0b31b17088fd36371
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102612427"
---
# <a name="security-control-logging-and-monitoring"></a>Controlo de Segurança: Registo e Monitorização

O registo e monitorização de segurança centra-se em atividades relacionadas com a ativação, aquisição e armazenamento de registos de auditoria para os serviços Azure.

## <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizar fontes de sincronização de tempo aprovadas

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2.1 | 6.1 | Microsoft |

A Microsoft mantém fontes de tempo para recursos Azure, no entanto, tem a opção de gerir as definições de sincronização de tempo para os seus recursos computativos.

- [Como configurar a sincronização do tempo para os recursos computativos do Azure Windows](../../virtual-machines/windows/time-sync.md)

- [Como configurar a sincronização temporal para os recursos computativos Azure Linux](../../virtual-machines/linux/time-sync.md)

## <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2,2 | 6.5, 6.6 | Cliente |

Ingerir registos via Azure Monitor para agregar dados de segurança gerados por dispositivos de ponto final, recursos de rede e outros sistemas de segurança. Dentro do Azure Monitor, utilize o Log Analytics Workspace(s) para consultar e realizar análises, e utilize contas de armazenamento Azure para armazenamento a longo prazo/arquivo.

Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros. 

- [Como embarcar Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md)

- [Como recolher registos internos de anfitriões da Azure Virtual Machine com o Azure Monitor](../../azure-monitor/vm/quick-collect-azurevm.md)

- [Como começar com o Azure Monitor e a integração do SIEM de terceiros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

## <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2.3 | 6.2, 6.3 | Cliente |

Ativar Definições de Diagnóstico em recursos Azure para acesso a registos de auditoria, segurança e diagnóstico. Os registos de atividade, que estão automaticamente disponíveis, incluem fonte de evento, data, utilizador, marcação de tempo, endereços de origem, endereços de destino e outros elementos úteis.

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md)

- [Compreenda o registo e diferentes tipos de registo em Azure](../../azure-monitor/essentials/platform-logs-overview.md)

## <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2,4 | 6.2, 6.3 | Cliente |

Se o recurso computacional for propriedade da Microsoft, então a Microsoft é responsável pela monitorização do mesmo. Se o recurso computacional é propriedade da sua organização, é da sua responsabilidade monitorizá-lo. Pode utilizar o Centro de Segurança Azure para monitorizar o SISTEMA. Os dados recolhidos pelo Security Center do sistema operativo incluem o tipo e versão de SISTEMA, OS (Registos de Eventos do Windows), processos de execução, nome da máquina, endereços IP e registados no utilizador. O Agente Descodudo de Log também recolhe ficheiros de despejo de acidentes.

- [Como recolher registos internos de anfitriões da Azure Virtual Machine com o Azure Monitor](../../azure-monitor/vm/quick-collect-azurevm.md)

- [Compreender a recolha de dados do Centro de Segurança Azure](../../security-center/security-center-enable-data-collection.md)

## <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2.5 | 6.4 | Cliente |

Dentro do Azure Monitor, desa um período de retenção do espaço de trabalho do Log Analytics de acordo com os regulamentos de conformidade da sua organização. Utilize contas de armazenamento Azure para armazenamento a longo prazo/arquivo.

- [Alterar o período de retenção de dados em Log Analytics](../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Como configurar a política de retenção para os registos de conta de armazenamento Azure](../../storage/common/manage-storage-analytics-logs.md#configure-logging)

## <a name="26-monitor-and-review-logs"></a>2.6: Monitor e revisão de Registos

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2,6 | 6.7 | Cliente |

Analise e monitorize registos para comportamento anómalo e reveja regularmente os resultados. Utilize o log analytics workspace do Azure Monitor para rever registos e efetuar consultas nos dados de registo.

Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros. 

- [Como embarcar Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [Compreender log analytics workspace](../../azure-monitor/logs/log-analytics-tutorial.md)

- [Como realizar consultas personalizadas no Azure Monitor](../../azure-monitor/logs/get-started-queries.md)

## <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2.7 | 6.8 | Cliente |

Utilize o Centro de Segurança Azure com log analytics workspace para monitorizar e alertar sobre a atividade anómala encontrada em registos de segurança e eventos.

Em alternativa, pode ativar e a bordo dados para a Azure Sentinel.

- [Como embarcar Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [Como gerir alertas no Centro de Segurança Azure](../../security-center/security-center-managing-and-responding-alerts.md)

- [Como alertar nos dados de registo de registo de registos de registos](../../azure-monitor/alerts/tutorial-response.md)

## <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a sessão anti-malware

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2,8 | 8.6 | Cliente |

Ativar a recolha de eventos antimalware para máquinas virtuais Azure e Serviços cloud.

- [Como configurar o Microsoft Antimalware para Máquinas Virtuais](/powershell/module/servicemanagement/azure.service/set-azurevmmicrosoftantimalwareextension)

- [Como configurar o Microsoft Antimalware para serviços cloud](/powershell/module/servicemanagement/azure.service/set-azureserviceantimalwareextension)

- [Compreenda o Antimalware da Microsoft](../fundamentals/antimalware.md)

## <a name="29-enable-dns-query-logging"></a>2.9: Ativar a sessão de consulta de DNS

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2.9 | 8.7 | Cliente |

Implemente uma solução de terceiros a partir do Azure Marketplace para a solução de registo de DNS conforme as suas organizações precisam.  

## <a name="210-enable-command-line-audit-logging"></a>2.10: Permitir a exploração de auditoria de linha de comando

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 2,10 | 8.8 | Cliente |

Utilize o Agente de Monitorização do Microsoft em todas as máquinas virtuais suportadas do Azure Windows para registar o evento de criação de processos e o campo CommandLine.   Para máquinas virtuais suportadas Azure Linux, pode configurar manualmente o registo de consolas por nó e utilizar o Syslog para armazenar os dados.  Além disso, utilize o espaço de trabalho do Log Analytics do Azure Monitor para rever registos e realizar consultas em dados registados a partir de máquinas Virtuais Azure. 

- [Data collection in Azure Security Center](../../security-center/security-center-enable-data-collection.md#data-collection-tier) (Recolha de dados no Centro de Segurança do Azure)

- [Como realizar consultas personalizadas no Azure Monitor](../../azure-monitor/logs/get-started-queries.md)

- [Syslog data sources in Azure Monitor](../../azure-monitor/agents/data-sources-syslog.md) (Origens de dados de Syslog no Azure Monitor)


## <a name="next-steps"></a>Passos seguintes

- Ver o próximo Controlo de Segurança: [Identidade e Controlo de Acesso](security-control-identity-access-control.md)