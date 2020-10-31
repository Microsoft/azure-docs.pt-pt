---
title: Como auditar as operações do avião de controlo da Azure Cosmos DB
description: Saiba como auditar as operações do avião de controlo, tais como adicionar uma região, atualizar o rendimento, a região falhar, adicionar um VNet etc. em Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/05/2020
ms.author: sngun
ms.openlocfilehash: 683fc553e7712e2a760a0af1b601207cb20f2f55
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93092811"
---
# <a name="how-to-audit-azure-cosmos-db-control-plane-operations"></a>Como auditar as operações do avião de controlo da Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

O Plano de Controlo em Azure Cosmos DB é um serviço RESTful que lhe permite realizar um conjunto diversificado de operações na conta Azure Cosmos. Expõe um modelo de recursos públicos (por exemplo: base de dados, conta) e várias operações aos utilizadores finais para realizar ações no modelo de recursos. As operações do avião de controlo incluem alterações na conta ou contentor Azure Cosmos. Por exemplo, operações como criar uma conta Azure Cosmos, adicionar uma região, atualizar o rendimento, a região falhar, adicionar um VNet etc. são algumas das operações do avião de controlo. Este artigo explica como auditar as operações do avião de controlo em Azure Cosmos DB. Pode executar as operações do avião de controlo nas contas da Azure Cosmos utilizando o portal Azure CLI, PowerShell ou Azure, enquanto para contentores, utilize o Azure CLI ou o PowerShell.

Seguem-se alguns cenários de exemplo em que a auditoria das operações dos aviões de controlo é útil:

* Você quer receber um alerta quando as regras de firewall para a sua conta Azure Cosmos são modificadas. O alerta é necessário para encontrar modificações não autorizadas às regras que regem a segurança da rede da sua conta Azure Cosmos e tomar medidas rápidas.

* Você quer receber um alerta se uma nova região for adicionada ou removida da sua conta Azure Cosmos. A adição ou remoção de regiões tem implicações nos requisitos de faturação e soberania de dados. Este alerta irá ajudá-lo a detetar uma adição acidental ou remoção da região na sua conta.

* Quer obter mais detalhes dos registos de diagnóstico sobre o que mudou. Por exemplo, um VNet foi alterado.

## <a name="disable-key-based-metadata-write-access"></a>Desativar metadados baseados em metadados

