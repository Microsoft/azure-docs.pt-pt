---
title: Visão geral dos logs de recursos do Azure | Microsoft Docs
description: Entenda os serviços com suporte e o esquema de eventos para logs de recursos do Azure.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: reference
author: rboucher
ms.author: robb
ms.date: 09/20/2019
ms.openlocfilehash: a418e3d1a59379284422d1d24c1457ab61d84a4c
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72551765"
---
# <a name="azure-resource-logs-overview"></a>Visão geral dos logs de recursos do Azure
Os logs de recursos do Azure são [os logs de plataforma](platform-logs-overview.md) emitidos pelos recursos do Azure que descrevem sua operação interna. Todos os logs de recursos compartilham um esquema comum de nível superior com a flexibilidade de cada serviço para emitir propriedades exclusivas para seus próprios eventos.

> [!NOTE]
> Os logs de recursos eram anteriormente conhecidos como logs de diagnóstico.

## <a name="collecting-resource-logs"></a>Coletando logs de recursos
Os logs de recursos são gerados automaticamente por recursos do Azure com suporte, mas não são coletados a menos que você os Configure usando uma [configuração de diagnóstico](diagnostic-settings.md). Crie uma configuração de diagnóstico para cada recurso do Azure para encaminhar os logs para os seguintes destinos:

| Destino | Cenário |
|:---|:---|:---|
| [Área de trabalho do Log Analytics](resource-logs-collect-storage.md) | Analise os logs com outros dados de monitoramento e aproveite Azure Monitor recursos como consultas de log e alertas de log. |
| [Armazenamento do Azure](archive-diagnostic-logs.md) | Arquive os logs para auditoria ou backup. |
| [Hub de eventos](resource-logs-stream-event-hubs.md) | Transmita os logs para sistemas de registro em log e telemetria de terceiros.  |

