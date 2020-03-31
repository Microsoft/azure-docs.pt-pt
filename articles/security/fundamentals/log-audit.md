---
title: Exploração madeireira e auditoria de segurança azure Microsoft Docs
description: Conheça os registos disponíveis no Azure e os conhecimentos de segurança que pode obter.
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
ms.openlocfilehash: bd0f42507e22559690e2682a391c53b9c090aa6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750799"
---
# <a name="azure-security-logging-and-auditing"></a>Exploração madeireira e auditoria de segurança azure

O Azure fornece um vasto leque de opções de auditoria e registo de segurança configuráveis para ajudá-lo a identificar lacunas nas suas políticas e mecanismos de segurança. Este artigo discute a geração, recolha e análise de registos de segurança dos serviços hospedados no Azure.

> [!Note]
> Algumas recomendações neste artigo podem resultar num aumento de dados, rede ou utilização de recursos computacionais, e aumentar os custos de licença ou subscrição.

## <a name="types-of-logs-in-azure"></a>Tipos de registos em Azure

As aplicações em nuvem são complexas com muitas partes móveis. O registo de dados pode fornecer informações sobre as suas aplicações e ajudá-lo:

- Problemas de resolução de problemas passados ou prevenir potenciais
- Melhorar o desempenho da aplicação ou a manutenção
- Automatizar ações que de outra forma exigiriam intervenção manual

Os registos azure são categorizados nos seguintes tipos:
* **Os registos de controlo/gestão** fornecem informações sobre as operações do Gestor de Recursos Azure CREATE, UPDATE e DELETE. Para mais informações, consulte os registos de [atividade do Azure.](../../azure-monitor/platform/platform-logs-overview.md)

* **Os registos** de planos de dados fornecem informações sobre eventos levantados como parte do uso de recursos do Azure. Exemplos deste tipo de registo são o sistema de eventos do Windows, a segurança e os registos de aplicações numa máquina virtual (VM) e os [registos](../../azure-monitor/platform/platform-logs-overview.md) de diagnóstico que são configurados através do Monitor Azure.

* **Eventos processados** fornecem informações sobre eventos/alertas analisados que foram processados em seu nome. Exemplos deste tipo são alertas do [Azure Security Center](../../security-center/security-center-managing-and-responding-alerts.md) onde o [Azure Security Center](../../security-center/security-center-intro.md) processou e analisou a sua subscrição e fornece alertas de segurança concisos.

A tabela seguinte lista os tipos de registos mais importantes disponíveis no Azure:

| Categoria do registo | Tipo de log | Utilização | Integração |
| ------------ | -------- | ------ | ----------- |
|[Registos de atividade](../../azure-monitor/platform/platform-logs-overview.md)|Eventos de controle-avião sobre recursos do Gestor de Recursos Azure|  Fornece informações sobre as operações que foram realizadas sobre os recursos na sua subscrição.|    Rest API, [Monitor Azure](../../azure-monitor/platform/platform-logs-overview.md)|
|[Registos de Recursos Azure](../../azure-monitor/platform/platform-logs-overview.md)|Dados frequentes sobre o funcionamento dos recursos do Gestor de Recursos Azure na subscrição|   Fornece informações sobre as operações que o seu próprio recurso realizou.| Azure Monitor|
|[Relatórios do Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md)|Registos e relatórios | Reporta atividades de registo do utilizador e informações sobre a atividade do sistema sobre utilizadores e gestão de grupos.|[Gráfico API](../../active-directory/develop/active-directory-graph-api-quickstart.md)|
|[Máquinas virtuais e serviços em nuvem](../../azure-monitor/learn/quick-collect-azurevm.md)|Serviço de Log de Eventos do Windows e Syslog Linux|  Captura dados do sistema e regista dados nas máquinas virtuais e transfere esses dados para uma conta de armazenamento à sua escolha.|   Windows (utilizando o Armazenamento de Diagnósticos Windows Azure [[WAD]](../../monitoring-and-diagnostics/azure-diagnostics.md)e Linux no Monitor Azure|
|[Análise de Armazenamento Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|Armazenamento de registo, fornece dados métricos para uma conta de armazenamento|Fornece informações sobre pedidos de rastreio, analisa tendências de utilização e diagnostica problemas com a sua conta de armazenamento.|   REST API ou a biblioteca de [clientes](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[Registos de fluxo do grupo de segurança da rede (NSG)](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)|Formato JSON, mostra fluxos de saída e de entrada por regra|Exibe informações sobre o tráfego IP de ingresse e desvia através de um Grupo de Segurança de Rede.|[Observador de Rede do Azure](../../network-watcher/network-watcher-monitoring-overview.md)|
|[Insight de aplicação](../../azure-monitor/app/app-insights-overview.md)|Registos, exceções e diagnósticos personalizados|  Fornece um serviço de monitorização do desempenho da aplicação (APM) para desenvolvedores web em várias plataformas.| REST API, [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|[Processar dados / alertas de segurança](../../security-center/security-center-intro.md)|  Alertas do Centro de Segurança Azure, alertas do Monitor Azure|    Fornece informações de segurança e alertas.|  REST APIs, JSON|

## <a name="log-integration-with-on-premises-siem-systems"></a>Integração de registo com sistemas SIEM no local
[A integração](../../security-center/security-center-export-data-to-siem.md) de alertas do Security Center discute como sincronizar alertas do Security Center, eventos de segurança de máquinas virtuais recolhidos por registos de diagnóstico sinuoso do Azure e registos de auditoria azure com os seus registos do Monitor Azure ou solução SIEM.

## <a name="next-steps"></a>Passos seguintes

- [Auditoria e exploração madeireira](management-monitoring-overview.md): Proteja os dados mantendo a visibilidade e respondendo rapidamente a alertas de segurança atempadamente.

- Recolha de registos de registo de segurança e auditoria dentro do [Azure](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/): Faça valer estas definições para garantir que as suas instâncias Azure estão a recolher os registos de segurança e auditoria corretos.

- [Configure as definições](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US)de auditoria para uma recolha do site : Se for um administrador de recolha de sites, recupere o histórico das ações dos utilizadores individuais e o histórico de ações tomadas durante uma determinada gama de datas.

- [Consulte o registo de auditoria no Office 365 Security & Compliance Center](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US): Utilize o Office 365 Security & Compliance Center para pesquisar o registo de auditoria unificado e ver a atividade do utilizador e administrador na sua organização office 365.
