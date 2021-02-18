---
title: Azure Resource Logs suportados serviços e esquemas
description: Compreenda os serviços suportados e o esquema de eventos para registos de recursos Azure.
ms.subservice: logs
ms.topic: reference
ms.date: 09/01/2020
ms.openlocfilehash: b37535b67ed1873c5a492e28e6bf4da8c67c7e5d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100616472"
---
# <a name="common-and-service-specific-schema-for-azure-resource-logs"></a>Esquema comum e específico de serviço para registos de recursos Azure

> [!NOTE]
> Os registos de recursos eram anteriormente conhecidos como registos de diagnóstico. O nome foi alterado em outubro de 2019, uma vez que os tipos de registos recolhidos pelo Azure Monitor mudaram para incluir mais do que apenas o recurso Azure. Além disso, a lista de categorias de registo de recursos que poderia recolher costumava estar listada neste artigo. Foram transferidos para [categorias de registo de recursos.](resource-logs-categories.md) 

[Os registos de recursos do Azure Monitor](../essentials/platform-logs-overview.md) são registos emitidos pelos serviços da Azure que descrevem o funcionamento desses serviços ou recursos. Todos os registos de recursos disponíveis através do Azure Monitor partilham um esquema comum de alto nível, com flexibilidade para cada serviço emitir propriedades únicas para os seus próprios eventos.

Uma combinação do tipo de recurso (disponível na `resourceId` propriedade) e o `category` esquema de identificação única. Este artigo descreve o esquema de nível superior para registos de recursos e ligações ao esquema para cada serviço.


## <a name="top-level-common-schema"></a>Esquema comum de alto nível

| Name | Obrigatório/Opcional | Description |
|---|---|---|
| hora | Necessário | A hora de jogo (UTC) do evento. |
| resourceId | Necessário | O ID de recursos do recurso que emitia o evento. Para os serviços de arrendamento, este é do formulário /inquilinos/inquilino-id/provedor/nome de provedor. |
| inquilinoId | Obrigatório para registos de inquilinos | O inquilino identificação do inquilino ative diretório a que este evento está ligado. Esta propriedade é usada apenas para registos ao nível do inquilino, não aparece em registos de nível de recursos. |
| operationName | Necessário | O nome da operação representada por este evento. Se o evento representar uma operação RBAC Azure, este é o nome de operação Azure RBAC (por exemplo, Microsoft.Storage/storageAccounts/blobServices/blobs/Read). Tipicamente modelado sob a forma de uma operação de Gestor de Recursos, mesmo que não sejam operações de Gestor de Recursos documentados reais ( `Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>` ) |
| operationVersion | Opcional | A versão api associada à operação, se a operaçãoName foi realizada com uma API (por exemplo, `http://myservice.windowsazure.net/object?api-version=2016-06-01` ). Se não houver API que corresponda a esta operação, a versão representa a versão dessa operação no caso de as propriedades associadas à alteração da operação no futuro. |
| categoria | Necessário | A categoria de registo do evento. Categoria é a granularidade em que pode ativar ou desativar registos num determinado recurso. As propriedades que aparecem dentro da bolha de propriedades de um evento são as mesmas dentro de uma determinada categoria de registo e tipo de recurso. As categorias típicas de registo são "Auditoria" "Operacional" "Execução" e "Pedido". |
| resultType | Opcional | O estado do evento. Os valores típicos incluem Iniciado, Em Progresso, Bem Sucedido, Falhado, Ativo e Resolvido. |
| resultSignature | Opcional | O sub-estado do evento. Se esta operação corresponder a uma chamada de API REST, este campo é o código de estado HTTP da chamada REST correspondente. |
| resultDescription | Opcional | A descrição estática de texto desta operação, por exemplo, "Obter ficheiro de armazenamento". |
| durationMs | Opcional | A duração da operação em milissegundos. |
| callerIpAddress | Opcional | O endereço IP do chamador, se a operação corresponder a uma chamada de API que viria de uma entidade com um endereço IP disponível ao público. |
| correlationId | Opcional | Um GUID usado para agrupar um conjunto de eventos relacionados. Normalmente, se dois eventos tiverem a mesma operaçãoName mas dois status diferentes (por exemplo "Iniciado" e "Bem sucedido"), partilham o mesmo ID de correlação. Isto também pode representar outras relações entre eventos. |
| identidade | Opcional | Uma bolha JSON que descreve a identidade do utilizador ou aplicação que realizou a operação. Tipicamente, este campo inclui a autorização e reclamações /JWT token de diretório ativo. |
| Level | Opcional | O nível de gravidade do evento. Deve ser um dos Informativos, Aviso, Erro ou Crítico. |
| localização | Opcional | A região do recurso que emite o evento, por exemplo "East US" ou "France South" |
| propriedades | Opcional | Quaisquer propriedades estendidas relacionadas com esta categoria particular de eventos. Todas as propriedades personalizadas/únicas devem ser colocadas dentro desta "Parte B" do esquema. |