## <a name="compute-resources"></a>Recursos de computação
Os logs de recursos diferem dos logs no nível do sistema operacional convidado nos recursos de computação do Azure. Os recursos de computação exigem um agente para coletar logs e métricas de seu sistema operacional convidado, incluindo dados como logs de eventos, syslog e contadores de desempenho. Use a [extensão de diagnóstico](agents-overview.md#azure-diagnostic-extension) para rotear dados de log de máquinas virtuais do Azure e o [agente de log Analytics](agents-overview.md#log-analytics-agent) para coletar logs e métricas de máquinas virtuais no Azure, em outras nuvens e localmente em um espaço de trabalho log Analytics. Consulte [fontes de dados de monitoramento para obter Azure monitor](data-sources.md) para obter detalhes.

## <a name="resource-logs-schema"></a>Esquema de logs de recursos
Cada serviço do Azure tem seu próprio esquema quando os logs de recursos são gravados em um dos destinos, mas todos compartilham um esquema de nível superior na tabela a seguir. Consulte [esquemas específicos do serviço](#service-specific-schemas) abaixo para obter links para o esquema para cada serviço. 

| Nome | Obrigatório/opcional | Descrição |
|---|---|---|
| hora | Obrigatório | O carimbo de data/hora (UTC) do evento. |
| resourceId | Obrigatório | A ID de recurso do recurso que emitiu o evento. Para serviços de locatário, esse é o formato/Tenants/Tenant-ID/Providers/Provider-Name. |
| tenantId | Necessário para logs de locatário | A ID de locatário do locatário de Active Directory ao qual esse evento está vinculado. Essa propriedade é usada somente para logs em nível de locatário, ela não aparece em logs de nível de recurso. |
| operationName | Obrigatório | O nome da operação representada por este evento. Se o evento representar uma operação RBAC, esse será o nome da operação RBAC (por exemplo, Microsoft. Storage/storageAccounts/blobservices/BLOBs/leitura). Normalmente modelados na forma de uma operação do Resource Manager, mesmo que não sejam operações reais documentadas do Resource Manager (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`) |
| operationVersion | Opcional | A versão de API associada à operação, se a operationName tiver sido executada usando uma API (por exemplo, `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Se não houver nenhuma API que corresponda a essa operação, a versão representará a versão dessa operação, caso as propriedades associadas à operação sejam alteradas no futuro. |
| categoria | Obrigatório | A categoria de log do evento. Categoria é a granularidade na qual você pode habilitar ou desabilitar logs em um recurso específico. As propriedades que aparecem dentro do blob de propriedades de um evento são as mesmas dentro de uma categoria de log e tipo de recurso específicos. As categorias de log típicas são "auditoria" "operacional" "execução" e "solicitação". |
| resultType | Opcional | O status do evento. Os valores típicos incluem iniciado, em andamento, bem-sucedido, com falha, ativo e resolvido. |
| resultSignature | Opcional | O substatus do evento. Se essa operação corresponder a uma chamada à API REST, esse será o código de status HTTP da chamada REST correspondente. |
| resultDescription | Opcional | A descrição de texto estático desta operação, por exemplo, "Obter arquivo de armazenamento". |
| durationMs | Opcional | A duração da operação em milissegundos. |
| callerIpAddress | Opcional | O endereço IP do chamador, se a operação corresponder a uma chamada à API que venha de uma entidade com um endereço IP disponível publicamente. |
| correlationId | Opcional | Um GUID usado para agrupar um conjunto de eventos relacionados. Normalmente, se dois eventos tiverem a mesma operationName, mas dois status diferentes (por exemplo, "Iniciado" e "êxito"), eles compartilham a mesma ID de correlação. Isso também pode representar outras relações entre eventos. |
| identidade | Opcional | Um blob JSON que descreve a identidade do usuário ou aplicativo que realizou a operação. Normalmente, isso incluirá a autorização e o token de declarações/JWT do Active Directory. |
| Nível | Opcional | O nível de severidade do evento. Deve ser uma das informações, aviso, erro ou crítico. |
| localização | Opcional | A região do recurso que emite o evento, por exemplo, "Leste dos EUA" ou "sul da França" |
| propriedades | Opcional | Todas as propriedades estendidas relacionadas a esta determinada categoria de eventos. Todas as propriedades personalizadas/exclusivas devem ser colocadas dentro dessa "parte B" do esquema. |

## <a name="service-specific-schemas"></a>Esquemas específicos do serviço
O esquema para os logs de diagnóstico de recurso varia de acordo com o tipo de recurso que é definido pela propriedade `resourceId`) e as propriedades de `category`. A lista a seguir mostra todos os serviços do Azure que dão suporte a logs de recursos com links para o serviço e esquema específico de categoria, quando disponível.

| Serviço | Documentos de & de esquema |
| --- | --- |
| Azure Active Directory | [Visão geral](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), esquema de [log de auditoria](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) e [esquemas de entradas](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| Gestão de API | [Logs de diagnóstico do gerenciamento de API](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Gateways da Aplicação |[Log de diagnóstico para o gateway de aplicativo](../../application-gateway/application-gateway-diagnostics.md) |
| Automatização do Azure |[Log Analytics para automação do Azure](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Log de diagnóstico do lote do Azure](../../batch/batch-diagnostics.md) |
| Base de Dados do Azure para MySQL | [Logs de diagnóstico do banco de dados do Azure para MySQL](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Base de Dados do Azure para PostgreSQL | [Logs de diagnóstico do banco de dados do Azure para PostgreSQL](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Serviços Cognitivos | [Log de diagnóstico para serviços cognitivas do Azure](../../cognitive-services/diagnostic-logging.md) |
| Rede de Entrega de Conteúdos | [Logs de diagnóstico do Azure para CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Log de Azure Cosmos DB](../../cosmos-db/logging.md) |
| Gestor de Dados | [Monitorar fábricas de dados usando Azure Monitor](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Accessing diagnostics logs for Azure Data Lake Analytics (Aceder aos registos de diagnóstico do Azure Data Lake Analytics)](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Arquivo do Data Lake |[Acessando logs de diagnóstico para Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Hubs de Eventos |[Logs de diagnóstico dos hubs de eventos do Azure](../../event-hubs/event-hubs-diagnostic-logs.md) |
| Express Route | Esquema não disponível. |
| Azure Firewall | Esquema não disponível. |
| Hub IoT | [Operações do Hub IoT](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Registo do Cofre de Chaves do Azure](../../key-vault/key-vault-logging.md) |
| Load Balancer |[Log Analytics para o Balanceador de Carga do Azure](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Esquema de controlo personalizado B2B de Aplicações Lógicas](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Grupos de Segurança de Rede |[Análise de registos para grupos de segurança de rede (NSGs) (Log analytics for network security groups (NSGs))](../../virtual-network/virtual-network-nsg-manage-log.md) |
| Proteção contra DDOS | [Gerenciar a proteção contra DDoS do Azure Standard](../../virtual-network/manage-ddos-protection.md) |
| Power BI Dedicado | [Log de diagnóstico para Power BI Embedded no Azure](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Serviços de Recuperação | [Modelo de dados para o backup do Azure](../../backup/backup-azure-reports-data-model.md)|
| Procurar |[Habilitando e usando o Análise de Tráfego de pesquisa](../../search/search-traffic-analytics.md) |
| Service Bus |[Logs de diagnóstico do barramento de serviço do Azure](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Log de diagnóstico do banco de dados SQL do Azure](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Logs de diagnóstico do trabalho](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Gestor de Tráfego | [Esquema de log do Gerenciador de tráfego](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Redes Virtuais | Esquema não disponível. |
| Gateways de Rede Virtual | Esquema não disponível. |

## <a name="next-steps"></a>Próximos Passos

* [Saiba mais sobre outros logs da plataforma Azure](platform-logs-overview.md) que você pode usar para monitorar o Azure.
