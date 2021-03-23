---
title: Referência de esquema de fonte de dados Azure Sentinel
description: Este artigo lista o Azure e os esquemas de fonte de dados de terceiros apoiados por Azure Sentinel, com ligações à sua documentação de referência.
author: batamig
ms.author: bagol
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: reference
ms.custom: ''
ms.date: 01/14/2021
ms.openlocfilehash: 84d5f1e4035fb730d33b7bc8dd375273610f0bfb
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771945"
---
# <a name="data-source-schema-reference"></a>Referência de esquema de fonte de dados

Este artigo lista o suporte do Azure e de esquemas de fonte de dados de terceiros, com ligações à sua documentação de referência.

## <a name="azure-data-sources"></a>Origens de dados do Azure

| Tipo                             | Origem de dados             | Registar Nome de mesa Do Registo | Referência de esquema |
| -------------------------------- | ---------------------- | ---------------------- | ---------------- |
| **Azure**                            | Azure Active Directory | SigninEvents           | [A atividade da AD AD informa propriedades de inscrição](/graph/api/resources/signin#properties) |
| **Azure**                            | Azure Active Directory | Auditorias              | [Referência de Auditoria do Monitor Azure](/azure/azure-monitor/reference/tables/auditlogs) |
| **Azure**                            | Azure Active Directory | AzureActivity          | [Referência Azure Monitor AzureActivity](/azure/azure-monitor/reference/tables/azureactivity) |
| **Azure**                            | Office                 | OfficeActivity         | Esquemas de API de Atividade de Gestão 365: <br>- [Esquema comum ](/office/office-365-management-api/office-365-management-activity-api-schema#common-schema)   <br>- [Troca de Admin ](/office/office-365-management-api/office-365-management-activity-api-schema#exchange-admin-schema) <br>- [Troca de caixas de correio](/office/office-365-management-api/office-365-management-activity-api-schema#exchange-mailbox-schema)  <br>- [Esquema base sharePoint](/office/office-365-management-api/office-365-management-activity-api-schema#sharepoint-base-schema)   <br>- [Operações de ficheiros SharePoint](/office/office-365-management-api/office-365-management-activity-api-schema#sharepoint-file-operations) |
| **Azure**                            | Azure Key Vault         | AzureDiagnostics       | [Referência Azure Monitor AzureDiagnostics](/azure/azure-monitor/reference/tables/azurediagnostics) |
| **Anfitrião**                             | Linux                  | Syslog                 | [Referência Syslog monitor Azure Monitor](/azure/azure-monitor/reference/tables/syslog) |
| **Rede**                          | Registos do IIS               | W3CIISLog              | [Referência Azure Monitor W3CIISLog](/azure/azure-monitor/reference/tables/w3ciislog) |
| **Rede**                          | VMinsights             | VMConnection           | [Referência VMConnection monitor Azure Monitor](/azure/azure-monitor/reference/tables/vmconnection) |
| **Rede**                          | Solução de dados de fio     | WireData               | [Referência de WireData do Monitor Azure](/azure/azure-monitor/reference/tables/wiredata) |
| **Rede**                          | Registos de fluxo NSG          | AzureNetworkAnalytics  | [Schema e agregação de dados em Análise de Tráfego](../network-watcher/traffic-analytics-schema.md) |
| | | | |

> [!NOTE]
> Para mais informações, consulte toda a referência de dados do [Azure Monitor](/azure/azure-monitor/reference/).
>
## <a name="3rd-party-vendor-data-sources"></a>Fontes de dados de fornecedores de terceiros

As listas de tabelas que se seguem suportam fornecedores de terceiros e a sua documentação de mapeamento de Syslog ou Common Event Format (CEF) para vários tipos de registos suportados, que contêm mapeamentos de campo CEF e registos de amostras para cada tipo de categoria.

| Tipo |    Fornecedor |    Produto | Registar Nome de mesa Do Registo | Referência de mapeamento de campo CEF  |
| ----- | ----- | ----- | ----- |----- |
| **Rede** | Palo Alto   | PAN OS    | CommonSecurityLog |   [Guia comum de integração do formato de evento PAN-OS 9.0](https://docs.paloaltonetworks.com/content/dam/techdocs/en_US/pdf/cef/pan-os-90-cef-configuration-guide.pdf) (pesquisa por *formatos de registo ao estilo CEF)* |
| **Rede** | Check Point  |ALL   | CommonSecurityLog | [Descrição dos campos de registo](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk109795)       |
| **Rede** | Fortigate   | ALL   | CommonSecurityLog | [Estrutura de Esquema de Log](https://docs.fortinet.com/document/fortigate/6.2.3/fortios-log-message-reference/738142/log-schema-structure)         |
| **Rede** | Barracuda | Firewall de Aplicações Web |  CommonSecurityLog   | [Como configurar syslog e outros registos](https://campus.barracuda.com/product/webapplicationfirewall/doc/4259935/how-to-configure-syslog-and-other-logs/)  |
| **Rede** | Cisco | ASA | CommonSecurityLog | [Cisco ASA Series Syslog Messages](https://www.cisco.com/c/en/us/td/docs/security/asa/syslog/b_syslog/about.html)    |
| **Rede** | Cisco | Poder de fogo   | CommonSecurityLog | [Cisco Firepower Threat Defense Syslog Mensagens](https://www.cisco.com/c/en/us/td/docs/security/firepower/Syslogs/b_fptd_syslog_guide.pdf)    |
| **Rede** | Cisco   | Guarda-chuva  | Tabela de registos personalizados  | [Formatos de registo e versão](https://docs.umbrella.com/deployment-umbrella/docs/log-formats-and-versioning)   |
| **Rede**   | Cisco | Meraki    | CommonSecurityLog |   [Tipos de eventos syslog e amostras de registo](https://documentation.meraki.com/zGeneral_Administration/Monitoring_and_Reporting/Syslog_Event_Types_and_Log_Samples)    |
| **Rede**   | Zscaler | Serviço de streaming Nano (NSS)|   CommonSecurityLog | [Formatação NSS Feeds (apenas](https://help.zscaler.com/zia/documentation-knowledgebase/analytics/nss/nss-feeds/formatting-nss-feeds) web, firewall, DNS e registos de túneis) |
| **Rede**   |F5 | BigIP LTM|    CommonSecurityLog|  [Mensagens de evento e tipos de ataque](https://techdocs.f5.com/kb/en-us/products/big-ip_ltm/manuals/product/bigip-external-monitoring-implementations-13-0-0/15.html)  |
| **Rede** | F5  | BigIP ASM|    CommonSecurityLog|  [Eventos de segurança de aplicações de registo](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-13-1-0/14.html)                                                           |
| **Rede** | Citrix  |Firewall de aplicativo web   | CommonSecurityLog|    [Suporte de registo de formato de evento comum (CEF) na Firewall de aplicação](https://support.citrix.com/article/CTX136146) <br>  [Referência de mensagem de syslog NetScaler 12.0](https://developer-docs.citrix.com/projects/netscaler-syslog-message-reference/en/12.0/)   |
|**Anfitrião** |Symantec | Symantec Endpoint Protection Manager (SEPM) | CommonSecurityLog|[Definições externas de registo e níveis de gravidade do evento de registo para o Gestor de Proteção de Pontos Finais](https://support.symantec.com/us/en/article.tech171741.html)|
|**Anfitrião** |Tendência Micro |Todos |CommonSecurityLog | [Syslog Content Mapping - CEF](https://docs.trendmicro.com/en-us/enterprise/control-manager-70/appendices/syslog-mapping-cef.aspx) |
| | | | | |

> [!NOTE]
> Para obter mais informações, consulte também [o mapeamento de campo CEF e CommonSecurityLog.](cef-name-mapping.md)
> 
## <a name="next-steps"></a>Passos seguintes

Saiba conectores Azure Sentinel mais suportados, tais como CEF, Syslog, direct, agente e conectores personalizados:

- [Ligar a origens de dados](connect-data-sources.md)

- [Azure Sentinel Syslog, CEF e outros conectores de terceiros](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-syslog-cef-and-other-3rd-party-connectors-grand/ba-p/803891)