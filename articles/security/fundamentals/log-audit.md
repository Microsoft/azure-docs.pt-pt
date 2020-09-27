---
title: Registo e auditoria de segurança Azure Microsoft Docs
description: Conheça os registos disponíveis no Azure e as informações de segurança que pode obter.
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
ms.openlocfilehash: c5ac9daeb741d400a759603c7a3e3e462cc9294f
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2020
ms.locfileid: "91398874"
---
# <a name="azure-security-logging-and-auditing"></a>Azure security logging and auditing (Registo e auditoria de segurança do Azure)

O Azure fornece um vasto leque de opções de auditoria e registo de segurança configuráveis para ajudá-lo a identificar lacunas nas suas políticas e mecanismos de segurança. Este artigo discute a geração, recolha e análise de registos de segurança de serviços hospedados no Azure.

> [!Note]
> Algumas recomendações neste artigo podem resultar em aumento de dados, rede ou utilização de recursos computativos, e aumentar os custos de licença ou subscrição.

## <a name="types-of-logs-in-azure"></a>Tipos de troncos em Azure

As aplicações em nuvem são complexas com muitas partes móveis. Os dados de registo podem fornecer informações sobre as suas aplicações e ajudá-lo:

- Resolver problemas passados ou prevenir potenciais
- Melhorar o desempenho da aplicação ou a manutenção
- Automatizar ações que de outra forma exigiriam intervenção manual

Os registos Azure são categorizados nos seguintes tipos:
* **Os registos de controlo/gestão** fornecem informações sobre as operações de CRIAÇÃO, ATUALIZAÇÃO e DELETE do Gestor de Recursos Azure. Para obter mais informações, consulte [os registos de atividade do Azure](../../azure-monitor/platform/platform-logs-overview.md).

* **Os registos de planos de dados** fornecem informações sobre os eventos angariados como parte do uso de recursos Azure. Exemplos deste tipo de registo são o sistema de eventos Windows, segurança e registos de aplicações numa máquina virtual (VM) e os [registos](../../azure-monitor/platform/platform-logs-overview.md) de diagnóstico que são configurados através do Azure Monitor.

* **Eventos processados** fornecem informações sobre eventos/alertas analisados que foram processados em seu nome. Exemplos deste tipo são alertas do [Azure Security Center](../../security-center/security-center-managing-and-responding-alerts.md) onde o [Azure Security Center](../../security-center/security-center-intro.md) processou e analisou a sua subscrição e fornece alertas de segurança concisos.

A tabela que se segue lista os tipos mais importantes de registos disponíveis no Azure:

| Categoria do registo | Tipo de registo | Utilização | Integração |
| ------------ | -------- | ------ | ----------- |
|[Troncos de atividade](../../azure-monitor/platform/platform-logs-overview.md)|Eventos de control-plane nos recursos do Gestor de Recursos da Azure|  Fornece informações sobre as operações que foram realizadas sobre os recursos na sua subscrição.|    Rest API, [Monitor Azure](../../azure-monitor/platform/platform-logs-overview.md)|
|[Registos de recursos Azure](../../azure-monitor/platform/platform-logs-overview.md)|Dados frequentes sobre o funcionamento dos recursos do Gestor de Recursos Azure em subscrição|   Fornece informações sobre as operações que o seu próprio recurso realizou.| Azure Monitor|
|[Relatórios do Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md)|Registos e relatórios | Reporta atividades de inscrição do utilizador e informações de atividade do sistema sobre utilizadores e gestão de grupos.|[Graph API](../../active-directory/develop/active-directory-graph-api-quickstart.md)|
|[Máquinas virtuais e serviços na nuvem](../../azure-monitor/learn/quick-collect-azurevm.md)|Serviço de Registo de Eventos do Windows e Syslog Linux|  Captura dados do sistema e regista dados nas máquinas virtuais e transfere esses dados para uma conta de armazenamento à sua escolha.|   Windows (utilizando o armazenamento do Windows Azure Diagnostics[[WAD]](../../monitoring-and-diagnostics/azure-diagnostics.md)e o Linux no Azure Monitor|
|[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|Registo de armazenamento, fornece dados de métricas para uma conta de armazenamento|Fornece informações sobre pedidos de rastreio, analisa tendências de utilização e diagnostica problemas com a sua conta de armazenamento.|   REST API ou a biblioteca do [cliente](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[Registos de fluxo do grupo de segurança da rede (NSG)](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)|Formato JSON, mostra fluxos de saída e de entrada numa base por regra|Exibe informações sobre o tráfego IP de entrada e saída através de um Grupo de Segurança de Rede.|[Observador de Rede do Azure](../../network-watcher/network-watcher-monitoring-overview.md)|
|[Insight de aplicação](../../azure-monitor/app/app-insights-overview.md)|Registos, exceções e diagnósticos personalizados|  Fornece um serviço de monitorização do desempenho da aplicação (APM) para desenvolvedores web em várias plataformas.| REST API, [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|[Dados do processo / alertas de segurança](../../security-center/security-center-intro.md)|  Alertas do Centro de Segurança Azure, alertas de registos do Azure Monitor|    Fornece informações de segurança e alertas.|  REST APIs, JSON|

## <a name="log-integration-with-on-premises-siem-systems"></a>Integração de registos com sistemas SIEM no local
[Integrar alertas do Security Center](../../security-center/security-center-export-data-to-siem.md) discute como sincronizar os alertas do Security Center, eventos de segurança de máquinas virtuais recolhidos por registos de diagnóstico azure e registos de auditoria do Azure com os registos do Azure Monitor ou solução SIEM.

## <a name="next-steps"></a>Passos seguintes

- [Auditoria e registo:](management-monitoring-overview.md)Proteja os dados mantendo a visibilidade e respondendo rapidamente a alertas de segurança oportunos.

- [Registo de segurança e recolha de registos de auditoria dentro do Azure](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/): Aplique estas definições para garantir que as suas instâncias Azure estão a recolher os registos de segurança e auditoria corretos.

- [Configurar as definições](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US)de auditoria para uma recolha do site : Se for um administrador de recolha de sites, recupere o histórico das ações individuais dos utilizadores e o histórico de ações realizadas durante um intervalo de datas específicas.

- [Pesta no registo de auditoria no centro de segurança Microsoft 365](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance): Utilize o centro de segurança Microsoft 365 para pesquisar o registo de auditoria unificado e ver a atividade do utilizador e administrador na sua organização.
