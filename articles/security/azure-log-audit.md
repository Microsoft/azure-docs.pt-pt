---
title: Log e auditoria do Azure | Microsoft Docs
description: Saiba mais sobre como você pode usar os dados de log para obter informações aprofundadas sobre seu aplicativo.
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
ms.openlocfilehash: 6634b674037b48ff9ef9690144ef5274f31a6ea8
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227969"
---
# <a name="azure-logging-and-auditing"></a>Registo e auditoria do Azure

O Azure fornece uma ampla variedade de opções configuráveis de auditoria e log de segurança para ajudá-lo a identificar lacunas em seus mecanismos e políticas de segurança. Este artigo aborda a geração, coleta e análise de logs de segurança de serviços hospedados no Azure.

> [!Note]
> Determinadas recomendações neste artigo podem resultar em maior uso de recursos de computação, rede ou dados e aumentar os custos de licença ou assinatura.

## <a name="types-of-logs-in-azure"></a>Tipos de logs no Azure

Os aplicativos de nuvem são complexos, com muitas partes móveis. Os logs fornecem dados para ajudar a manter seus aplicativos em funcionamento. Os logs ajudam a solucionar problemas anteriores ou a evitar possíveis. E podem ajudar a melhorar o desempenho ou a manutenção do aplicativo, ou automatizar ações que, de outra forma, exigem intervenção manual.

Os logs do Azure são categorizados nos seguintes tipos:
* **Os logs de controle/gerenciamento** fornecem informações sobre Azure Resource Manager criar, atualizar e excluir operações. Para obter mais informações, consulte [logs de atividades do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs).

* **Os logs do plano de dados** fornecem informações sobre eventos gerados como parte do uso de recursos do Azure. Exemplos desse tipo de log são os logs de aplicativo, segurança e sistema de eventos do Windows em uma VM (máquina virtual) e os [logs de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) configurados por meio de Azure monitor.

* Os **eventos processados** fornecem informações sobre eventos/alertas analisados que foram processados em seu nome. Exemplos desse tipo são [alertas da central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) , em que a [central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) processou e analisou sua assinatura e fornece alertas de segurança concisos.

A tabela a seguir lista os tipos mais importantes de logs disponíveis no Azure:

