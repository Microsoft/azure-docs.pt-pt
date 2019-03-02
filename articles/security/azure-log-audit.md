---
title: Azure Registro e auditoria | Documentos da Microsoft
description: Saiba mais sobre como pode utilizar dados de log para obter informações aprofundadas sobre a sua aplicação.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/14/2019
ms.author: TomSh
ms.openlocfilehash: eb2f42b690099002f2f14aa4e782906a76c01d4c
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57241515"
---
# <a name="azure-logging-and-auditing"></a>Registo e auditoria do Azure

O Azure oferece um vasto leque de segurança configuráveis, auditoria e registo de opções para o ajudar a identificar lacunas na sua mecanismos e as políticas de segurança. Este artigo aborda a gerar, recolher e analisar registos de segurança dos serviços alojados no Azure.

> [!Note]
> Algumas recomendações neste artigo podem resultar em maior de dados, a rede ou a utilização de recursos de computação e aumentar os custos de licenciamento ou de subscrição.

## <a name="types-of-logs-in-azure"></a>Tipos de registos no Azure

Aplicações na cloud são complexas, com muitas partes móveis. Registos de fornecem dados para ajudar a manter seus aplicativos em funcionamento. Registos de ajudar a resolver problemas anteriores problemas ou impedir a potencial aqueles. E podem ajudar a melhorar o desempenho da aplicação ou a capacidade de manutenção, ou automatize ações que caso contrário, requer intervenção manual.

Registos do Azure são categorizados nos seguintes tipos:
* **Registos de controlo/gestão** fornecem informações sobre as operações do Azure Resource Manager criar, ATUALIZAR e eliminar. Para obter mais informações, consulte [registos de atividades do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs).

* **Registos do plano de dados** fornecem informações sobre eventos gerados como parte de utilização do recurso do Azure. Os exemplos deste tipo de registo são o sistema de eventos do Windows, segurança, e o aplicativo registra em log numa máquina virtual (VM) e o [registos de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) que são configuradas através do Azure Monitor.

* **Processar eventos** fornecem informações sobre analisadas eventos/alertas que foram processados em seu nome. Os exemplos deste tipo são [alertas do Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) onde [Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) tem processados e analisados a sua subscrição e fornece alertas de segurança concisa.

A tabela seguinte lista os tipos mais importantes de registos disponíveis no Azure:

| Categoria do registo | Tipo de registo | Utilização | Integração |
| ------------ | -------- | ------ | ----------- |
|[Registos de atividades](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|Eventos de plano de controlo de recursos do Azure Resource Manager|   Fornece informações sobre as operações executadas nos recursos na sua subscrição.|    REST API, [Monitor do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|
|[Registos de diagnóstico do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|Dados frequentes sobre o funcionamento de recursos do Azure Resource Manager na subscrição|    Fornece informações sobre operações executadas pelo recurso em si.| O Azure Monitor, [Stream](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|
|[Relatórios do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)|Os registos e relatórios | Relatórios de atividades de início de sessão de usuário e informações de atividade do sistema sobre utilizadores e de gestão de grupo.|[Graph API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart)|
|[Máquinas virtuais e serviços em nuvem](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm)|Serviço de registo de eventos do Windows e Linux Syslog|  Captura de dados do sistema e dados de registo nas máquinas virtuais e transferências de dados para uma conta de armazenamento à sua escolha.|   Windows (usando o Windows Azure Diagnostics [[WAD](https://docs.microsoft.com/azure/azure-diagnostics)] armazenamento) e do Linux no Azure Monitor|
|[Análise de armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|Registo de armazenamento, fornece dados de métricas para uma conta de armazenamento|Fornece informações sobre os pedidos de rastreio, analisa as tendências de utilização e diagnósticos de problemas com a sua conta de armazenamento.|   REST API ou o [biblioteca de cliente](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[Registos de fluxo do grupo de segurança de rede (NSG)](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|Formato JSON, mostra os fluxos de saída e entrados numa base por regras|Mostra informações sobre o tráfego IP de entrada e de saída através de um grupo de segurança de rede.|[Observador de rede do Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)|
|[Application insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview)|Os registos, exceções e diagnósticos personalizados|   Fornece um desempenho de aplicações (APM) de serviço para os desenvolvedores da web em várias plataformas de monitorização.| REST API, [do Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|Processar dados / alertas de segurança|    Alertas do Centro de segurança do Azure, alertas de registos do Azure Monitor|    Fornece informações de segurança e alertas.|  REST APIs, JSON|

### <a name="activity-logs"></a>Registos de atividade

[Registos de atividades do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornecem informações sobre as operações executadas nos recursos na sua subscrição. Registos de atividade foram anteriormente conhecidos como "registos de auditoria" ou "registos operacionais", uma vez que reportam [eventos de plano de controlo](https://driftboatdave.com/2016/10/13/azure-auditing-options-for-your-custom-reporting-needs/) para as suas subscrições. 

Registos de atividades ajudará a determinar o "o quê, quem e quando" para operações de escrita (ou seja, colocar, publicar ou eliminar). Registos de atividades também ajudam a compreender o estado da operação e outras propriedades relevantes. Registos de Atividades não incluem operações de leitura (GET).

Neste artigo, PUT, POST e DELETE Consulte todas as operações de escrita com um registo de atividade nos recursos. Por exemplo, pode utilizar os registos de atividade para encontrar um erro quando estiver solucionando problemas ou para monitorizar a forma como um utilizador na sua organização alterou um recurso.

![Diagrama de log de atividade](./media/azure-log-audit/azure-log-audit-fig1.png)

Pode obter eventos de um registo de atividade com o portal do Azure, [CLI do Azure](https://docs.microsoft.com/azure/storage/storage-azure-cli), cmdlets do PowerShell, e [API de REST do Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough). Registos de atividades têm o período de retenção de dados de 90 dias.

Cenários de integração de um evento de registo de atividade:

* [Criar um alerta de e-mail ou webhook que é acionado por um evento de registo de atividade](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-auditlog-to-webhook-email).

* [Stream-lo para um hub de eventos](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs) para ingestão por um serviço de terceiros ou de uma solução de análise personalizada, como o Power BI.

* Analise-o no Power BI utilizando as [pacote de conteúdos do Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).

* [Guarde-o para uma conta de armazenamento para inspeção de arquivamento ou manual](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-activity-log). Pode especificar o período de retenção (em dias) ao utilizar perfis de registo.

* Consultar e vê-la no portal do Azure.

* Consultá-los através do cmdlet do PowerShell, CLI do Azure ou REST API.

* Exportar o registo de atividades com perfis de registo para [registos do Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

Pode utilizar uma conta de armazenamento ou [espaço de nomes de hub de eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-resource-manager-namespace-event-hub-enable-archive) que não está na mesma subscrição que aquele que está a emitir o registo. Quem configura a definição tem de ter o adequado [controlo de acesso baseado em funções (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) acesso para ambas as subscrições.

### <a name="azure-diagnostics-logs"></a>Registos de diagnóstico do Azure

Registos de diagnóstico do Azure são emitidos por um recurso que fornece dados avançados e frequentes sobre o funcionamento desse recurso. O conteúdo estes registos varia consoante o tipo de recurso. Por exemplo, [registos de sistema de eventos do Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) são uma categoria de registos de diagnóstico para as VMs, e [BLOBs, tabelas e registos de fila](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account) são as categorias de registos de diagnóstico para contas de armazenamento. Os registos de diagnóstico diferem dos registos de atividade, os quais fornecem informações sobre as operações executadas nos recursos na sua subscrição.

![Diagramas de registos do diagnóstico do Azure](./media/azure-log-audit/azure-log-audit-fig2.png)

Registos de diagnóstico do Azure oferecem várias opções de configuração, como o portal do Azure, PowerShell, CLI do Azure e a API REST.

**Cenários de integração**

* Guarde-as para um [conta de armazenamento](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) para inspeção de auditoria ou manual. Pode especificar o período de retenção (em dias), utilizando as definições de diagnóstico.

* [Stream-los para os hubs de eventos](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs) para ingestão por um serviço de terceiros ou de uma solução de análise personalizada, tal como [PowerBI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/).

* Analisá-los com [registos do Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

**Serviços suportados, o esquema para os registos de diagnóstico e categorias de registo suportadas por tipo de recurso**


| Serviço | Esquema e documentação | Tipo de recurso | Categoria |
| ------- | ------------- | ------------- | -------- |
|Azure Load Balancer| [Registos de Monitor do Azure para o Balanceador de carga (pré-visualização)](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)|Microsoft.Network/loadBalancers<br>Microsoft.Network/loadBalancers|    LoadBalancerAlertEvent<br>LoadBalancerProbeHealthStatus|
|Grupos de Segurança de Rede|[Registos de Monitor do Azure para grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)|Microsoft.Network/networksecuritygroups<br>Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent<br>NetworkSecurityGroupRuleCounter|
|Gateway de Aplicação do Azure|[Registo de diagnósticos para o Gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)|Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog<br>ApplicationGatewayPerformanceLog<br>ApplicationGatewayFirewallLog|
|Azure Key Vault|[Registos do Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-logging)|Microsoft.KeyVault/vaults|AuditEvent|
|Azure Search|[Ativar e utilizar a análise de tráfego de pesquisa](https://docs.microsoft.com/azure/search/search-traffic-analytics)|Microsoft.Search/searchServices|OperationLogs|
|Azure Data Lake Store|[Registos de diagnóstico de acesso para o Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs)|Microsoft.DataLakeStore/accounts<br>Microsoft.DataLakeStore/accounts|Auditoria<br>Pedidos|
|Azure Data Lake Analytics|[Registos de diagnóstico de acesso para o Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs)|Microsoft.DataLakeAnalytics/accounts<br>Microsoft.DataLakeAnalytics/accounts|Auditoria<br>Pedidos|
|Azure Logic Apps|[Esquema de controlo personalizado B2B de Aplicações Lógicas](https://docs.microsoft.com/azure/logic-apps/logic-apps-track-integration-account-custom-tracking-schema)|Microsoft.Logic/workflows<br>Microsoft.Logic/integrationAccounts|WorkflowRuntime<br>IntegrationAccountTrackingEvents|
|Azure Batch|[Registos de diagnóstico do Azure Batch](https://docs.microsoft.com/azure/batch/batch-diagnostics)|Microsoft.Batch/batchAccounts|ServiceLog|
|Automatização do Azure|[Registos de Monitor do Azure para a automatização do Azure](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|Microsoft.Automation/automationAccounts<br>Microsoft.Automation/automationAccounts|JobLogs<br>JobStreams|
|Azure Event Hubs|[Registos de diagnóstico de Hubs de eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs)|Microsoft.EventHub/namespaces<br>Microsoft.EventHub/namespaces|ArchiveLogs<br>OperationalLogs|
|Azure Stream Analytics|[Registos de diagnóstico da tarefa](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)|Microsoft.StreamAnalytics/streamingjobs<br>Microsoft.StreamAnalytics/streamingjobs|Execução<br>Criação de conteúdos|
|Service Bus do Azure|[Registos de diagnóstico do Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-diagnostic-logs)|Microsoft.ServiceBus/namespaces|OperationalLogs|

### <a name="azure-active-directory-reporting"></a>Relatórios do Azure Active Directory

Azure Active Directory (Azure AD) inclui segurança, atividade e relatórios de auditoria para o diretório de um utilizador. O [relatório de auditoria do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) ajuda a identificar ações privilegiadas que ocorreram na instância do utilizador do Azure AD. Ações privilegiadas incluem alterações de elevação (por exemplo, a criação da função ou a reposições de palavra-passe), as configurações da política (por exemplo, políticas de palavra-passe) alteração ou alterações à configuração do diretório (por exemplo, alterações às definições de Federação do domínio).

Os relatórios fornecem o registo de auditoria para o nome do evento, o utilizador que efetuou a ação, o recurso de destino afetado pela alteração e a data e hora (em UTC). Os utilizadores podem obter a lista de eventos de auditoria para o Azure AD através da [portal do Azure](https://portal.azure.com/), conforme descrito nas [ver os registos de auditoria](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

Os relatórios incluídos estão listados na tabela a seguir:

| Relatórios de segurança | Relatórios de atividade | Relatórios de auditoria |
| :--------------- | :--------------- | :------------ |
|Inícios de sessão de fontes desconhecidas| Utilização da aplicação: resumo| Relatório de auditoria de diretório|
|Inícios de sessão após várias falhas|  Utilização da aplicação: detalhado||
|Inícios de sessão de várias localizações geográficas|    Dashboard de aplicações||
|Inícios de sessão de endereços IP com atividade suspeita|   Erros de aprovisionamento de contas||
|Atividades irregulares de início de sessão|    Dispositivos de utilizadores individuais||
|Inícios de sessão de dispositivos possivelmente infetados|   Atividade de utilizador individuais||
|Utilizadores com atividade anómala de início de sessão| Relatório de atividade de grupos||
||Relatório de registo de atividade de reposição de palavra-passe||
||Atividade de reposição de palavra-passe|||

Os dados nestes relatórios podem ser útil para seus aplicativos, como sistemas de informações de segurança e gestão de eventos (SIEM), auditoria e ferramentas de business intelligence. As APIs dos relatórios do Azure AD proporcionam acesso programático aos dados através de um conjunto de APIs baseadas em REST. Pode chamar estas [APIs](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) de várias linguagens de programação e ferramentas.

Eventos no relatório de auditoria do Azure AD são mantidos durante 180 dias.

> [!Note]
> Para obter mais informações sobre a retenção de relatórios, consulte [políticas de retenção de relatórios do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention).

Se estiver interessado em manter seus eventos de auditoria mais tempo, utilize a API de relatórios para extrair regularmente [eventos de auditoria](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events) num arquivo de dados separado.

### <a name="virtual-machine-logs-that-use-azure-diagnostics"></a>Registos de máquina virtual que utilizam o diagnóstico do Azure

[Diagnóstico do Azure](https://docs.microsoft.com/azure/azure-diagnostics) é o recurso no Azure que permite a recolha de dados de diagnóstico num aplicativo implantado. Pode utilizar a extensão de diagnóstico de qualquer uma das várias origens. São suportados atualmente [funções de web e de trabalho do serviço de cloud do Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me).

![Registos de máquina virtual que utilizam o diagnóstico do Azure](./media/azure-log-audit/azure-log-audit-fig3.png)

### <a name="azure-virtual-machineslearnpathsdeploy-a-website-with-azure-virtual-machines-that-are-running-microsoft-windows-and-service-fabrichttpsdocsmicrosoftcomazureservice-fabricservice-fabric-overview"></a>[Máquinas virtuais do Azure](/learn/paths/deploy-a-website-with-azure-virtual-machines/) que estão a executar o Microsoft Windows e [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview)

Pode ativar o diagnóstico do Azure numa máquina virtual efetuando qualquer um dos seguintes procedimentos:

* [Utilizar o Visual Studio para rastrear as máquinas virtuais do Azure](https://docs.microsoft.com/azure/vs-azure-tools-debug-cloud-services-virtual-machines)

* [Configurar remotamente o diagnóstico do Azure numa máquina virtual do Azure](https://docs.microsoft.com/azure/virtual-machines-dotnet-diagnostics)

* [Utilize o PowerShell para configurar diagnósticos em máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-ps-extensions-diagnostics?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

* [Criar uma máquina virtual do Windows com monitorização e diagnósticos com um modelo Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="storage-analytics"></a>Análise de Armazenamento

[Análise de armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) regista e fornece dados de métricas para uma conta de armazenamento. Pode utilizar estes dados para rastrear pedidos, analisar tendências de utilização e diagnosticar problemas relacionados com a sua conta de armazenamento. Registo de análise de armazenamento está disponível para o [serviços de armazenamento de Blobs do Azure, filas do Azure e Azure Table](https://docs.microsoft.com/azure/storage/storage-introduction). Análise de armazenamento regista informações detalhadas sobre pedidos com êxito ou falhadas para um serviço de armazenamento.

Pode usar essas informações para monitorizar pedidos individuais e para diagnosticar problemas com um serviço de armazenamento. Pedidos são registados numa base de melhor esforço. Entradas de registo são criadas apenas se existirem pedidos feitos contra o ponto final de serviço. Por exemplo, se uma conta de armazenamento tiver uma atividade em seu ponto final do blob, mas não em seus pontos de extremidade de tabelas ou filas, apenas os registos que dizem respeito ao serviço de armazenamento de BLOBs são criados.

Para utilizar a análise de armazenamento, ativá-la individualmente para cada serviço que pretende monitorizar. Pode ativá-la no [portal do Azure](https://portal.azure.com/). Para obter mais informações, consulte [monitorizar uma conta de armazenamento no portal do Azure](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). Também pode ativar a análise de armazenamento através de programação com a API REST ou a biblioteca de cliente. Utilize a operação definir propriedades de serviço para ativar a análise de armazenamento individualmente para cada serviço.

Os dados agregados são armazenados num blob bem conhecido (para o registo) e em tabelas bem conhecidas (para métricas), que pode acessar com o serviço de armazenamento de BLOBs e as APIs do serviço de armazenamento de tabela.

Análise de armazenamento tem um limite de 20 terabytes (TB) na quantidade de dados armazenados, que é independentes do limite total para a sua conta de armazenamento. Todos os registos são armazenados no [blobs de blocos](https://docs.microsoft.com/azure/storage/storage-analytics) num contentor com o nome $logs, que é criado automaticamente ao ativar a análise de armazenamento para uma conta de armazenamento.

> [!Note]
> * Para obter mais informações sobre faturação e políticas de retenção de dados, consulte [a análise de armazenamento e de faturação](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing).
> * Para obter mais informações sobre os limites de conta de armazenamento, consulte [metas de escalabilidade e desempenho do armazenamento do Azure](https://docs.microsoft.com/azure/storage/storage-scalability-targets).

Análise de armazenamento regista os seguintes tipos de pedidos de autenticado e anônimos:

| Autenticados  | Anónimo|
| :------------- | :-------------|
| Pedidos com êxito | Pedidos com êxito |
|Pedidos falhados, incluindo o tempo limite, limitação, rede, autorização e outros erros | Pedidos com uma assinatura de acesso partilhado, incluindo pedidos de falhadas e bem-sucedidas |
| Pedidos com uma assinatura de acesso partilhado, incluindo pedidos de falhadas e bem-sucedidas |Erros de tempo limite para o cliente e servidor |
|   Pedidos de dados de análise |    Pedidos falhados de GET com código de erro 304 (não modificado) |
| Pedidos efetuados por análise de armazenamento em si, como o registo é criada ou eliminada, não tem sessão iniciados. Uma lista completa dos dados com sessão iniciada está documentada no [a análise de armazenamento conectado operações e mensagens de estado](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) e [formato de registo de análise de armazenamento](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). | Todos os outros pedidos anónimos com falhas não são registados. Uma lista completa dos dados com sessão iniciada está documentada no [a análise de armazenamento conectado operações e mensagens de estado](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) e [formato de registo de análise de armazenamento](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). |

### <a name="azure-networking-logs"></a>Registos de rede do Azure

Rede de registo e monitorização no Azure é abrangente e abrange duas amplas categorias:

* [Observador de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): Monitorização de rede baseada em cenário é fornecido com os recursos do observador de rede. Este serviço inclui a captura de pacotes, próximo salto, fluxo de IP verificar, a vista de grupo de segurança, os registos de fluxo do NSG. A monitorização ao nível do cenário fornece uma vista de ponta a ponta dos recursos de rede em comparação com a monitorização de recursos de rede individuais.

* [Monitorização de recursos](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): Monitorização de nível de recursos é composto por quatro recursos, os registos de diagnóstico, métricas, resolução de problemas e estado de funcionamento do recurso. Todos esses recursos são criados ao nível de recursos da rede.

![Registos de rede do Azure](./media/azure-log-audit/azure-log-audit-fig4.png)

Observador de rede é um serviço regional que lhe permite monitorizar e diagnosticar condições a um nível de cenário rede, para e do Azure. Diagnóstico de rede e ferramentas de visualização disponíveis com o observador de rede ajudam a compreender, diagnosticar e obter informações da sua rede no Azure.

### <a name="network-security-group-flow-logging"></a>Registo do fluxo do grupo de segurança de rede

[Registos de fluxo NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) são uma funcionalidade do observador de rede que pode utilizar para ver informações sobre o tráfego IP de entrada e de saída através de um NSG. Estes registos de fluxo são escritos no formato JSON e mostrarem:
* Fluxos de saída e entrados numa base por regras.
* O NIC que o fluxo se aplica a.
* 5 cadeias de identificação informações sobre o fluxo: o IP de origem ou de destino, a porta de origem ou de destino e o protocolo.
* Se o tráfego foi permitido ou negado.

Embora os registos de fluxo NSGs de destino, não são apresentadas da mesma forma como os outros registos. Os registos de fluxo são armazenados apenas dentro de uma conta de armazenamento.

As mesmas políticas de retenção que forem vistas num outros registos aplicam-se aos registos de fluxo. Registos de tem uma política de retenção que pode definir a partir de 1 dia até 365 dias. Se não definir uma política de retenção, os registos são mantidos para sempre.

**Registos de diagnóstico**

Eventos periódicos e espontânea são criados pelos recursos de rede e com sessão iniciados em contas de armazenamento e enviados para um hub de eventos ou de registos do Azure Monitor. Os registos de fornecem informações sobre o estado de funcionamento de um recurso. Elas podem ser exibidas em ferramentas como o Power BI e o Azure Monitor registos. Para saber como ver os registos de diagnóstico, veja [registos do Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics).

![Registos de diagnósticos](./media/azure-log-audit/azure-log-audit-fig5.png)

Estão disponíveis para os registos de diagnóstico [Balanceador de carga](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), [grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), rotas, e [Gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).

Observador de rede fornece que um diagnóstico regista o modo de exibição. Esta vista contém todos os recursos de rede que suportam o log de diagnósticos. A partir desta vista, pode ativar e desativar recursos de rede rápida e conveniente.


Para além das capacidades de registo mencionado anteriormente, o observador de rede atualmente tem as seguintes capacidades:
- [Topologia](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview): Fornece uma vista de nível de rede que mostra os vários interconexões e associações entre recursos de rede num grupo de recursos.

- [Captura de pacote variável](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview): Captura de dados de pacote dentro e fora de uma máquina virtual. Opções de filtragem e controles de ajuste fino, tais como definições de limite de tempo e tamanho, avançadas fornecem a versatilidade. Os dados de pacotes podem ser armazenados num armazenamento de BLOBs ou no disco no local *. cap* formato de ficheiro.

* [Verificação de fluxo IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview): Verifica se um pacote é permitido ou negado com base nos parâmetros de pacote de 5 cadeias de identificação do fluxo informações (ou seja, IP de destino, IP de origem, porta de destino, porta de origem e protocolo). Se o pacote for recusado por um grupo de segurança, a regra e o grupo que negou o pacote é devolvido.

* [Do próximo salto](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview): Determina o próximo salto para pacotes que está a ser encaminhada para os recursos de infraestrutura de rede do Azure, para que pode diagnosticar quaisquer rotas definidas pelo utilizador configurado incorretamente.

* [Vista de grupo de segurança](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview): Obtém as regras de segurança aplicada e eficiente que são aplicadas numa VM.

* [Gateway de rede virtual e a resolução de problemas de ligação](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest): Ajuda-o a resolver problemas de ligações e gateways de rede virtual.

* [Limites de subscrição de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): Permite-lhe ver a utilização de recursos de rede contra limites.

### <a name="application-insights"></a>Application Insights

[O Azure Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) é um serviço APM extensível para desenvolvedores da web em várias plataformas. Usá-lo para monitorizar aplicações web em direto. Deteta automaticamente anomalias de desempenho. inclui ferramentas de análise poderosas para o ajudar a diagnosticar problemas e a compreender o que os utilizadores efetivamente fazem com a sua aplicação.

O Application Insights foi concebido para ajudar a melhorar continuamente o desempenho e a usabilidade.

Funciona para as aplicações numa grande variedade de plataformas, incluindo .NET, node. js e Java EE, quer estejam alojadas no local ou na cloud. Ele se integra com o seu processo de DevOps e tem pontos de conexão com várias ferramentas de desenvolvimento.

![Diagrama do Application Insights](./media/azure-log-audit/azure-log-audit-fig6.png)

O Application Insights destina-se à equipa de programação, para o ajudar a compreender o desempenho da aplicação e como está a ser utilizada. Monitoriza:

* **Pedido de taxas, tempos de resposta e taxas de falhas**: Descubra quais páginas estão mais populares, em que alturas do dia, e onde estão os seus utilizadores. Veja que páginas têm o melhor desempenho. Se seus tempos de resposta e taxas de falhas aumentarem-se quando há mais pedidos, poderá ter um problema relativo a recursos.

* **Taxas de dependência, tempos de resposta e taxas de falhas**: Descubra se os serviços externos estão a atrasar.

* **Exceções**: Analisar as estatísticas agregadas ou escolha instâncias específicas e explore o rastreio de pilha e pedidos relacionados. São reportadas exceções do servidor e do browser.

* **Vistas de página e o desempenho de carga**: Obter relatórios a partir de browsers dos seus utilizadores.

* **Chamadas AJAX**: Obter taxas de página Web, tempos de resposta e taxas de falhas.

* **Contagens de utilizadores e sessões**.

* **Contadores de desempenho**: Obter dados das suas máquinas de servidor Windows ou Linux, como CPU, memória e utilização de rede.

* **Diagnóstico do anfitrião**: Obter dados do Docker ou do Azure.

* **Registos de rastreio de diagnóstico**: Obter dados da sua aplicação, para que possa correlacionar eventos de rastreio com pedidos.

* **Métricas e eventos personalizados**: Obter dados que escreve no código de cliente ou servidor, para controlar eventos comerciais, como artigos vendidos ou jogos ganhos.

A tabela seguinte apresenta uma lista e descreve os cenários de integração:

| Cenário de integração | Descrição |
| --------------------- | :---------- |
|[Mapa da aplicação](https://docs.microsoft.com/azure/application-insights/app-insights-app-map)|Os componentes da sua aplicação, com as principais métricas e alertas.||
|[Por exemplo pesquisa nos diagnósticos dados](https://docs.microsoft.com/azure/application-insights/app-insights-diagnostic-search)| Procure e filtre eventos como pedidos, exceções, chamadas de dependências, rastreios de registo e visualizações de página.||
|[Explorador de métricas para dados agregados](https://docs.microsoft.com/azure/azure-monitor/app/metrics-explorer)|Explore, filtre e segmente dados agregados, como taxas de pedidos, falhas e exceções, tempos de resposta e tempos de carregamento de páginas.||
|[Dashboards](https://docs.microsoft.com/azure/application-insights/app-insights-dashboards#dashboards)|Combine dados de vários recursos e partilhe-os com outras pessoas. Ideais para aplicações com vários componentes e para visualização contínua na sala de equipa.||
|[Stream de métricas em direto](https://docs.microsoft.com/azure/azure-monitor/app/live-stream)|Quando implementa uma compilação nova, veja estes indicadores de desempenho em tempo quase real, para ter a certeza de que está tudo a funcionar conforme esperado.||
|[Análise](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)|Responda a perguntas difíceis sobre o desempenho e a utilização da sua aplicação através desta poderosa linguagem de consultas.||
|[Alertas automáticas e manuais](https://docs.microsoft.com/azure/application-insights/app-insights-alerts)|Alertas automáticos se adaptar a padrões normais da sua aplicação de telemetria e são acionadas se ocorrer algo fora do padrão habitual. Também pode definir alertas em níveis específicos de métricas personalizadas ou padrão.||
|[Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)|Ver dados de desempenho no código. Aceda ao código a partir dos rastreios de pilha.||
|[Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)|Integre métricas de utilização com outras métricas de business intelligence.||
|[API REST](https://dev.applicationinsights.io/)|Escreva código para executar consultas nas métricas e nos dados não processados.||
|[Exportação contínua](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry)|Exportação de dados não processados para o armazenamento em massa quando for lançado.||

### <a name="azure-security-center-alerts"></a>Alertas do Centro de segurança do Azure

Deteção de ameaças do Centro de segurança do Azure funciona através da recolha automática de informações de segurança de seus recursos do Azure, rede e soluções de parceiros ligadas. Analisa esta informação, muitas vezes correlacionando informações de várias origens, para identificar ameaças. Os alertas de segurança são prioritários no Centro de Segurança, juntamente com recomendações sobre como remediar a ameaça. Para obter mais informações, consulte [Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro).

![Diagrama de centro de segurança do Azure](./media/azure-log-audit/azure-log-audit-fig7.png)

O Centro de Segurança emprega análises de segurança avançadas, que ultrapassam as abordagens baseadas na assinatura. Aplica-se para soluções de dados grandes e [aprendizagem](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) tecnologias para avaliar eventos em recursos de infraestrutura do integralmente na nuvem. Dessa forma, detetar ameaças que seriam impossíveis de identificar através de abordagens manuais e previsão da evolução de ataques. Estas análises de segurança incluem:

* **Informações de ameaças integradas**: Procura pessoas mal-intencionadas conhecidas ao aplicar inteligência de ameaças globais de produtos da Microsoft e serviços, a unidade Microsoft Crimes digitais (DCU), o Microsoft Security Response Center (MSRC) e feeds externos.

* **Análise comportamental**: Aplica-se padrões conhecidos para detetar um comportamento malicioso.

* **Deteção de anomalias**: Utiliza a criação de perfis estatística para criar uma linha de base histórica. Alerta sobre os desvios de linhas de base estabelecidos em conformidade com um potencial vetor de ataque.

Muitas operações de segurança e as equipes de resposta a incidentes contam com uma solução SIEM como ponto de partida para separação e investigar alertas de segurança. Com integração de registos do Azure, pode sincronizar alertas do Centro de segurança e os eventos de segurança de máquina virtual, recolhidos por registos de diagnóstico e auditoria do Azure, com a solução de SIEM em tempo real ou registos do Azure Monitor.

## <a name="azure-monitor-logs"></a>Registos de Monitor do Azure

Registos de Monitor do Azure é um serviço no Azure que ajuda a recolher e analisar os dados que são gerados por recursos na sua cloud e ambientes no local. Ele fornece informações em tempo real ao utilizar pesquisa integrada e dashboards personalizados para analisar, prontamente, milhões de registos em todas as suas cargas de trabalho e servidores, independentemente da respetiva localização física.

![Diagrama de registos do Azure Monitor](./media/azure-log-audit/azure-log-audit-fig8.png)

Os registos do centro do Azure Monitor é a área de trabalho do Log Analytics, que está alojada no Azure. Registos de Monitor do Azure recolhe dados na área de trabalho de origens ligadas ao configurar origens de dados e adicionar soluções à sua subscrição. Origens de dados e soluções de cada criar diferentes tipos de registos, cada um com seu próprio conjunto de propriedades. Mas origens e soluções podem ainda ser analisadas em conjunto em consultas para a área de trabalho. Esta capacidade permite-lhe utilizar as mesmas ferramentas e métodos para trabalhar com uma variedade de dados recolhidos por uma variedade de origens.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Origens ligadas são os computadores e outros recursos que geram os dados que são recolhidos pelos registos do Azure Monitor. Origens podem incluir os agentes que estão instalados no [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) e [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) computadores que se ligam diretamente ou agentes no [um grupo de gestão ligado do System Center Operations Manager](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). Registos de Monitor do Azure também podem recolher dados a partir de um [conta de armazenamento do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage).

[Origens de dados](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources) são os vários tipos de dados que são recolhidos a partir de cada origem ligada. As origens incluem eventos e [dados de desempenho](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters) partir [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) e agentes do Linux, além de origens como [registos do IIS](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs) e [registos de texto personalizado](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-custom-logs). Pode configurar cada origem de dados que pretende recolher e a configuração é entregue automaticamente a cada origem ligada.

Existem quatro formas de [recolher registos e métricas para serviços do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage):

* Direcionar o diagnóstico do Azure para registos do Azure Monitor (**diagnóstico** na tabela a seguir)

* Registos do diagnóstico do Azure para o armazenamento do Azure para o Azure Monitor (**armazenamento** na tabela a seguir)

* Conectores para serviços do Azure (**conector** na tabela a seguir)

* Scripts para recolher e, em seguida, publicar dados sobre os registos do Azure Monitor (células em branco na tabela a seguir e para os serviços que não estão listados)

| Serviço | Tipo de recurso | Registos | Métricas | Solução |
| :------ | :------------ | :--- | :------ | :------- |
|Gateway de Aplicação do Azure| Microsoft.Network/<br>applicationGateways|  Diagnóstico|Diagnóstico|    [Aplicação do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics) [análise do Gateway](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics)|
|Application Insights||     Conector|  Conector|  [O Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) [conector (pré-visualização)](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)|
|Contas de automatização do Azure| Microsoft.Automation/<br>AutomationAccounts|    Diagnóstico||       [Mais informações](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|
|Contas de Batch do Azure|  Microsoft.Batch/<br>batchAccounts|  Diagnóstico|    Diagnóstico||
|Serviços cloud clássico||       Armazenamento||       [Mais informações](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage-iis-table)|
|Serviços Cognitivos|    Microsoft.CognitiveServices/<br>accounts|       Diagnóstico|||
|Azure Data Lake Analytics| Microsoft.DataLakeAnalytics/<br>accounts|   Diagnóstico|||
|Azure Data Lake Store| Microsoft.DataLakeStore/<br>accounts|   Diagnóstico|||
|Espaço de nomes do Hub de eventos do Azure| Microsoft.EventHub/<br>namespaces|  Diagnóstico|    Diagnóstico||
|Azure IoT Hub| Microsoft.Devices/<br>IotHubs||     Diagnóstico||
|Azure Key Vault|   Microsoft.KeyVault/<br>cofres|  Diagnóstico  || [Análise do Cofre de Chaves](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault)|
|Azure Load Balancer|   Microsoft.Network/<br>loadBalancers|    Diagnóstico|||
|Azure Logic Apps|  Microsoft.Logic/<br>Fluxos de trabalho|  Diagnóstico|    Diagnóstico||
||Microsoft.Logic/<br>integrationAccounts||||
|Grupos de Segurança de Rede|   Microsoft.Network/<br>networksecuritygroups|Diagnóstico||   [Análise do grupo de segurança de rede do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-network-security-group-analytics-solution-in-log-analytics)|
|Cofres de recuperação|   Microsoft.RecoveryServices/<br>cofres|||[Análise (pré-visualização) de serviços de recuperação do Azure](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
|Procurar serviços|   Microsoft.Search/<br>searchServices|    Diagnóstico|    Diagnóstico||
|Espaço de nomes do Service Bus| Microsoft.ServiceBus/<br>namespaces|    Diagnóstico|Diagnóstico|    [Análise do Service Bus (pré-visualização)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
|Service Fabric||       Armazenamento||    [Análise do Service Fabric (pré-visualização)](https://docs.microsoft.com/azure/log-analytics/log-analytics-service-fabric)|
|SQL (v12)| Microsoft.Sql/<br>servers/<br>bases de dados||       Diagnóstico||
||Microsoft.Sql/<br>servers/<br>elasticPools||||
|Armazenamento|||         Script| [Análise de armazenamento do Azure (pré-visualização)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution)|
|Máquinas Virtuais do Azure|    Microsoft.Compute/<br>virtualMachines|  Extensão|  Extensão||
||||Diagnóstico||
|Conjuntos de dimensionamento de máquinas virtuais|    Microsoft.Compute/<br>virtualMachines    ||Diagnóstico||
||Microsoft.Compute/<br>virtualMachineScaleSets/<br>virtualMachines||||
|Farms de servidores Web|Microsoft.Web/<br>serverfarms||   Diagnóstico
|Sites|  Microsoft.Web/<br>sites ||      Diagnóstico|    [Mais informações](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webappazure-oms-monitoring)|
||Microsoft.Web/<br>sites/<br>ranhuras|||||


## <a name="log-integration-with-on-premises-siem-systems"></a>Integração de registos com sistemas SIEM no local

Com o Azure Log Integration pode integrar registos não processados a partir dos seus recursos do Azure com o seu sistema SIEM no local (segurança informações e o evento de sistema de gestão). Downloads de AzLog foram desativados a 27 de Junho de 2018. Para obter orientações sobre o que fazer a postagem de revisão de encaminhamento de movimentação [monitor do Azure de utilização para integração com ferramentas SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

![Diagrama de integração de registo](./media/azure-log-audit/azure-log-audit-fig9.png)

Integração de registos recolhe diagnóstico do Azure a partir de suas máquinas de virtuais do Windows, registos de atividades do Azure, alertas do Centro de segurança do Azure e os registos do fornecedor de recursos do Azure. Esta integração fornece um dashboard unificado para todos os seus ativos, se forem no local ou na cloud, para que pode agregar, correlacionar, analisar e de alertas para eventos de segurança.

Integração de registos, atualmente, suporta a integração de registos de atividade do Azure, registos de auditoria de registos de eventos do Windows das máquinas de virtuais do Windows com a sua subscrição do Azure, alertas do Centro de segurança do Azure, os registos de diagnóstico do Azure e do Azure AD.

| Tipo de registo | O Azure Monitor registos de suporte JSON (Splunk, ArcSight e IBM QRadar) |
| :------- | :-------------------------------------------------------- |
|Registos de auditoria do Azure AD|   Sim|
|Registos de atividade| Sim|
|Alertas do Centro de segurança |Sim|
|Registos de diagnóstico (registos de recursos)|  Sim|
|Registos de VM|   Sim, por meio de eventos reencaminhados e não através de JSON|

[Introdução ao Azure Log Integration](https://docs.microsoft.com/azure/security/security-azure-log-integration-get-started): Este tutorial explica-lhe instalar o Azure Log Integration e integrar registos do armazenamento do Azure, os registos de atividades do Azure, alertas do Centro de segurança do Azure e do Azure AD registos de auditoria.

Cenários de integração para o SIEM:

* [Passos de configuração de parceiros](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/): Esta postagem de blog mostra-lhe como configurar a integração de registos do Azure para trabalhar com soluções de parceiros Splunk e cso da HP e IBM QRadar.

* [Integração de registos do Azure FAQ](https://docs.microsoft.com/azure/security/security-azure-log-integration-faq): Este artigo responde a perguntas sobre a integração de registos do Azure.

* [Integrar alertas do Centro de segurança com o Azure Log Integration](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration): Este artigo descreve como sincronizar alertas do Centro de segurança, eventos de segurança de máquina virtual recolhidos pelos registos de diagnóstico do Azure, e registos de auditoria do Azure com a solução de SIEM ou registos do Azure Monitor.

## <a name="next-steps"></a>Passos Seguintes

- [Auditoria e registo](https://docs.microsoft.com/azure/security/security-management-and-monitoring-overview): Proteger dados ao manter a visibilidade e a responder rapidamente a alertas de segurança oportuna.

- [Coleção de registo e o registo de auditoria de segurança no Azure](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/): Impor estas definições para se certificar de que as instâncias do Azure estão a recolher os registos de segurança e auditoria corretos.

- [Configurar definições de auditoria para um conjunto de sites](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US): Se for um administrador de conjunto de sites, obter o histórico de ações de usuários individuais e o histórico das ações efetuadas durante um intervalo de datas específico. 

- [Procurar o registo de auditoria no Centro de conformidade de segurança do Office 365 e](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US): Utilize o Centro de conformidade e segurança do Office 365 para procurar o registo de auditoria unificada e ver a atividade de utilizador e administrador na sua organização do Office 365.


