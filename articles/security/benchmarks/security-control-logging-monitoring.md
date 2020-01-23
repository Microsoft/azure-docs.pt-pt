---
title: Controle de segurança do Azure-registro em log e monitoramento
description: Monitoramento e registro em log de controle de segurança
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: ae9c678d9dfca895ec74ed92bcb1b541db6b134e
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76545505"
---
# <a name="security-control-logging-and-monitoring"></a>Controle de segurança: registro em log e monitoramento

O monitoramento e o log de segurança se concentram em atividades relacionadas à habilitação, à aquisição e ao armazenamento de logs de auditoria para os serviços do Azure.

## <a name="21-use-approved-time-synchronization-sources"></a>2,1: usar fontes de sincronização de tempo aprovadas

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 2.1 | 6.1 | Microsoft |

No entanto, a Microsoft mantém fontes de tempo para recursos do Azure. você tem a opção de gerenciar as configurações de sincronização de tempo para seus recursos de computação.

Como configurar a sincronização de horário para recursos de computação do Azure:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

## <a name="22-configure-central-security-log-management"></a>2,2: configurar o gerenciamento do log de segurança central

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 2.2 | 6,5, 6,6 | Cliente |

Os logs de ingestão por meio de Azure Monitor para agregar dados de segurança gerados por dispositivos de ponto de extremidade, recursos de rede e outros sistemas de segurança. Em Azure Monitor, use Log Analytics espaços de trabalho para consultar e executar análises e use contas de armazenamento do Azure para armazenamento de longo prazo/arquivamento.

Como alternativa, você pode habilitar e dados integrados para o Azure Sentinel ou um SIEM de terceiros. Como carregar o Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Como coletar logs e métricas de plataforma com o Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Como coletar logs de host interno da máquina virtual do Azure com o Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm

Como começar a usar Azure Monitor e integração de SIEM de terceiros:

https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/

## <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: habilitar o log de auditoria para recursos do Azure

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 2.3 | 6,2, 6,3 | Cliente |

Habilite as configurações de diagnóstico nos recursos do Azure para acessar logs de auditoria, segurança e diagnóstico. Logs de atividade, que estão disponíveis automaticamente, incluem origem do evento, data, usuário, carimbo de data/hora, endereços de origem, endereços de destino e outros elementos úteis.

Como coletar logs e métricas de plataforma com o Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Entender o registro em log e diferentes tipos de log no Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

## <a name="24-collect-security-logs-from-operating-systems"></a>2,4: coletar logs de segurança de sistemas operacionais

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 2.4 | 6,2, 6,3 | Cliente |

Se o recurso de computação pertence à Microsoft, a Microsoft é responsável por monitorá-lo. Se o recurso de computação pertence à sua organização, é sua responsabilidade monitorá-lo. Você pode usar a central de segurança do Azure para monitorar o sistema operacional. Os dados coletados pela central de segurança do sistema operacional incluem o tipo e a versão do so, logs do sistema operacional (logs de eventos do Windows), processos em execução, nome do computador, endereços IP e usuário conectado. O agente de Log Analytics também coleta arquivos de despejo de memória.

Como coletar logs de host interno da máquina virtual do Azure com o Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm

Entender a coleta de dados da central de segurança do Azure:

https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection

## <a name="25-configure-security-log-storage-retention"></a>2,5: configurar retenção de armazenamento de log de segurança

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 2.5 | 6.4 | Cliente |

Em Azure Monitor, defina seu período de retenção de espaço de trabalho de Log Analytics de acordo com os regulamentos de conformidade de sua organização. Use contas de armazenamento do Azure para armazenamento de longo prazo/arquivamento.

Como definir parâmetros de retenção de log para Log Analytics espaços de trabalho:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

## <a name="26-monitor-and-review-logs"></a>2,6: monitorar e examinar os logs

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 2,6 | 6.7 | Cliente |

Analise e monitore logs de comportamento anormal e Examine regularmente os resultados. Use o espaço de trabalho Log Analytics do Azure Monitor para examinar os logs e executar consultas nos dados de log.

Como alternativa, você pode habilitar e dados integrados para o Azure Sentinel ou um SIEM de terceiros. 

Como carregar o Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Entender Log Analytics espaço de trabalho:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Como executar consultas personalizadas no Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

## <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: habilitar alertas para atividade anômala

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 2.7 | 6.8 | Cliente |

Use a central de segurança do Azure com Log Analytics espaço de trabalho para monitoramento e alertas em atividade anômala encontrada em eventos e logs de segurança.

Como alternativa, você pode habilitar e dados integrados para o Azure Sentinel.

Como carregar o Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Como gerenciar alertas na central de segurança do Azure:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Como alertar sobre os dados de log do log Analytics:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

## <a name="28-centralize-anti-malware-logging"></a>2,8: centralizar o registro em log de anti-malware

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 2.8 | 8,6 | Cliente |

Habilite a coleta de eventos antimalware para máquinas virtuais e serviços de nuvem do Azure.

Como configurar o Microsoft antimalware para máquinas virtuais:

https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmmicrosoftantimalwareextension?view=azuresmps-4.0.0

Como configurar o Microsoft antimalware para serviços de nuvem:

https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureserviceantimalwareextension?view=azuresmps-4.0.0

Entender o Microsoft AntiMalware:

https://docs.microsoft.com/azure/security/fundamentals/antimalware

## <a name="29-enable-dns-query-logging"></a>2,9: habilitar o log de consultas DNS

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 2.9 | 8.7 | Cliente |

Implemente uma solução de terceiros para registro em log DNS.

## <a name="210-enable-command-line-audit-logging"></a>2,10: habilitar o log de auditoria de linha de comando

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 2.1 | 8.8 | Cliente |

Configure manualmente o log do console e a transcrição do PowerShell em uma base por nó.

## <a name="next-steps"></a>Passos seguintes

Consulte o próximo controle de segurança: [identidade e controle de acesso](security-control-identity-access-control.md)