| Categoria do registo | Tipo de log | Utilização | Integração |
| ------------ | -------- | ------ | ----------- |
|[Logs de atividade](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|Eventos de plano de controle em recursos de Azure Resource Manager|   Fornece informações sobre as operações que foram realizadas em recursos em sua assinatura.|    API REST, [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|
|[Registos de diagnóstico do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|Dados frequentes sobre a operação de Azure Resource Manager recursos na assinatura|    Fornece informações sobre as operações que seu próprio recurso realizou.| Azure Monitor, [fluxo](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|
|[Relatórios do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)|Logs e relatórios | Relata as atividades de entrada do usuário e as informações de atividade do sistema sobre o gerenciamento de usuários e grupos.|[Graph API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart)|
|[Máquinas virtuais e serviços de nuvem](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm)|Serviço log de eventos do Windows e syslog do Linux|  Captura dados do sistema e dados de registro em log nas máquinas virtuais e transfere esses dados para uma conta de armazenamento de sua escolha.|   Windows (usando o Windows Diagnóstico do Azure [[wad](https://docs.microsoft.com/azure/azure-diagnostics)] Storage) e Linux no Azure monitor|
|[Análise de Armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|Log de armazenamento, fornece dados de métricas para uma conta de armazenamento|Fornece informações sobre as solicitações de rastreamento, analisa tendências de uso e diagnostica problemas com sua conta de armazenamento.|   API REST ou a [biblioteca de cliente](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[Logs de fluxo do NSG (grupo de segurança de rede)](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|O formato JSON mostra os fluxos de entrada e saída por regra|Exibe informações sobre o tráfego IP de entrada e saída por meio de um grupo de segurança de rede.|[Observador de rede do Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)|
|[Application insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview)|Logs, exceções e diagnósticos personalizados|   Fornece um serviço de monitoramento do desempenho de aplicativos (APM) para desenvolvedores da Web em várias plataformas.| API REST, [Power bi](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|Processar dados/alertas de segurança|    Alertas da central de segurança do Azure, alertas de logs de Azure Monitor|    Fornece informações de segurança e alertas.|  APIs REST, JSON|

### <a name="activity-logs"></a>Registos de atividade

[Os logs de atividades do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornecem informações sobre as operações que foram executadas em recursos em sua assinatura. Os logs de atividade eram anteriormente conhecidos como "logs de auditoria" ou "logs operacionais", pois eles relatam [eventos de plano de controle](https://driftboatdave.com/2016/10/13/azure-auditing-options-for-your-custom-reporting-needs/) para suas assinaturas. 

Os logs de atividades ajudam a determinar "o que, quem e quando" para operações de gravação (isto é, PUT, POST ou DELETE). Os logs de atividades também ajudam você a entender o status da operação e outras propriedades relevantes. Os logs de atividade não incluem operações de leitura (GET).

Neste artigo, PUT, POST e DELETE referem-se a todas as operações de gravação que um log de atividades contém nos recursos. Por exemplo, você pode usar os logs de atividade para encontrar um erro quando estiver solucionando problemas ou para monitorar como um usuário em sua organização modificou um recurso.

![Diagrama do log de atividades](./media/azure-log-audit/azure-log-audit-fig1.png)

Você pode recuperar eventos de um log de atividades usando o portal do Azure, [CLI do Azure](https://docs.microsoft.com/azure/storage/storage-azure-cli), cmdlets do PowerShell e [Azure monitor API REST](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough). Os logs de atividade têm um período de retenção de dados de 90 dias.

Cenários de integração para um evento do log de atividades:

* [Crie um alerta de webhook ou email disparado por um evento do log de atividades](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-auditlog-to-webhook-email).

* [Transmita-o para um hub de eventos](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs) para ingestão por um serviço de terceiros ou uma solução de análise personalizada, como o PowerBI.

* Analise-o no PowerBI usando o [pacote de conteúdo do powerbi](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).

* [Salve-o em uma conta de armazenamento para a inspeção manual ou de arquivamento](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-activity-log). Você pode especificar o tempo de retenção (em dias) usando perfis de log.

* Consulte e exiba-o no portal do Azure.

* Consulte-o por meio do cmdlet do PowerShell, CLI do Azure ou API REST.

* Exporte o log de atividades com perfis de log para [Azure monitor logs](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

Você pode usar uma conta de armazenamento ou um [namespace de Hub de eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-resource-manager-namespace-event-hub-enable-archive) que não esteja na mesma assinatura que a que está emitindo o log. Quem define a configuração deve ter o acesso [RBAC (controle de acesso baseado em função)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) apropriado para ambas as assinaturas.

### <a name="azure-diagnostics-logs"></a>Registos de diagnóstico do Azure

Os logs de diagnóstico do Azure são emitidos por um recurso que fornece dados avançados e frequentes sobre a operação do recurso. O conteúdo estes registos varia consoante o tipo de recurso. Por exemplo, [logs do sistema de eventos do Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) são uma categoria de logs de diagnóstico para VMs e [logs de BLOB, tabela e fila](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account) são categorias de logs de diagnóstico para contas de armazenamento. Os logs de diagnóstico são diferentes dos logs de atividades, que fornecem informações sobre as operações que foram realizadas em recursos em sua assinatura.

![Diagramas dos logs de diagnóstico do Azure](./media/azure-log-audit/azure-log-audit-fig2.png)

Os logs de diagnóstico do Azure oferecem várias opções de configuração, como o portal do Azure, o PowerShell, o CLI do Azure e a API REST.

**Cenários de integração**

* Salve-os em uma [conta de armazenamento](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) para auditoria ou inspeção manual. Você pode especificar o tempo de retenção (em dias) usando as configurações de diagnóstico.

* [Transmita-os para os hubs de eventos](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs) para ingestão por um serviço de terceiros ou uma solução de análise personalizada, como o [PowerBI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/).

* Analise-os com [logs de Azure monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

**Serviços com suporte, esquema para logs de diagnóstico e categorias de log com suporte por tipo de recurso**


| Serviço | Esquema e documentação | Tipo de recurso | Category |
| ------- | ------------- | ------------- | -------- |
|Azure Load Balancer| [Logs de Azure Monitor para Load Balancer (versão prévia)](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)|Microsoft.Network/loadBalancers<br>Microsoft.Network/loadBalancers|    LoadBalancerAlertEvent<br>LoadBalancerProbeHealthStatus|
|Grupos de Segurança de Rede|[Logs de Azure Monitor para grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)|Microsoft.Network/networksecuritygroups<br>Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent<br>NetworkSecurityGroupRuleCounter|
|Gateway de Aplicação do Azure|[Log de diagnóstico para o gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)|Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog<br>ApplicationGatewayPerformanceLog<br>ApplicationGatewayFirewallLog|
|Azure Key Vault|[Logs de Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-logging)|Microsoft.KeyVault/vaults|AuditEvent|
|Azure Search|[Habilitando e usando o Análise de Tráfego de pesquisa](https://docs.microsoft.com/azure/search/search-traffic-analytics)|Microsoft.Search/searchServices|OperationLogs|
|Azure Data Lake Store|[Acessar os logs de diagnóstico para Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs)|Microsoft.DataLakeStore/accounts<br>Microsoft.DataLakeStore/accounts|Auditoria<br>Pedidos|
|Azure Data Lake Analytics|[Acessar os logs de diagnóstico para Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs)|Microsoft.DataLakeAnalytics/accounts<br>Microsoft.DataLakeAnalytics/accounts|Auditoria<br>Pedidos|
|Azure Logic Apps|[Esquema de controlo personalizado B2B de Aplicações Lógicas](https://docs.microsoft.com/azure/logic-apps/logic-apps-track-integration-account-custom-tracking-schema)|Microsoft.Logic/workflows<br>Microsoft.Logic/integrationAccounts|WorkflowRuntime<br>IntegrationAccountTrackingEvents|
|Azure Batch|[Logs de diagnóstico do lote do Azure](https://docs.microsoft.com/azure/batch/batch-diagnostics)|Microsoft.Batch/batchAccounts|ServiceLog|
|Automatização do Azure|[Logs de Azure Monitor para a automação do Azure](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|Microsoft.Automation/automationAccounts<br>Microsoft.Automation/automationAccounts|JobLogs<br>JobStreams|
|Azure Event Hubs|[Logs de diagnóstico dos hubs de eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs)|Microsoft.EventHub/namespaces<br>Microsoft.EventHub/namespaces|ArchiveLogs<br>OperationalLogs|
|Azure Stream Analytics|[Logs de diagnóstico de trabalho](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)|Microsoft.StreamAnalytics/streamingjobs<br>Microsoft.StreamAnalytics/streamingjobs|Execução<br>Criação de conteúdos|
|Service Bus do Azure|[Registos de diagnósticos do Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-diagnostic-logs)|Microsoft.ServiceBus/namespaces|OperationalLogs|

### <a name="azure-active-directory-reporting"></a>Relatórios do Azure Active Directory

O Azure Active Directory (AD do Azure) inclui relatórios de segurança, atividade e auditoria para o diretório de um usuário. O [relatório de auditoria do Azure ad](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) ajuda a identificar ações privilegiadas que ocorreram na instância do Azure AD do usuário. As ações privilegiadas incluem alterações de elevação (por exemplo, criação de função ou redefinições de senha), alteração das configurações de política (por exemplo, políticas de senha) ou alterações na configuração de diretório (por exemplo, alterações nas configurações de Federação de domínio).

Os relatórios fornecem o registro de auditoria para o nome do evento, o usuário que realizou a ação, o recurso de destino afetado pela alteração e a data e hora (em UTC). Os usuários podem recuperar a lista de eventos de auditoria para o Azure AD por meio do [portal do Azure](https://portal.azure.com/), conforme descrito em [exibir seus logs de auditoria](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

Os relatórios incluídos são listados na tabela a seguir:

| Relatórios de segurança | Relatórios de atividade | Relatórios de auditoria |
| :--------------- | :--------------- | :------------ |
|Inícios de sessão de fontes desconhecidas| Utilização da aplicação: resumo| Relatório de auditoria de diretório|
|Inícios de sessão após várias falhas|  Utilização da aplicação: detalhado||
|Inícios de sessão de várias localizações geográficas|    Dashboard de aplicações||
|Inícios de sessão de endereços IP com atividade suspeita|   Erros de aprovisionamento de contas||
|Atividades irregulares de início de sessão|    Dispositivos de utilizadores individuais||
|Inícios de sessão de dispositivos possivelmente infetados|   Atividade de usuário individual||
|Utilizadores com atividade anómala de início de sessão| Relatório de atividade de grupos||
||Relatório de atividade de registro de redefinição de senha||
||Atividade de reposição de palavra-passe||

Os dados nesses relatórios podem ser úteis para seus aplicativos, como sistemas SIEM (gerenciamento de eventos e informações de segurança), auditoria e ferramentas de business intelligence. As APIs dos relatórios do Azure AD proporcionam acesso programático aos dados através de um conjunto de APIs baseadas em REST. Você pode chamar essas [APIs](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) de várias ferramentas e linguagens de programação.

A retenção de eventos no relatório de auditoria do Azure AD varia entre 7-90 dias, dependendo do tipo de licença associado ao locatário. 

> [!Note]
> Para obter mais informações sobre a retenção de relatórios, consulte [políticas de retenção de relatórios do Azure ad](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention).

Se você estiver interessado em reter seus eventos de auditoria por mais tempo, use a API de relatórios para extrair [eventos de auditoria](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events) regularmente para um armazenamento de dados separado.

### <a name="virtual-machine-logs-that-use-azure-diagnostics"></a>Logs de máquina virtual que usam Diagnóstico do Azure

[Diagnóstico do Azure](https://docs.microsoft.com/azure/azure-diagnostics) é o recurso no Azure que permite a coleta de dados de diagnóstico em um aplicativo implantado. Você pode usar a extensão de diagnóstico de qualquer uma das várias fontes. Atualmente, há suporte para [funções Web e de trabalho do serviço de nuvem do Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me).

![Logs de máquina virtual que usam Diagnóstico do Azure](./media/azure-log-audit/azure-log-audit-fig3.png)

### <a name="azure-virtual-machineslearnpathsdeploy-a-website-with-azure-virtual-machines-that-are-running-microsoft-windows-and-service-fabrichttpsdocsmicrosoftcomazureservice-fabricservice-fabric-overview"></a>[Máquinas virtuais do Azure](/learn/paths/deploy-a-website-with-azure-virtual-machines/) que estão executando o Microsoft Windows e o [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview)

Você pode habilitar Diagnóstico do Azure em uma máquina virtual fazendo o seguinte:

* [Usar o Visual Studio para rastrear máquinas virtuais do Azure](https://docs.microsoft.com/azure/vs-azure-tools-debug-cloud-services-virtual-machines)

* [Configurar Diagnóstico do Azure remotamente em uma máquina virtual do Azure](https://docs.microsoft.com/azure/virtual-machines-dotnet-diagnostics)

* [Usar o PowerShell para configurar o diagnóstico em máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-ps-extensions-diagnostics?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

* [Criar uma máquina virtual do Windows com monitoramento e diagnóstico usando um modelo de Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="storage-analytics"></a>Análise de Armazenamento

[Análise de armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) logs e fornece dados de métricas para uma conta de armazenamento. Pode utilizar estes dados para rastrear pedidos, analisar tendências de utilização e diagnosticar problemas relacionados com a sua conta de armazenamento. Análise de Armazenamento registro em log está disponível para o [blob do Azure, a fila do Azure e os serviços de armazenamento de tabelas do Azure](https://docs.microsoft.com/azure/storage/storage-introduction). Análise de Armazenamento registra informações detalhadas sobre solicitações bem-sucedidas e com falha em um serviço de armazenamento.

Você pode usar essas informações para monitorar solicitações individuais e diagnosticar problemas com um serviço de armazenamento. As solicitações são registradas em uma base de melhor esforço. As entradas de log serão criadas somente se houver solicitações feitas no ponto de extremidade de serviço. Por exemplo, se uma conta de armazenamento tiver atividade em seu ponto de extremidade de BLOB, mas não em seus pontos de extremidades de tabela ou de fila, somente os logs pertencentes ao serviço de armazenamento de BLOBs serão criados.

Para usar Análise de Armazenamento, habilite-o individualmente para cada serviço que você deseja monitorar. Você pode habilitá-lo no [portal do Azure](https://portal.azure.com/). Para obter mais informações, consulte [monitorar uma conta de armazenamento no portal do Azure](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). Você também pode habilitar Análise de Armazenamento programaticamente por meio da API REST ou da biblioteca de cliente. Use a operação definir propriedades do serviço para habilitar Análise de Armazenamento individualmente para cada serviço.

Os dados agregados são armazenados em um blob conhecido (para registro em log) e em tabelas conhecidas (para métricas), que você pode acessar usando as APIs serviço de armazenamento de BLOBs e serviço de armazenamento de tabelas.

O Análise de Armazenamento tem um limite de 20 terabytes (TB) na quantidade de dados armazenados que é independente do limite total da sua conta de armazenamento. Todos os logs são armazenados em blobs de [blocos](https://docs.microsoft.com/azure/storage/storage-analytics) em um contêiner chamado $logs, que é criado automaticamente quando você habilita análise de armazenamento para uma conta de armazenamento.

> [!Note]
> * Para obter mais informações sobre políticas de cobrança e retenção de dados, consulte [análise de armazenamento e cobrança](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing).
> * Para obter mais informações sobre limites de conta de armazenamento, consulte [metas de desempenho e escalabilidade do armazenamento do Azure](https://docs.microsoft.com/azure/storage/storage-scalability-targets).

Análise de Armazenamento registra os seguintes tipos de solicitações autenticadas e anônimas:

| Autenticado  | Anónimo|
| :------------- | :-------------|
| Solicitações bem-sucedidas | Solicitações bem-sucedidas |
|Solicitações com falha, incluindo tempo limite, limitação, rede, autorização e outros erros | Solicitações que usam uma assinatura de acesso compartilhado, incluindo solicitações com falha e bem-sucedidas |
| Solicitações que usam uma assinatura de acesso compartilhado, incluindo solicitações com falha e bem-sucedidas |Erros de tempo limite para o cliente e o servidor |
|   Solicitações para dados de análise |    Solicitações GET com falha com o código de erro 304 (não modificado) |
| As solicitações feitas por Análise de Armazenamento em si, como criação ou exclusão de log, não são registradas. Uma lista completa dos dados registrados em log está documentada em [análise de armazenamento operações registradas e mensagens de status](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) e [análise de armazenamento formato de log](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). | Todas as outras solicitações anônimas com falha não são registradas. Uma lista completa dos dados registrados em log está documentada em [análise de armazenamento operações registradas e mensagens de status](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) e [análise de armazenamento formato de log](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). |

### <a name="azure-networking-logs"></a>Logs de rede do Azure

O monitoramento e o registro em log de rede no Azure são abrangentes e abrangem duas categorias amplas:

* [Observador de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): O monitoramento de rede baseado em cenário é fornecido com os recursos do observador de rede. Esse serviço inclui captura de pacote, próximo salto, verificação de fluxo de IP, exibição de grupo de segurança, logs de fluxo de NSG. O monitoramento em nível de cenário fornece uma exibição de ponta a ponta dos recursos de rede em contraste com o monitoramento de recursos de rede individual.

* [Monitoramento de recursos](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): O monitoramento no nível de recurso consiste em quatro recursos, logs de diagnóstico, métricas, solução de problemas e integridade de recursos. Todos esses recursos são criados no nível de recurso da rede.

![Logs de rede do Azure](./media/azure-log-audit/azure-log-audit-fig4.png)

O observador de rede é um serviço regional que permite monitorar e diagnosticar condições em um nível de cenário de rede em, para e do Azure. As ferramentas de visualização e diagnóstico de rede disponíveis com o observador de rede ajudam você a entender, diagnosticar e obter informações sobre sua rede no Azure.

### <a name="network-security-group-flow-logging"></a>Log de fluxo do grupo de segurança de rede

[Os logs de fluxo NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) são um recurso do observador de rede que você pode usar para exibir informações sobre o tráfego IP de entrada e saída por meio de um NSG. Esses logs de fluxo são gravados no formato JSON e mostram:
* Fluxos de saída e de entrada por regra.
* A NIC à qual o fluxo se aplica.
* informações de 5 tuplas sobre o fluxo: o IP de origem ou de destino, a porta de origem ou de destino e o protocolo.
* Se o tráfego foi permitido ou negado.

Embora os logs de fluxo tenham como destino NSGs, eles não são exibidos da mesma maneira que os outros logs. Os logs de fluxo são armazenados somente dentro de uma conta de armazenamento.

As mesmas políticas de retenção que são vistas em outros logs se aplicam aos logs de fluxo. Os logs têm uma política de retenção que você pode definir de 1 dia para 365 dias. Se não definir uma política de retenção, os registos são mantidos para sempre.

**Registos de diagnóstico**

Eventos periódicos e ESPONTANEOS são criados por recursos de rede e registrados em contas de armazenamento e enviados para um hub de eventos ou logs de Azure Monitor. Os logs fornecem informações sobre a integridade de um recurso. Eles podem ser exibidos em ferramentas como Power BI e Azure Monitor logs. Para saber como exibir os logs de diagnóstico, consulte [Azure monitor logs](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics).

![Logs de diagnóstico](./media/azure-log-audit/azure-log-audit-fig5.png)

Os logs de diagnóstico estão disponíveis para [Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), [grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), rotas e gateway de [aplicativo](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).

O observador de rede fornece uma exibição de logs de diagnóstico. Esta exibição contém todos os recursos de rede que dão suporte ao log de diagnóstico. Nessa exibição, você pode habilitar e desabilitar os recursos de rede de forma conveniente e rápida.


Além dos recursos de log mencionados anteriormente, o observador de rede atualmente tem os seguintes recursos:
- [Topologia](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview): Fornece uma exibição de nível de rede que mostra as várias interconexões e associações entre os recursos de rede em um grupo de recursos.

- [Captura de pacote variável](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview): Captura dados de pacote dentro e fora de uma máquina virtual. Opções avançadas de filtragem e controles de ajuste fino, como configurações de limitação de tempo e tamanho, fornecem versatilidade. Os dados do pacote podem ser armazenados em um repositório de BLOB ou no disco local no formato de arquivo *. Cap* .

- [Verificação de fluxo de IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview): Verifica se um pacote é permitido ou negado com base em parâmetros de pacote de cinco tuplas de informações de fluxo (ou seja, IP de destino, IP de origem, porta de destino, porta de origem e protocolo). Se o pacote for negado por um grupo de segurança, a regra e o grupo que negaram o pacote serão retornados.

- [Próximo salto](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview): Determina o próximo salto para os pacotes que estão sendo roteados na malha de rede do Azure, para que você possa diagnosticar quaisquer rotas definidas pelo usuário mal configuradas.

- [Exibição do grupo de segurança](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview): Obtém as regras de segurança efetivas e aplicadas que são aplicadas em uma VM.

- [Solução de problemas de gateway de rede virtual e conexão](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest): Ajuda a solucionar problemas de gateways e conexões de rede virtual.

- [Limites de assinatura de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): Permite exibir o uso de recursos de rede em relação aos limites.

### <a name="application-insights"></a>Application Insights

O [aplicativo Azure](https://docs.microsoft.com/azure/application-insights/app-insights-overview) insights é um serviço de APM extensível para desenvolvedores da Web em várias plataformas. Use-o para monitorar aplicativos Web em tempo real. Ele detecta automaticamente anomalias de desempenho. inclui ferramentas de análise poderosas para o ajudar a diagnosticar problemas e a compreender o que os utilizadores efetivamente fazem com a sua aplicação.

O Application Insights foi projetado para ajudá-lo a melhorar continuamente o desempenho e a usabilidade.

Ele funciona para aplicativos em uma ampla variedade de plataformas, incluindo .NET, Node. js e Java EE, independentemente de estarem hospedadas localmente ou na nuvem. Ele se integra ao seu processo de DevOps e tem pontos de conexão com várias ferramentas de desenvolvimento.

![Diagrama de Application Insights](./media/azure-log-audit/azure-log-audit-fig6.png)

O Application Insights destina-se à equipa de programação, para o ajudar a compreender o desempenho da aplicação e como está a ser utilizada. Monitoriza:

* **Taxas de solicitação, tempos de resposta e taxas de falha**: Descubra quais páginas são mais populares, em que horários do dia e onde estão os usuários. Veja que páginas têm o melhor desempenho. Se os tempos de resposta e as tarifas de falha ficarem altos quando houver mais solicitações, você poderá ter um problema de origem.

* **Taxas de dependência, tempos de resposta e taxas de falha**: Descubra se os serviços externos estão lentos.

* **Exceções**: Analise as estatísticas agregadas ou selecione instâncias específicas e aprofunde-se no rastreamento de pilha e nas solicitações relacionadas. São reportadas exceções do servidor e do browser.

* **Exibições de página e desempenho de carga**: Obtenha relatórios dos navegadores dos seus usuários.

* **Chamadas AJAX**: Obtenha taxas de página da Web, tempos de resposta e taxas de falha.

* **Contagens de utilizadores e sessões**.

* **Contadores de desempenho**: Obtenha dados de suas máquinas de servidor Windows ou Linux, como CPU, memória e uso de rede.

* **Diagnóstico do host**: Obter dados do Docker ou do Azure.

* **Logs de rastreamento de diagnóstico**: Obter dados de seu aplicativo, para que você possa correlacionar eventos de rastreamento com solicitações.

* **Métricas e eventos personalizados**: Obtenha os dados que você escreve no código do cliente ou do servidor para acompanhar eventos de negócios, como itens vendidos ou jogos ganhos.

A tabela a seguir lista e descreve os cenários de integração:

| Cenário de integração | Descrição |
| --------------------- | :---------- |
|[Mapa do aplicativo](https://docs.microsoft.com/azure/application-insights/app-insights-app-map)|Os componentes da sua aplicação, com as principais métricas e alertas.|
|[Pesquisa de diagnóstico para dados de instância](https://docs.microsoft.com/azure/application-insights/app-insights-diagnostic-search)| Procure e filtre eventos como pedidos, exceções, chamadas de dependências, rastreios de registo e visualizações de página.|
|[Metrics Explorer para dados agregados](https://docs.microsoft.com/azure/azure-monitor/app/metrics-explorer)|Explore, filtre e segmente dados agregados, como taxas de pedidos, falhas e exceções, tempos de resposta e tempos de carregamento de páginas.|
|[Dashboards](https://docs.microsoft.com/azure/azure-monitor/app/overview-dashboard)|Combine dados de vários recursos e partilhe-os com outras pessoas. Ideais para aplicações com vários componentes e para visualização contínua na sala de equipa.|
|[Live Metrics Stream](https://docs.microsoft.com/azure/azure-monitor/app/live-stream)|Quando implementa uma compilação nova, veja estes indicadores de desempenho em tempo quase real, para ter a certeza de que está tudo a funcionar conforme esperado.|
|[Análise](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)|Responda a perguntas difíceis sobre o desempenho e a utilização da sua aplicação através desta poderosa linguagem de consultas.|
|[Alertas automáticos e manuais](https://docs.microsoft.com/azure/application-insights/app-insights-alerts)|Os alertas automáticos se adaptam aos padrões normais do aplicativo de telemetria e são disparados quando há algo fora do padrão usual. Também pode definir alertas em níveis específicos de métricas personalizadas ou padrão.|
|[Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)|Exibir dados de desempenho no código. Aceda ao código a partir dos rastreios de pilha.|
|[Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)|Integre métricas de utilização com outras métricas de business intelligence.|
|[REST API](https://dev.applicationinsights.io/)|Escreva código para executar consultas nas métricas e nos dados não processados.|
|[Exportação contínua](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry)|Exportação em massa de dados brutos para armazenamento quando ele chega.|

### <a name="azure-security-center-alerts"></a>Alertas da central de segurança do Azure

A detecção de ameaças da central de segurança do Azure funciona coletando automaticamente informações de segurança de seus recursos do Azure, da rede e das soluções de parceiros conectadas. Analisa esta informação, muitas vezes correlacionando informações de várias origens, para identificar ameaças. Os alertas de segurança são prioritários no Centro de Segurança, juntamente com recomendações sobre como remediar a ameaça. Para obter mais informações, consulte [central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro).

![Diagrama da central de segurança do Azure](./media/azure-log-audit/azure-log-audit-fig7.png)

O Centro de Segurança emprega análises de segurança avançadas, que ultrapassam as abordagens baseadas na assinatura. Ele se aplica a grandes volumes de dados e tecnologias de [aprendizado de máquina](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) para avaliar eventos em toda a malha de nuvem. Dessa forma, ele detecta ameaças que seriam impossíveis de identificar usando abordagens manuais e prevendo a evolução dos ataques. Estas análises de segurança incluem:

* **Inteligência de ameaças integrada**: Procura atores ruins conhecidos aplicando inteligência contra ameaças globais de produtos e serviços da Microsoft, a DCU (unidade de crimes digitais da Microsoft), o Microsoft Security Response Center (MSRC) e feeds externos.

* **Análise comportamental**: Aplica padrões conhecidos para descobrir comportamentos mal-intencionados.

* **Detecção**de anomalias: Usa a criação de perfil estatística para criar uma linha de base histórica. Alerta sobre os desvios de linhas de base estabelecidos em conformidade com um potencial vetor de ataque.

Muitas operações de segurança e equipes de resposta a incidentes contam com uma solução SIEM como ponto de partida para a triagem e a investigação de alertas de segurança. Com a integração de log do Azure, você pode sincronizar alertas da central de segurança e eventos de segurança de máquina virtual, coletados pelo diagnóstico do Azure e logs de auditoria, com seus logs de Azure Monitor ou solução SIEM quase em tempo real.

## <a name="azure-monitor-logs"></a>Registos do Azure Monitor

Os logs de Azure Monitor são um serviço no Azure que ajuda a coletar e analisar dados gerados pelos recursos em seus ambientes de nuvem e locais. Ele fornece informações em tempo real usando a pesquisa integrada e painéis personalizados para analisar rapidamente milhões de registros em todas as suas cargas de trabalho e servidores, independentemente de sua localização física.

![Diagrama de logs de Azure Monitor](./media/azure-log-audit/azure-log-audit-fig8.png)

No centro de Azure Monitor logs está o espaço de trabalho Log Analytics, que é hospedado no Azure. Os logs de Azure Monitor coletam dados no espaço de trabalho de fontes conectadas Configurando fontes de dados e adicionando soluções à sua assinatura. As fontes de dados e soluções criam diferentes tipos de registro, cada um com seu próprio conjunto de propriedades. Mas as fontes e soluções ainda podem ser analisadas juntas em consultas ao espaço de trabalho. Esse recurso permite que você use as mesmas ferramentas e métodos para trabalhar com uma variedade de dados coletados por uma variedade de fontes.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

As fontes conectadas são os computadores e outros recursos que geram os dados coletados pelos logs de Azure Monitor. As fontes podem incluir agentes instalados em computadores [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) e [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) que se conectam diretamente ou agentes em [um grupo de gerenciamento System Center Operations Manager conectado](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). Os logs de Azure Monitor também podem coletar dados de uma [conta de armazenamento do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage).

As [fontes de dados](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources) são os vários tipos de dados que são coletados de cada fonte conectada. As fontes incluem eventos [e dados de desempenho](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters) de agentes do [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) e do Linux, além de fontes como [logs do IIS](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs) e [logs de texto personalizados](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-custom-logs). Pode configurar cada origem de dados que pretende recolher e a configuração é entregue automaticamente a cada origem ligada.

Há quatro maneiras de [coletar logs e métricas para os serviços do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage):

* Diagnóstico do Azure direto para logs de Azure Monitor (**diagnóstico** na tabela a seguir)

* Diagnóstico do Azure o armazenamento do Azure para Azure Monitor logs (**armazenamento** na tabela a seguir)

* Conectores para serviços do Azure (**conector** na tabela a seguir)

* Scripts para coletar e postar dados em logs de Azure Monitor (células em branco na tabela a seguir e para serviços que não estão listados)

| Serviço | Tipo de recurso | Registos | Métricas | Solução |
| :------ | :------------ | :--- | :------ | :------- |
|Gateway de Aplicação do Azure| Microsoft.Network/<br>applicationGateways|  Diagnóstico|Diagnóstico|    [Aplicativo Azure](https://docs.microsoft.com/azure/azure-monitor/insights/azure-networking-analytics) [Análise de gateway](https://docs.microsoft.com/azure/azure-monitor/insights/azure-networking-analytics#azure-application-gateway-analytics-solution-in-azure-monitor)|
|Application Insights||     Conector|  Conector|  [Application insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) [Conector (versão prévia)](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)|
|Contas de automação do Azure| Microsoft. Automation/<br>automationAccounts|    Diagnóstico||       [Mais informações](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|
|Contas do lote do Azure|  Microsoft.Batch/<br>batchAccounts|  Diagnóstico|    Diagnóstico||
|Serviços cloud clássico||       Armazenamento||       [Mais informações](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage-iis-table)|
|Serviços Cognitivos|    Microsoft.CognitiveServices/<br>accounts|       Diagnóstico|||
|Azure Data Lake Analytics| Microsoft.DataLakeAnalytics/<br>accounts|   Diagnóstico|||
|Azure Data Lake Store| Microsoft.DataLakeStore/<br>accounts|   Diagnóstico|||
|Namespace do hub de eventos do Azure| Microsoft.EventHub/<br>namespaces|  Diagnóstico|    Diagnóstico||
|Azure IoT Hub| Microsoft.Devices/<br>IotHubs||     Diagnóstico||
|Azure Key Vault|   Microsoft.KeyVault/<br>cofres|  Diagnóstico  || [Análise do Cofre de Chaves](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault)|
|Azure Load Balancer|   Microsoft.Network/<br>loadBalancers|    Diagnóstico|||
|Azure Logic Apps|  Microsoft.Logic/<br>fluxos|  Diagnóstico|    Diagnóstico||
||Microsoft.Logic/<br>integrationAccounts||||
|Grupos de Segurança de Rede|   Microsoft.Network/<br>networksecuritygroups|Diagnóstico||   [Análise do grupo de segurança de rede do Azure](https://docs.microsoft.com/azure/azure-monitor/insights/azure-networking-analytics#azure-application-gateway-and-network-security-group-analytics)|
|Cofres de recuperação|   Microsoft.RecoveryServices/<br>cofres|||[Análise (pré-visualização) de serviços de recuperação do Azure](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
|Procurar serviços|   Microsoft.Search/<br>searchServices|    Diagnóstico|    Diagnóstico||
|Espaço de nomes do Service Bus| Microsoft.ServiceBus/<br>namespaces|    Diagnóstico|Diagnóstico|    [Análise do Service Bus (pré-visualização)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
|Service Fabric||       Armazenamento||    [Análise do Service Fabric (pré-visualização)](https://docs.microsoft.com/azure/log-analytics/log-analytics-service-fabric)|
|SQL (v12)| Microsoft.Sql/<br>servidores<br>databases||       Diagnóstico||
||Microsoft.Sql/<br>servidores<br>elasticPools||||
|Armazenamento|||         Script| [Análise de armazenamento do Azure (pré-visualização)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution)|
|Máquinas Virtuais do Azure|    Microsoft.Compute/<br>VirtualMachines|  Extensão|  Extensão||
||||Diagnóstico||
|Conjuntos de dimensionamento de máquinas virtuais|    Microsoft.Compute/<br>VirtualMachines    ||Diagnóstico||
||Microsoft.Compute/<br>virtualMachineScaleSets/<br>VirtualMachines||||
|Farms de servidores Web|Microsoft.Web/<br>serverfarms||   Diagnóstico
|Sites|  Microsoft.Web/<br>sites ||      Diagnóstico|    [Mais informações](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webappazure-oms-monitoring)|
||Microsoft.Web/<br>sites<br>períodos||||


## <a name="log-integration-with-on-premises-siem-systems"></a>Integração de log com sistemas SIEM locais

Com a integração de log do Azure, você pode integrar logs brutos de seus recursos do Azure com seu sistema SIEM local (sistema de informações de segurança e de gerenciamento de eventos). Os downloads do AzLog foram desabilitados em 27 de junho de 2018. Para obter diretrizes sobre o que fazer ao mover para frente examine o [Azure monitor de envio para integração com ferramentas Siem](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

![Diagrama de integração de log](./media/azure-log-audit/azure-log-audit-fig9.png)

A integração de log coleta diagnóstico do Azure de suas máquinas virtuais do Windows, logs de atividades do Azure, alertas da central de segurança do Azure e logs do provedor de recursos do Azure. Essa integração fornece um painel unificado para todos os seus ativos, sejam eles locais ou na nuvem, para que você possa agregar, correlacionar, analisar e alertar para eventos de segurança.

A integração de log atualmente dá suporte à integração de logs de atividades do Azure, logs de eventos do Windows de máquinas virtuais do Windows com sua assinatura do Azure, alertas da central de segurança do Azure, logs de diagnóstico do Azure e logs de auditoria do Azure AD.

| Tipo de log | Logs de Azure Monitor que dão suporte a JSON (Splunk, ArcSight e IBM QRadar) |
| :------- | :-------------------------------------------------------- |
|Logs de auditoria do Azure AD|   Sim|
|Registos de atividade| Sim|
|Alertas da central de segurança |Sim|
|Logs de diagnóstico (logs de recursos)|  Sim|
|Logs de VM|   Sim, por meio de eventos encaminhados e não por meio de JSON|

[Introdução à integração de log do Azure](https://docs.microsoft.com/azure/security/security-azure-log-integration-get-started): Este tutorial orienta você pela instalação da integração de log do Azure e integração dos logs do armazenamento do Azure, dos logs de atividades do Azure, dos alertas da central de segurança do Azure e dos logs de auditoria do Azure AD.

Cenários de integração do SIEM:

* [Etapas de configuração do parceiro](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/): Esta postagem de Blog mostra como configurar a integração de log do Azure para trabalhar com as soluções de parceiros Splunk, HP ArcSight e IBM QRadar.

* [Perguntas frequentes sobre integração de log do Azure](https://docs.microsoft.com/azure/security/security-azure-log-integration-faq): Este artigo responde a perguntas sobre a integração de log do Azure.

* [Integrando alertas da central de segurança com a integração de log do Azure](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration): Este artigo discute como sincronizar alertas da central de segurança, os eventos de segurança de máquina virtual coletados pelos logs de diagnóstico do Azure e os logs de auditoria do Azure com seus logs de Azure Monitor ou solução SIEM.

## <a name="next-steps"></a>Passos seguintes

- [Auditoria e registro em log](https://docs.microsoft.com/azure/security/security-management-and-monitoring-overview): Proteja os dados mantendo a visibilidade e respondendo rapidamente aos alertas de segurança oportunos.

- [Log de segurança e coleta de log de auditoria no Azure](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/): Aplique essas configurações para garantir que suas instâncias do Azure estejam coletando os logs de auditoria e segurança corretos.

- [Definir configurações de auditoria para um conjunto de sites](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US): Se você for um administrador de conjunto de sites, recupere o histórico de ações de usuários individuais e o histórico de ações executadas durante um determinado intervalo de datas. 

- [Pesquise o log de auditoria no centro de conformidade e segurança do Office 365](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US): Use o Centro de Conformidade e Segurança do Office 365 para pesquisar o log de auditoria unificada e exibir a atividade de usuário e administrador na sua organização do Office 365.


