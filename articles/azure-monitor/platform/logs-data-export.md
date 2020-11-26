---
title: Log Analytics exportação de dados do espaço de trabalho em Azure Monitor (pré-visualização)
description: A exportação de dados do Log Analytics permite-lhe exportar continuamente dados de tabelas selecionadas do seu espaço de trabalho Log Analytics para uma conta de armazenamento Azure ou Azure Event Hubs à medida que são recolhidos.
ms.subservice: logs
ms.topic: conceptual
ms.custom: references_regions, devx-track-azurecli
author: bwren
ms.author: bwren
ms.date: 10/14/2020
ms.openlocfilehash: bd929d06bca370ffab53ce2023188bc12a1d8bd1
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186444"
---
# <a name="log-analytics-workspace-data-export-in-azure-monitor-preview"></a>Log Analytics exportação de dados do espaço de trabalho em Azure Monitor (pré-visualização)
A exportação de dados do espaço de trabalho do Log Analytics no Azure Monitor permite-lhe exportar continuamente dados de tabelas selecionadas no seu espaço de trabalho Log Analytics para uma conta de armazenamento Azure ou Azure Event Hubs à medida que são recolhidos. Este artigo fornece detalhes sobre esta funcionalidade e passos para configurar a exportação de dados nos seus espaços de trabalho.

## <a name="overview"></a>Descrição geral
Uma vez configurada a exportação de dados para o seu espaço de trabalho Log Analytics, quaisquer novos dados enviados para as tabelas selecionadas no espaço de trabalho são automaticamente exportados para a sua conta de armazenamento de hora a hora ou para o seu centro de eventos em tempo quase real.

![Visão geral da exportação de dados](media/logs-data-export/data-export-overview.png)

Todos os dados das tabelas incluídas são exportados sem filtro. Por exemplo, quando configura uma regra de exportação de dados para a tabela *SecurityEvent,* todos os dados enviados para a tabela *SecurityEvent* são exportados a partir do tempo de configuração.


## <a name="other-export-options"></a>Outras opções de exportação
Os dados do espaço de trabalho log Analytics exportam continuamente dados de um espaço de trabalho Log Analytics. Outras opções para exportar dados para cenários específicos incluem:

