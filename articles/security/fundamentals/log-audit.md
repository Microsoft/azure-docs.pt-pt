---
title: Auditoria e log de segurança do Azure | Microsoft Docs
description: Saiba mais sobre os logs disponíveis no Azure e as informações de segurança que você pode obter.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2019
ms.author: terrylan
ms.openlocfilehash: f53c7e7b045805a28223fb5670afc45c980454bc
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498399"
---
# <a name="azure-security-logging-and-auditing"></a>Auditoria e log de segurança do Azure

O Azure fornece uma ampla variedade de opções configuráveis de auditoria e log de segurança para ajudá-lo a identificar lacunas em seus mecanismos e políticas de segurança. Este artigo aborda a geração, coleta e análise de logs de segurança de serviços hospedados no Azure.

> [!Note]
> Determinadas recomendações neste artigo podem resultar em maior uso de recursos de computação, rede ou dados e aumentar os custos de licença ou assinatura.

## <a name="types-of-logs-in-azure"></a>Tipos de logs no Azure

Os aplicativos de nuvem são complexos com muitas partes móveis. Os dados de log podem fornecer informações sobre seus aplicativos e ajudá-lo a:

- Solucionar problemas antigos ou evitar possíveis
- Melhorar o desempenho ou a facilidade de manutenção do aplicativo
- Automatizar ações que, de outra forma, exigirão intervenção manual

Os logs do Azure são categorizados nos seguintes tipos:
* **Os logs de controle/gerenciamento** fornecem informações sobre Azure Resource Manager criar, atualizar e excluir operações. Para obter mais informações, consulte [logs de atividades do Azure](../../azure-monitor/platform/activity-logs-overview.md).

* **Os logs do plano de dados** fornecem informações sobre eventos gerados como parte do uso de recursos do Azure. Exemplos desse tipo de log são os logs de aplicativo, segurança e sistema de eventos do Windows em uma VM (máquina virtual) e os [logs de diagnóstico](../../azure-monitor/platform/resource-logs-overview.md) configurados por meio de Azure monitor.

* Os **eventos processados** fornecem informações sobre eventos/alertas analisados que foram processados em seu nome. Exemplos desse tipo são [alertas da central de segurança do Azure](../../security-center/security-center-managing-and-responding-alerts.md) , em que a [central de segurança do Azure](../../security-center/security-center-intro.md) processou e analisou sua assinatura e fornece alertas de segurança concisos.

A tabela a seguir lista os tipos mais importantes de logs disponíveis no Azure:

| Categoria do registo | Tipo de log | Utilização | Integração |
| ------------ | -------- | ------ | ----------- |
|[Logs de atividade](../../azure-monitor/platform/activity-logs-overview.md)|Eventos de plano de controle em recursos de Azure Resource Manager|  Fornece informações sobre as operações que foram realizadas em recursos em sua assinatura.|    API REST, [Azure monitor](../../azure-monitor/platform/activity-logs-overview.md)|
|[Logs de recursos do Azure](../../azure-monitor/platform/resource-logs-overview.md)|Dados frequentes sobre a operação de Azure Resource Manager recursos na assinatura|   Fornece informações sobre as operações que seu próprio recurso realizou.| Azure Monitor|
|[Relatórios de Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md)|Logs e relatórios | Relata as atividades de entrada do usuário e as informações de atividade do sistema sobre o gerenciamento de usuários e grupos.|[Graph API](../../active-directory/develop/active-directory-graph-api-quickstart.md)|
|[Máquinas virtuais e serviços de nuvem](../../azure-monitor/learn/quick-collect-azurevm.md)|Serviço log de eventos do Windows e syslog do Linux|  Captura dados do sistema e dados de registro em log nas máquinas virtuais e transfere esses dados para uma conta de armazenamento de sua escolha.|   Windows (usando o Windows Diagnóstico do Azure [[wad](../../monitoring-and-diagnostics/azure-diagnostics.md)] Storage) e Linux no Azure monitor|
|[Análise do Armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|Log de armazenamento, fornece dados de métricas para uma conta de armazenamento|Fornece informações sobre as solicitações de rastreamento, analisa tendências de uso e diagnostica problemas com sua conta de armazenamento.|   API REST ou a [biblioteca de cliente](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[Logs de fluxo do NSG (grupo de segurança de rede)](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)|O formato JSON mostra os fluxos de entrada e saída por regra|Exibe informações sobre o tráfego IP de entrada e saída por meio de um grupo de segurança de rede.|[Observador de Rede do Azure](../../network-watcher/network-watcher-monitoring-overview.md)|
|[Application insights](../../azure-monitor/app/app-insights-overview.md)|Logs, exceções e diagnósticos personalizados|  Fornece um serviço de monitoramento do desempenho de aplicativos (APM) para desenvolvedores da Web em várias plataformas.| API REST, [Power bi](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|[Processar dados/alertas de segurança](../../security-center/security-center-intro.md)|  Alertas da central de segurança do Azure, alertas de logs de Azure Monitor|    Fornece informações de segurança e alertas.|  APIs REST, JSON|

## <a name="log-integration-with-on-premises-siem-systems"></a>Integração de log com sistemas SIEM locais
A [integração de alertas da central de segurança](../../security-center/security-center-export-data-to-siem.md) discute como sincronizar alertas da central de segurança, os eventos de segurança de máquina virtual coletados pelos logs de diagnóstico do Azure e os logs de auditoria do Azure com seus logs de Azure monitor ou solução Siem.

## <a name="next-steps"></a>Passos seguintes

- [Auditoria e registro em log](management-monitoring-overview.md): Proteja os dados mantendo a visibilidade e respondendo rapidamente aos alertas de segurança oportunos.

- [Log de segurança e coleta de log de auditoria no Azure](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/): aplique essas configurações para garantir que suas instâncias do Azure estejam coletando os logs de auditoria e segurança corretos.

- [Definir configurações de auditoria para um conjunto de sites](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US): se você for um administrador de conjunto de sites, recupere o histórico de ações de usuários individuais e o histórico de ações executadas durante um determinado intervalo de datas.

- [Pesquisar o log de auditoria no centro de conformidade e segurança do office 365](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US): Use o centro de conformidade e segurança do Office 365 para pesquisar o log de auditoria unificado e exibir a atividade de usuário e administrador na sua organização do Office 365.