Antes de auditar as operações do avião de controlo em Azure Cosmos DB, desative os metadados baseados em chaves para escrever o acesso na sua conta. Quando os metadados-chave escrevem o acesso é desativado, os clientes que se conectam à conta Azure Cosmos através de chaves de conta são impedidos de aceder à conta. Pode desativar o acesso de escrita definindo a `disableKeyBasedMetadataWriteAccess` propriedade como verdadeira. Depois de definir esta propriedade, as alterações a qualquer recurso podem ocorrer a partir de um utilizador com o papel e credenciais adequados de controlo de acesso baseado em funções (RBAC). Para saber mais sobre como definir esta propriedade, consulte as [alterações de Prevenção a partir do artigo dos SDKs.](role-based-access-control.md#prevent-sdk-changes) 

Depois de `disableKeyBasedMetadataWriteAccess` ligado, se os clientes baseados em SDK executarem operações de criação ou atualização, um erro *"Operação 'POST' no recurso 'ContainerNameorDatabaseName' não é permitido através do ponto final do Azure Cosmos DB.* Tem de acessar o acesso a tais operações para a sua conta, ou realizar as operações de criação/atualização através do Azure Resource Manager, Azure CLI ou Azure PowerShell. Para voltar a mudar, desative o Desativado MetadataWriteAccess para **falso** utilizando o Azure CLI, conforme descrito no artigo De prevenção do artigo [da Cosmos SDK.](role-based-access-control.md#prevent-sdk-changes) Certifique-se de mudar o valor de `disableKeyBasedMetadataWriteAccess` falso em vez de verdadeiro.

Considere os seguintes pontos ao desligar os metadados, escreva o acesso:

* Avalie e certifique-se de que as suas aplicações não fazem chamadas de metadados que alteram os recursos acima referidos (por exemplo, criar recolha, atualização de produção, ...) utilizando as teclas SDK ou conta.

* Atualmente, o portal Azure utiliza chaves de conta para operações de metadados e, por isso, estas operações serão bloqueadas. Em alternativa, utilize as implementações do modelo Azure CLI, SDKs ou Resource Manager para realizar tais operações.

## <a name="enable-diagnostic-logs-for-control-plane-operations"></a>Permitir registos de diagnóstico para operações de plano de controlo

Pode ativar registos de diagnóstico para operações de plano de controlo utilizando o portal Azure. Após a ativação, os registos de diagnóstico registarão a operação como um par de eventos iniciais e completos com detalhes relevantes. Por exemplo, a *RegionFailoverStart* e *a RegionFailoverComplete* completarão o evento de failover da região.

Utilize as seguintes etapas para permitir o registo de registos nas operações do avião de controlo:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue para a sua conta Azure Cosmos.

1. Abra o painel **de definições de diagnóstico,** forneça um **Nome** para os registos criarem.

1. Selecione **ControlPlaneRequests** para o tipo de registo e selecione a opção **Enviar para Registar Analytics.**

Também pode armazenar os registos numa conta de armazenamento ou transmitir para um centro de eventos. Este artigo mostra como enviar registos para registar análises e, em seguida, questioná-los. Depois de ativar, leva alguns minutos para que os registos de diagnóstico produzam efeitos. Todas as operações do avião de controlo realizadas após esse ponto podem ser rastreadas. A imagem que se segue mostra como permitir registos de planos de controlo:

:::image type="content" source="./media/audit-control-plane-logs/enable-control-plane-requests-logs.png" alt-text="Permitir a exploração de pedidos de plano de controlo":::

## <a name="view-the-control-plane-operations"></a>Ver as operações do avião de controlo

Depois de ligar o registo, utilize os seguintes passos para localizar as operações para uma conta específica:

1. Inscreva-se no [portal Azure](https://portal.azure.com).

1. Abra o **separador Monitor** a partir da navegação à esquerda e, em seguida, selecione o **painel de Registos.** Abre uma UI onde pode facilmente executar consultas com essa conta específica no âmbito. Faça a seguinte consulta para visualizar os registos do avião de controlo:

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="ControlPlaneRequests"
   | where TimeGenerated >= ago(1h)
   ```

As imagens que se seguem captam registos quando um nível de consistência é alterado para uma conta Azure Cosmos:

:::image type="content" source="./media/audit-control-plane-logs/add-ip-filter-logs.png" alt-text="Permitir a exploração de pedidos de plano de controlo":::

As imagens seguintes captam registos quando o espaço-chave ou uma tabela de uma conta Cassandra são criados e quando o resultado é atualizado. Os registos do plano de controlo para criar e atualizar operações na base de dados e o contentor são registados separadamente, como mostrado na imagem seguinte:

:::image type="content" source="./media/audit-control-plane-logs/throughput-update-logs.png" alt-text="Permitir a exploração de pedidos de plano de controlo":::

## <a name="identify-the-identity-associated-to-a-specific-operation"></a>Identificar a identidade associada a uma operação específica

Se pretender depurar ainda mais, pode identificar uma operação específica no **registo de Atividades** utilizando o ID de Atividade ou através do calendário da operação. O timetamp é utilizado para alguns clientes do Gestor de Recursos onde o ID de atividade não é explicitamente passado. O registo de Atividades dá detalhes sobre a identidade com que a operação foi iniciada. A imagem que se segue mostra como utilizar o ID da atividade e encontrar as operações associadas a ele no registo de atividade:

:::image type="content" source="./media/audit-control-plane-logs/find-operations-with-activity-id.png" alt-text="Permitir a exploração de pedidos de plano de controlo":::

## <a name="control-plane-operations-for-azure-cosmos-account"></a>Controle as operações do avião para a conta da Azure Cosmos

Seguem-se as operações do avião de controlo disponíveis ao nível da conta. A maioria das operações são rastreadas ao nível da conta. Estas operações estão disponíveis como métricas no monitor Azure:

* Região adicionada
* Região removida
* Conta eliminada
* Região falhou mais
* Conta criada
* Rede virtual eliminada
* Definições de rede de conta atualizadas
* Definições de replicação de conta atualizadas
* Chaves de conta atualizadas
* Definições de backup de conta atualizadas
* Definições de diagnóstico de conta atualizadas

## <a name="control-plane-operations-for-database-or-containers"></a>Operações de controlo de aviões para base de dados ou contentores

Seguem-se as operações do avião de controlo disponíveis na base de dados e no nível do contentor. Estas operações estão disponíveis como métricas no monitor Azure:

* Base de Dados SQL Criada
* Base de Dados SQL Atualizada
* Produção de base de dados SQL atualizada
* Base de Dados SQL Eliminada
* Recipiente SQL criado
* Recipiente SQL Atualizado
* Produção de contentores SQL atualizada
* Recipiente SQL eliminado
* Cassandra Keyspace Criado
* Cassandra Keyspace Atualizado
* Cassandra Keyspace Throughput Atualizado
* Cassandra Keyspace eliminada
* Tabela Cassandra Criada
* Tabela Cassandra Atualizada
* Produção de tabela Cassandra atualizada
* Tabela Cassandra Eliminada
* Gremlin Base de Dados Criada
* Base de Dados Gremlin Atualizada
* Produção de base de dados gremlin atualizada
* Gremlin Base de Dados Eliminada
* Gremlin Graph Criado
* Gráfico de Gremlin Atualizado
* Throughputo de gráfico gremlin atualizado
* Gremlin Graph Eliminado
* Base de Dados de Mongo Criada
* Base de Dados de Mongo Atualizada
* Produção de base de dados de Mongo atualizada
* Base de Dados de Mongo eliminada
* Coleção Mongo Criada
* Coleção Mongo Atualizada
* Produção de coleção de Mongo atualizada
* Coleção mongo eliminada
* Tabela AzureTable Criada
* Tabela AzureTable atualizada
* Produção de tabela azuretable atualizado
* Tabela AzureTable Eliminada

## <a name="diagnostic-log-operations"></a>Operações de registo de diagnóstico

Seguem-se os nomes de funcionamento nos registos de diagnóstico para diferentes operações:

* RegionAddStart, RegionAddComplete
* RegionRemoveStart, RegionRemoveComplete
* AccountDeleteStart, AccountDeleteComplete
* RegionFailoverStart, RegionFailoverComplete
* AccountCreateStart, AccountCreateComplete
* AccountUpdateStart, AccountUpdateComplete
* VirtualNetworkDeleteStart, VirtualNetworkDeleteComplete
* DiagnosticLogUpdateStart, DiagnosticLogUpdateComplete

Para operações específicas da API, a operação é nomeada com o seguinte formato:

* ApiKind + ApiKindResourceType + OperationType
* ApiKind + ApiKindResourceType + "Produção" + operaçãoType

**Exemplo** 

* CassandraKeyspacesCreate
* CassandraKeyspacesUpdate
* CassandraKeyspacesThroughputUpdate
* SqlContainersUpdate

A propriedade *ResourceDetails* contém todo o corpo de recursos como uma carga útil de pedido e contém todas as propriedades solicitadas para atualizar

## <a name="diagnostic-log-queries-for-control-plane-operations"></a>Consultas de registo de diagnóstico para operações de plano de controlo

Seguem-se alguns exemplos para obter registos de diagnóstico para operações de planos de controlo:

```kusto
AzureDiagnostics 
| where Category startswith "ControlPlane"
| where OperationName contains "Update"
| project httpstatusCode_s, statusCode_s, OperationName, resourceDetails_s, activityId_g
```

```kusto
AzureDiagnostics 
| where Category =="ControlPlaneRequests"
| where TimeGenerated >= todatetime('2020-05-14T17:37:09.563Z')
| project TimeGenerated, OperationName, apiKind_s, apiKindResourceType_s, operationType_s, resourceDetails_s
```

```kusto
AzureDiagnostics 
| where Category =="ControlPlaneRequests"
| where  OperationName startswith "SqlContainersUpdate"
```

```kusto
AzureDiagnostics 
| where Category =="ControlPlaneRequests"
| where  OperationName startswith "SqlContainersThroughputUpdate"
```

Consulta para obter a atividadeId e o chamador que iniciou a operação de eliminação do recipiente:

```kusto
(AzureDiagnostics
| where Category == "ControlPlaneRequests"
| where OperationName == "SqlContainersDelete"
| where TimeGenerated >= todatetime('9/3/2020, 5:30:29.300 PM')
| summarize by activityId_g )
| join (
AzureActivity
| parse HTTPRequest with * "clientRequestId\": \"" activityId_g "\"" * 
| summarize by Caller, HTTPRequest, activityId_g)
on activityId_g
| project Caller, activityId_g
```

Consulta para obter atualizações de índice ou ttl. Em seguida, pode comparar a saída desta consulta com uma atualização anterior para ver a variação no índice ou ttl.

```Kusto
AzureDiagnostics
| where Category =="ControlPlaneRequests"
| where  OperationName == "SqlContainersUpdate"
| project resourceDetails_s
```

**saída:**

```json
{id:skewed,indexingPolicy:{automatic:true,indexingMode:consistent,includedPaths:[{path:/*,indexes:[]}],excludedPaths:[{path:/_etag/?}],compositeIndexes:[],spatialIndexes:[]},partitionKey:{paths:[/pk],kind:Hash},defaultTtl:1000000,uniqueKeyPolicy:{uniqueKeys:[]},conflictResolutionPolicy:{mode:LastWriterWins,conflictResolutionPath:/_ts,conflictResolutionProcedure:}
```

## <a name="next-steps"></a>Passos seguintes

* [Explore o Monitor Azure para Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* [Monitore e depure com métricas em Azure Cosmos DB](use-metrics.md)