- Exportação programada de uma consulta de log utilizando uma App Lógica. Isto é semelhante ao recurso de exportação de dados, mas permite-lhe enviar dados filtrados ou agregados para o armazenamento do Azure. Este método, no entanto, está sujeito a [limites](../service-limits.md#log-analytics-workspaces)  de consulta de log Ver [dados do Arquivo do Log Analytics para o armazenamento Azure usando a Logic App](logs-export-logic-app.md).
- Uma vez exportar usando uma App Lógica. Consulte [o conector de registos Azure Monitor para aplicações lógicas e automatização de energia](logicapp-flow-connector.md).
- Uma vez exporta para máquina local usando o script PowerShell. Consulte [Invoke-AzOperationalInsightsQueryExport](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport).


## <a name="current-limitations"></a>Limitações atuais

- Atualmente, a configuração só pode ser executada utilizando pedidos CLI ou REST. Não é possível utilizar o portal Azure ou o PowerShell.
- A ```--export-all-tables``` opção em CLI e REST não é suportada e será removida. Deve fornecer explicitamente a lista de tabelas nas regras de exportação.
- As tabelas suportadas são atualmente limitadas às específicas na secção de [tabelas suportadas](#supported-tables) abaixo. Se a regra de exportação de dados incluir uma tabela não apoiada, a operação terá sucesso, mas nenhum dado será exportado para esse quadro. Se a regra de exportação de dados incluir uma tabela que não existe, falhará com o erro ```Table <tableName> does not exist in the workspace.```
- O seu espaço de trabalho Log Analytics pode estar em qualquer região, exceto no seguinte:
  - Suíça Norte
  - Suíça Oeste
  - Regiões governamentais de Azure
- A conta de armazenamento de destino ou o centro de eventos devem estar na mesma região que o espaço de trabalho Log Analytics.
- Os nomes das tabelas a exportar não podem ser superiores a 60 caracteres para uma conta de armazenamento e não mais de 47 caracteres para um centro de eventos. As tabelas com nomes mais longos não serão exportadas.

> [!NOTE]
> A exportação de dados do Log Analytics escreve dados como blob de apêndice que está atualmente em pré-visualização para Azure Data Lake Storage Gen2. Deve abrir um pedido de apoio antes de configurar a exportação para este armazenamento. Utilize os seguintes detalhes para este pedido.
> - Tipo de problema: técnico
> - Subscrição: A sua subscrição
> - Serviço: Data Lake Storage Gen2
> - Recurso: O nome do seu recurso
> - Resumo: Solicitando o registo de subscrição para aceitar dados da Data Export do Log Analytics.
> - Tipo de problema: Conectividade
> - Subtipo de problema: Problema de conectividade

## <a name="data-completeness"></a>Preencha os dados
A exportação de dados continuará a tentar o envio de dados por um tempo até 30 minutos no caso de o destino não estar disponível. Se ainda estiver indisponível após 30 minutos, os dados serão descartados até que o destino fique disponível.

## <a name="cost"></a>Custo
Atualmente, não existem encargos adicionais para a funcionalidade de exportação de dados. Os preços para a exportação de dados serão anunciados no futuro e um aviso fornecido antes do início da faturação. Se optar por continuar a utilizar a exportação de dados após o período de pré-aviso, será cobrado à taxa aplicável.

## <a name="export-destinations"></a>Destinos de exportação

### <a name="storage-account"></a>Conta de armazenamento
Os dados são enviados para as contas de armazenamento a cada hora. A configuração de exportação de dados cria um recipiente para cada tabela na conta de armazenamento com o nome *am-* seguido pelo nome da tabela. Por exemplo, a tabela *SecurityEvent* enviaria para um contentor chamado *am-SecurityEvent*.

O caminho da conta de armazenamento é *WorkspaceResourceId=/subscrições/subscrição-id/grupos de recursos/ \<resource-group\> /providers/microsoft.operationalinsights/workspaces/ \<workspace\> /y= \<four-digit numeric year\> \<two-digit numeric month\> /m= \<two-digit numeric day\> /d= /h= \<two-digit 24-hour clock hour\> /m=00/PT1H.jsem*. Uma vez que as bolhas de apêndice estão limitadas a 50 mil escritos em armazenamento, o número de bolhas exportadas pode estender-se se o número de apêndices for elevado. O padrão de nomeação para bolhas em tal caso seria PT1H_#.json, onde # é a contagem incremental de bolhas.

O formato de dados da conta de armazenamento são [linhas JSON.](./resource-logs-blob-format.md) Isto significa que cada registo é delimitado por uma nova linha, sem matriz de registos externos e sem vírgulas entre os registos da JSON. 

[![Dados da amostra de armazenamento](media/logs-data-export/storage-data.png)](media/logs-data-export/storage-data.png#lightbox)

A exportação de dados do Log Analytics pode escrever blobs de apêndice para contas de armazenamento imutáveis quando as políticas de retenção baseadas no tempo têm a definição *de admissão Dedesempretes* habilitadas ativadas. Isto permite escrever novos blocos para uma bolha de apêndice, mantendo ao mesmo tempo a proteção e a conformidade da imutabilidade. Ver Permita que as bolhas de [apêndice protegidas escrevam](../../storage/blobs/storage-blob-immutable-storage.md#allow-protected-append-blobs-writes).

### <a name="event-hub"></a>Hub de eventos
Os dados são enviados para o seu centro de eventos em tempo quase real, à medida que chegam ao Azure Monitor. É criado um hub de eventos para cada tipo de dados que exporta com o nome *am-* seguido pelo nome da tabela. Por exemplo, a tabela *SecurityEvent* enviaria para um centro de eventos chamado *am-SecurityEvent*. Se quiser que os dados exportados cheguem a um centro de eventos específico, ou se tiver uma tabela com um nome que exceda o limite de 47 caracteres, pode fornecer o nome do seu próprio centro de eventos e exportar todos os dados para tabelas definidas para ele.

Considerações:
1. O sku 'Basic' event hub suporta [um limite](../../event-hubs/event-hubs-quotas.md#basic-vs-standard-tiers) de tamanho de evento mais baixo e alguns registos no seu espaço de trabalho podem ultrapassá-lo e ser largados. Recomendamos a utilização do centro de eventos 'Standard' ou 'Dedicado' como destino de exportação.
2. O volume de dados exportados aumenta frequentemente ao longo do tempo, e a escala do centro de eventos precisa de ser aumentada para lidar com taxas de transferência maiores e evitar cenários de estrangulamento e latência de dados. Deve utilizar a função de insuflado automático dos Centros de Eventos para aumentar automaticamente e aumentar o número de unidades de produção e satisfazer as necessidades de utilização. Consulte automaticamente as unidades de produção do [Azure Event Hubs](../../event-hubs/event-hubs-auto-inflate.md) para obter mais detalhes.

## <a name="prerequisites"></a>Pré-requisitos
Seguem-se os pré-requisitos que devem ser preenchidos antes de configurar a exportação de dados do Log Analytics.

- A conta de armazenamento e o centro de eventos já devem ser criados e devem estar na mesma região que o espaço de trabalho Log Analytics. Se precisar de replicar os seus dados para outras contas de armazenamento, pode utilizar qualquer uma das opções de [redundância do Azure Storage](../../storage/common/storage-redundancy.md).  
- A conta de armazenamento deve ser StorageV1 ou StorageV2. O armazenamento clássico não é suportado  
- Se configurar a sua conta de armazenamento para permitir o acesso a partir de redes selecionadas, tem de adicionar uma exceção nas definições da sua conta de armazenamento para permitir que o Azure Monitor escreva para o seu armazenamento.

## <a name="enable-data-export"></a>Permitir a exportação de dados
As etapas seguintes devem ser executadas para permitir a exportação de dados do Log Analytics. Consulte as seguintes secções para obter mais detalhes sobre cada uma.

- Registar fornecedor de recursos.
- Permitir serviços de confiança da Microsoft.
- Criar uma ou mais regras de exportação de dados que definam os quadros para exportar e o seu destino.

### <a name="register-resource-provider"></a>Registar o fornecedor de recursos
O seguinte fornecedor de recursos Azure precisa de se registar para a sua subscrição para permitir a exportação de dados do Log Analytics. 

- Microsoft.Insights

Este fornecedor de recursos provavelmente já estará registado para a maioria dos utilizadores do Azure Monitor. Para verificar, **aceda** a Subscrições no portal Azure. Selecione a sua subscrição e, em seguida, clique em **fornecedores** de recursos na secção **Definições** do menu. Localizar **microsoft.insights**. Se o seu estado está **registado,** então já está registado. Caso contrário, clique em **Registar** para registá-lo.

Também pode utilizar qualquer um dos métodos disponíveis para registar um fornecedor de recursos, conforme descrito nos [fornecedores e tipos de recursos Azure](../../azure-resource-manager/management/resource-providers-and-types.md). Segue-se um comando de amostra utilizando o PowerShell:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.insights
```

### <a name="allow-trusted-microsoft-services"></a>Permitir serviços de confiança da Microsoft
Se configurar a sua Conta de Armazenamento para permitir o acesso a partir de redes selecionadas, tem de adicionar uma exceção para permitir que o Azure Monitor escreva na conta. A partir de **Firewalls e redes virtuais** para a sua conta de armazenamento, selecione **Permita que os serviços fidedignos da Microsoft acedam a esta conta de armazenamento.**

[![Firewalls de conta de armazenamento e redes virtuais](media/logs-data-export/storage-account-vnet.png)](media/logs-data-export/storage-account-vnet.png#lightbox)


### <a name="create-or-update-data-export-rule"></a>Criar ou atualizar regra de exportação de dados
Uma regra de exportação de dados define os dados a exportar para um conjunto de tabelas para um único destino. Pode criar uma regra para cada destino.


# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Utilize o seguinte comando CLI para visualizar as tabelas no seu espaço de trabalho. Pode ajudar a copiar as tabelas que deseja e incluir na regra de exportação de dados.

```azurecli
az monitor log-analytics workspace table list -resource-group resourceGroupName --workspace-name workspaceName --query [].name --output table
```

Utilize o seguinte comando para criar uma regra de exportação de dados para uma conta de armazenamento utilizando o CLI.

```azurecli
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $storageAccountId
```

Utilize o seguinte comando para criar uma regra de exportação de dados para um centro de eventos utilizando o CLI.

```azurecli
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $eventHubsNamespacesId
```

# <a name="rest"></a>[REST](#tab/rest)

Utilize o seguinte pedido para criar uma regra de exportação de dados utilizando a API REST. O pedido deve utilizar a autorização de símbolo do portador e a aplicação/json do tipo de conteúdo.

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

O corpo do pedido especifica o destino das tabelas. Segue-se um corpo de amostra para o pedido DEE para uma conta de armazenamento.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name"
        },
        "tablenames": [
            "table1",
            "table2" 
        ],
        "enable": true
    }
}
```

Segue-se um corpo de amostra para o pedido DE REST para um centro de eventos.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.EventHub/namespaces/eventhub-namespaces-name"
        },
        "tablenames": [
            "table1",
            "table2"
        ],
        "enable": true
    }
}
```

Segue-se um corpo de amostra para o pedido DEE para um centro de eventos onde é fornecido o nome do hub do evento. Neste caso, todos os dados exportados são enviados para o centro deste evento.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.EventHub/namespaces/eventhub-namespaces-name",
            "metaData": {
                "EventHubName": "eventhub-name"
        },
        "tablenames": [
            "table1",
            "table2"
        ],
        "enable": true
    }
  }
}
```
---

## <a name="view-data-export-configuration"></a>Ver configuração de exportação de dados

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Utilize o seguinte comando para visualizar a configuração de uma regra de exportação de dados utilizando O LÍI.

```azurecli
az monitor log-analytics workspace data-export show --resource-group resourceGroupName --workspace-name workspaceName --name ruleName
```

# <a name="rest"></a>[REST](#tab/rest)

Utilize o seguinte pedido para visualizar a configuração de uma regra de exportação de dados utilizando a API REST. O pedido deve utilizar a autorização de símbolo do portador.

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```
---

## <a name="disable-an-export-rule"></a>Desativar uma regra de exportação

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

As regras de exportação podem ser desativadas para que pare a exportação quando não precisa de reter dados durante um determinado período, como quando os testes estão a ser realizados. Utilize o seguinte comando para desativar uma regra de exportação de dados utilizando o CLI.

```azurecli
az monitor log-analytics workspace data-export update --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --enable false
```

# <a name="rest"></a>[REST](#tab/rest)

Utilize o seguinte pedido para desativar uma regra de exportação de dados utilizando a API REST. O pedido deve utilizar a autorização de símbolo do portador.

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
Authorization: Bearer <token>
Content-type: application/json

{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name"
        },
        "tablenames": [
"table1",
    "table2" 
        ],
        "enable": false
    }
}
```
---

## <a name="delete-an-export-rule"></a>Eliminar uma regra de exportação

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Utilize o seguinte comando para eliminar uma regra de exportação de dados utilizando o CLI.

```azurecli
az monitor log-analytics workspace data-export delete --resource-group resourceGroupName --workspace-name workspaceName --name ruleName
```

# <a name="rest"></a>[REST](#tab/rest)

Utilize o seguinte pedido para eliminar uma regra de exportação de dados utilizando a API REST. O pedido deve utilizar a autorização de símbolo do portador.

```rest
DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```
---

## <a name="view-all-data-export-rules-in-a-workspace"></a>Ver todas as regras de exportação de dados num espaço de trabalho

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Utilize o seguinte comando para visualizar todas as regras de exportação de dados num espaço de trabalho utilizando OCli.

```azurecli
az monitor log-analytics workspace data-export list --resource-group resourceGroupName --workspace-name workspaceName
```

# <a name="rest"></a>[REST](#tab/rest)

Utilize o seguinte pedido para visualizar todas as regras de exportação de dados num espaço de trabalho utilizando a API REST. O pedido deve utilizar a autorização de símbolo do portador.

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports?api-version=2020-08-01
```
---

## <a name="unsupported-tables"></a>Mesas não suportadas
Se a regra de exportação de dados incluir uma tabela não apoiada, a configuração terá sucesso, mas nenhum dado será exportado para esse quadro. Se a tabela for mais tarde suportada, os seus dados serão exportados nessa altura.

Se a regra de exportação de dados incluir uma tabela que não existe, falhará com o erro ```Table <tableName> does not exist in the workspace.```


## <a name="supported-tables"></a>Mesas apoiadas
As tabelas suportadas estão atualmente limitadas às especificadas abaixo. Todos os dados da tabela serão exportados a menos que sejam especificadas limitações. Esta lista será atualizada à medida que o suporte para tabelas adicionais é adicionado.


| Tabela | Limitações |
|:---|:---|
| AADDomainServicesAccountLogon | |
| AADDomainServicesAbusoManagement | |
| AADDomainServicesDirectoryServiceAccess | |
| AADDomainServicesLogonLogoff | |
| AADDomainServicesPolicyChange | |
| AADDomainServicesPrivilegeUse | |
| AADManagedIdentitySignInLogs | |
| AADNonInteractiveUserSignInLogs | |
| AADProvisioningLogs | |
| AADServicePrincipalSignInLogs | |
| ADAssessmentRecommendation | |
| ADFActivityRun | |
| ADFPipelineRun | |
| ADFTriggerRun | |
| AdReplicationResult | |
| ADSecurityAssessmentRecommendation | |
| ADTDigitalTwinsOperação | |
| ADTEventRoutesOperação | |
| ADTModelsOperação | |
| ADTQueryOperação | |
| ADXCommand | |
| ADXQuery | |
| AegDeliveryFailureLogs | |
| AegPublishFailureLogs | |
| Alerta |Apoio parcial. Alguns dos dados desta tabela são ingeridos através da conta de armazenamento. Atualmente, estes dados não são exportados. |
| Anomalias | |
| ApiManagementGatewayLogs | |
| AppCenterError | |
| AppPlatformSystemLogs | |
| AppServiceAppLogs | |
| AppServiceAuditLogs | |
| AppServiceConsoleLogs | |
| AppServiceFileAuditLogs | |
| AppServiceHTTPLogs | |
| AppServiceIPSecAuditLogs | |
| AppServicePlatformLogs | |
| Auditorias | |
| AutoscaleEvaluationsLog | |
| AutoscaleActionsLog | |
| AWSCloudTrail | |
| AzureAssessmentRecommendation | |
| AzureDevOpsAuditing | |
| ComportamentoNalíticos | |
| BlockchainApplicationLog | |
| BlockchainProxyLog | |
| BlockchainProxyLog | |
| CommonSecurityLog | |
| CommonSecurityLog | |
| Grupo de Computadores | |
| Data de Configuração | Apoio parcial. Alguns dos dados são ingeridos através de serviços internos que não são apoiados para exportação. Atualmente, estes dados não são exportados. |
| ContainerImageInventory | |
| ContentorInventory | |
| ContainerLog | |
| ContentorNodeInventory | |
| ContainerServiceLog | |
| CoreAzureBackup | |
| DatabricksAcontas | |
| DatabricksClusters | |
| DatabricksDBFS | |
| DatabricksInstancePools | |
| DatabricksJobs | |
| DatabricksNotebook | |
| DatabricksSecrets | |
| DatabricksSQLPermissions | |
| DatabricksSH | |
| DatabricksWorkspace | |
| DnsEvents | |
| DnsInventory | |
| Dinâmica365Atividade | |
| Evento | Apoio parcial. Alguns dos dados desta tabela são ingeridos através da conta de armazenamento. Atualmente, estes dados não são exportados. |
| TrocaComendamentRecommendation | |
| Ingestion Falhada | |
| FunÇõesAppLogs | |
| HDInsightAmbariClusterAlerts | |
| HDInsightAmbariSystemMetrics | |
| HDInsightGatewayAuditLogs | |
| HDInsightHadoopAndYarnLogs | |
| HDInsightHadoopAndYarnMetrics | |
| HDInsightHBaseLogs | |
| HDInsightHBaseMetrics | |
| HDInsightHiveAndLLAPLogsSample | |
| HDInsightKafkaLogs | |
| HDInsightKafkaMetrics | |
| HDInsightOozieLogs | |
| HDInsightSecurityLogs | |
| HDInsightSparkApplicationSEvents | |
| HDInsightSparkBlockManagerEvents | |
| HDInsightSparkEnvironmentEvents | |
| HDInsightSparkEventsLog | |
| HDInsightSparkExecutorEvents | |
| HDInsightSparkExtraEvents | |
| HDInsightSparkJobEvents | |
| HDInsightSparkLogs | |
| HDInsightSparkSQLExecutionS | |
| HDInsightSparkStageEvents | |
| HDInsightSparkStageTaskAccumulables | |
| HDInsightSparkTaskEvents | |
| HDInsightStormLogs | |
| HDInsightStormMetrics | |
| HDInsightStormTopologyMetrics | |
| Heartbeat | |
| Marca de Caça | |
| InsightsMetrics | Apoio parcial. Alguns dos dados são ingeridos através de serviços internos que não são apoiados para exportação. Esta parte está em falta nas exportações atualmente. |
| IntuneAuditLogs | |
| IntuneDeviceComplianceOrg | |
| IntuneOperationalLogs | |
| KubeEvents | |
| KubeHealth | |
| KubeMonAgentEvents | |
| KubeNodeInventory | |
| KubePodInventory | |
| KubeServices | |
| KubeServices | |
| LAQueryLogs | |
| McasshadowItReporting | |
| MicrosoftAzureBastionAuditLogs | |
| MicrosoftDataShareReceivedSnapshotLog | |
| MicrosoftDataShareSentSnapshotLog | |
| MicrosoftDataShareShareLog | |
| MicrosoftHealthcareApisAuditLogs | |
| NWConnectionMonitorDestinationListenerResult | |
| NWConnectionMonitordNSResult | |
| NWConnectionMonitorPathResult | |
| NWConnectionMonitorPathResult | |
| NWConnectionMonitorTestResult | |
| NWConnectionMonitorTestResult | |
| OfficeActivity | Apoio parcial. Alguns dos dados para ingeridos via webhooks do Office 365 para Log Analytics. Atualmente, estes dados não são exportados. |
| Operação | Apoio parcial. Alguns dos dados são ingeridos através de serviços internos que não são apoiados para exportação. Atualmente, estes dados não são exportados. |
| Des | Apoio parcial. Apenas os dados de desempenho do Windows são suportados atualmente. Os dados de desempenho do linux não são atualmente exportados. |
| ProteçãoStatus | |
| SCCMAssessmentRecommendation | |
| SCOMAssessmentRecommendation | |
| SecurityAlert | |
| Base de Segurança | |
| SecurityBaselineSummary | |
| Deteceção de Segurança | |
| SecurityEvent | |
| SegurançaIncident | |
| SecurityIoTRawEvent | |
| SegurançaNestedRecommendation | |
| Pré-comenda de Segurança | |
| SfBAssessmentRecommendation | |
| SfBOnlineAssessmentRecommendation | |
| SharePointOnlineAssessmentRecommendation | |
| SignalRServiceDiagnosticLogs | |
| SigninLogs | |
| SpAssessmentRecommendation | |
| SQLAssessmentRecommendation | |
| Ingestion sucedeu | |
| Eventos SynapseGateway | |
| SinapseRBACEvents | |
| Syslog | Apoio parcial. Alguns dos dados desta tabela são ingeridos através da conta de armazenamento. Atualmente, estes dados não são exportados. |
| ThreatIntelligenceIndicator | |
| Atualizar | Apoio parcial. Alguns dos dados são ingeridos através de serviços internos que não são apoiados para exportação. Atualmente, estes dados não são exportados. |
| UpdateRunProgress | |
| UpdateSummary | |
| Utilização | |
| UserAccessAnalytics | |
| UserPeerAnalytics | |
| Lista de observação | |
| WindowsEvent | |
| Parede de Fogo windows | |
| WireData | Apoio parcial. Alguns dos dados são ingeridos através de serviços internos que não são apoiados para exportação. Atualmente, estes dados não são exportados. |
| WorkloadMonitoringPerf | |
| WVDAgentHealthStatus | |
| WVDCheckpoints | |
| WVDConnections | |
| WVDErrors | |
| WVDFeeds | |
| WVDManagement | |


## <a name="next-steps"></a>Passos seguintes

- [Consultar os dados exportados do Azure Data Explorer](azure-data-explorer-query-storage.md).