## <a name="service-specific-schemas"></a>Esquemas específicos de serviço

O esquema para registos de recursos varia consoante a categoria de recursos e registos. Esta lista mostra serviços que disponibilizam registos de recursos e ligações ao sistema de serviço e à categoria específica, sempre que disponível. Esta lista está sempre a mudar à medida que novos serviços são adicionados, por isso, se não vir o que precisa abaixo, use um motor de busca para descobrir documentação adicional. Sinta-se livre para abrir um edição do GitHub sobre este artigo para que possamos atualizá-lo.

| Serviço | Schema & Docs |
| --- | --- |
| Azure Active Directory | [Visão geral](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), [Esquema de registo de auditoria](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) e esquema de [login](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | [Serviços de Análise Azure - Registo de diagnóstico de configuração](../../analysis-services/analysis-services-logging.md) |
| Gestão de API | [Registos de Recursos de Gestão da API](../../api-management/api-management-howto-use-azure-monitor.md#resource-logs) |
| Serviço de Aplicações | [Registos de serviço de aplicações](../../app-service/troubleshoot-diagnostic-logs.md)
| Gateways de Aplicação |[Registo de gateway de aplicações](../../application-gateway/application-gateway-diagnostics.md) |
| Automatização do Azure |[Analítica de registo para Azure Automation](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Registo de lote de Azure](../../batch/batch-diagnostics.md) |
| Serviços Cognitivos | [Registo de Serviços Cognitivos Azure](../../cognitive-services/diagnostic-logging.md) |
| Container Registry | [Registo de registo de contentores Azure](../../container-registry/container-registry-diagnostics-audit-logs.md) |
| Rede de Entrega de Conteúdos | [Registos Azure para CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| Cosmos DB | [Azure Cosmos DB Logging](../../cosmos-db/monitor-cosmos-db.md) |
| Data Factory | [Monitorize fábricas de dados usando o Monitor Azure](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Aceder a registos para Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Storage |[Aceder a registos para a Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Azure Data Explorer | [Registos Azure Data Explorer](/azure/data-explorer/using-diagnostic-logs) |
| Base de Dados do Azure para MySQL | [Base de Dados Azure para registos de diagnóstico mySQL](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Base de Dados do Azure para PostgreSQL | [Base de Dados Azure para registos PostgreSQL](../../postgresql/concepts-server-logs.md#resource-logs) |
| Azure Databricks | [Registo de diagnósticos no Azure Databricks](/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs) |
| Azure Digital Twins | [Configurar diagnósticos de gémeos digitais Azure](../../digital-twins/troubleshoot-diagnostics.md#log-schemas)
| Hubs de Eventos |[Logs de hubs de eventos Azure](../../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | Schema não está disponível. |
| Azure Firewall | Schema não está disponível. |
| Front Door | [Registo para porta da frente](../../frontdoor/front-door-diagnostics.md) |
| IoT Hub | [Operações do Hub IoT](../../iot-hub/monitor-iot-hub-reference.md#resource-logs) |
| Key Vault |[Azure Key Vault Logging](../../key-vault/general/logging.md) |
| Kubernetes Service |[Azure Kubernetes Logging](../../aks/view-master-logs.md#log-event-schema) |
| Balanceador de Carga |[Análise de registos para o Balanceador de Carga do Azure](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Esquema de controlo personalizado B2B de Aplicações Lógicas](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Grupos de Segurança de Rede |[Análise de registos para grupos de segurança de rede (NSGs) (Log analytics for network security groups (NSGs))](../../virtual-network/virtual-network-nsg-manage-log.md) |
| Proteção contra DDoS | [Registo de registo para norma de proteção DDoS Azure](../../ddos-protection/diagnostic-logging.md#log-schemas) |
| Power BI Dedicado | [Registo de Power BI Incorporado em Azure](/power-bi/developer/azure-pbie-diag-logs) |
| Serviços de Recuperação | [Modelo de dados para backup Azure](../../backup/backup-azure-reports-data-model.md)|
| Pesquisar |[Ativar e utilizar a Pesquisa traffic Analytics](../../search/search-traffic-analytics.md) |
| Service Bus |[Registos de ônibus de serviço Azure](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| Base de Dados SQL | [Registo da base de dados Azure SQL](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md) |
| Stream Analytics |[Registos de trabalhos](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Gestor de Tráfego | [Esquema de registo do gestor de tráfego](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Redes Virtuais | Schema não está disponível. |
| Gateways de Rede Virtual | Schema não está disponível. |



## <a name="next-steps"></a>Passos Seguintes

* [Consulte as categorias de registo de recursos que pode recolher](resource-logs-categories.md)
* [Saiba mais sobre registos de recursos](../essentials/platform-logs-overview.md)
* [Fluxo de registos de recursos para **Centros de Eventos**](./resource-logs.md#send-to-azure-event-hubs)
* [Alterar definições de diagnóstico de registo de recursos utilizando a API do Monitor Azure](/rest/api/monitor/diagnosticsettings)
* [Analise os registos do armazenamento do Azure com o Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)
