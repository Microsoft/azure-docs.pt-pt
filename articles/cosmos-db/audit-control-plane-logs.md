---
title: Como auditar operações de aviões de controlo da Azure Cosmos DB
description: Saiba como auditar as operações de planos de controlo, tais como adicionar uma região, atualização de entrada, falha da região, adicionar um VNet etc. em Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: sngun
ms.openlocfilehash: d380e4c025b35f0000e13c62422d54dc10079524
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192872"
---
# <a name="how-to-audit-azure-cosmos-db-control-plane-operations"></a>Como auditar operações de aviões de controlo da Azure Cosmos DB

Control Plane in Azure Cosmos DB é um serviço RESTful que lhe permite realizar um conjunto diversificado de operações na conta Azure Cosmos. Expõe um modelo de recursos públicos (por exemplo: base de dados, conta) e várias operações aos utilizadores finais para realizar ações no modelo de recurso. As operações de controlo do avião incluem alterações na conta ou contentor da Azure Cosmos. Por exemplo, operações como criar uma conta Azure Cosmos, adicionar uma região, atualizar o rendimento, falha da região, adicionar um VNet etc. são algumas das operações de controle do avião. Este artigo explica como auditar as operações de controlo de aviões em Azure Cosmos DB. Pode executar as operações de controlo de aviões nas contas Azure Cosmos utilizando o portal Azure CLI, PowerShell ou Azure, enquanto para contentores, utilize o Azure CLI ou o PowerShell.

Seguem-se alguns cenários de exemplo em que a auditoria das operações de controlo dos aviões é útil:

* Você quer receber um alerta quando as regras de firewall para a sua conta Azure Cosmos são modificadas. O alerta é necessário para encontrar modificações não autorizadas às regras que regem a segurança da rede da sua conta Azure Cosmos e tomar medidas rápidas.

* Você quer receber um alerta se uma nova região é adicionada ou removida da sua conta Azure Cosmos. A adição ou remoção de regiões tem implicações nos requisitos de faturação e soberania de dados. Este alerta irá ajudá-lo a detetar uma adição acidental ou remoção da região por conta própria.

* Você quer obter mais detalhes dos registos de diagnóstico sobre o que mudou. Por exemplo, um VNet foi alterado.

## <a name="disable-key-based-metadata-write-access"></a>Desativar o acesso de escrita de metadados baseados na chave

Antes de auditar as operações de controlo de aviões em Azure Cosmos DB, desative os metadados baseados na chave, a escrever acesso na sua conta. Quando o acesso de escrita de metadados baseados na chave é desativado, os clientes que ligam à conta Azure Cosmos através de chaves de conta são impedidos de aceder à conta. Você pode desativar `disableKeyBasedMetadataWriteAccess` o acesso de escrita, definindo a propriedade como verdadeira. Depois de definir esta propriedade, as alterações a qualquer recurso podem ocorrer a partir de um utilizador com a função e credenciais de controlo de acesso baseados em Funções adequadas(RBAC). Para saber mais sobre como definir esta propriedade, consulte as [alterações de Prevenção do artigo dos SDKs.](role-based-access-control.md#preventing-changes-from-cosmos-sdk) Depois de desativar o acesso à escrita, as alterações baseadas em SDK para a entrada, o índice continuará a funcionar.

Considere os seguintes pontos ao desligar o acesso de escrita dos metadados:

* Avalie e certifique-se de que as suas aplicações não fazem chamadas de metadados que alteram os recursos acima referidos (por exemplo, criem recolha, atualização de entrada, ...) utilizando as teclas SDK ou conta.

* Atualmente, o portal Azure utiliza chaves de conta para operações de metadados e, por isso, estas operações serão bloqueadas. Em alternativa, utilize as implementações do modelo Azure CLI, SDKs ou Resource Manager para efetuar tais operações.

## <a name="enable-diagnostic-logs-for-control-plane-operations"></a>Ativar registos de diagnóstico para operações de controlo de aviões

Pode ativar registos de diagnóstico para operações de controlo do avião utilizando o portal Azure. Após a ativação, os registos de diagnóstico registarão a operação como um par de eventos de início e completam eventos com detalhes relevantes. Por exemplo, o *RegionFailoverStart* e *regionFailoverComplete* completarão o evento de failover da região.

Utilize os seguintes passos para permitir a exploração de madeira nas operações de planos de controlo:

1. Assine no [portal Azure](https://portal.azure.com) e navegue para a sua conta Azure Cosmos.

1. Abra o painel de **definições de diagnóstico,** forneça um **Nome** para os registos criarem.

1. Selecione **ControlPlaneRequests** para o tipo de log e selecione a opção **Enviar para Registar Analytics.**

Também pode armazenar os registos numa conta de armazenamento ou transmitir para um centro de eventos. Este artigo mostra como enviar registos para registar análises e, em seguida, questioná-los. Depois de ativar, demora alguns minutos para que os registos de diagnóstico entrem em vigor. Todas as operações de controlo do avião realizadas depois desse ponto podem ser rastreadas. A imagem que se segue mostra como ativar os registos de planos de controlo:

![Ativar o controlo de pedidos de registo de pedidos de aviões](./media/audit-control-plane-logs/enable-control-plane-requests-logs.png)

## <a name="view-the-control-plane-operations"></a>Ver as operações de controlo do avião

Depois de ligar a exploração madeireira, utilize os seguintes passos para rastrear as operações para obter uma conta específica:

1. Assine pelo [portal Azure.](https://portal.azure.com)

1. Abra o separador **Monitor** a partir da navegação à esquerda e, em seguida, selecione o painel de **registos.** Abre um UI onde pode facilmente executar consultas com essa conta específica no âmbito. Faça a seguinte consulta para ver os registos de planos de controlo:

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="ControlPlaneRequests"
   | where TimeGenerated >= ago(1h)
   ```

As seguintes imagens capturam registos quando um VNET é adicionado a uma conta Azure Cosmos:

![Controle os registos de planos quando um VNet é adicionado](./media/audit-control-plane-logs/add-ip-filter-logs.png)

As seguintes imagens capturam registos quando a entrada de uma tabela Cassandra é atualizada:

![Controlar os registos dos planos quando a entrada é atualizada](./media/audit-control-plane-logs/throughput-update-logs.png)

## <a name="identify-the-identity-associated-to-a-specific-operation"></a>Identificar a identidade associada a uma operação específica

Se pretender depurar ainda mais, pode identificar uma operação específica no **registo de Atividade** utilizando o ID de atividade ou pelo carimbo temporal da operação. O timestamp é usado para alguns clientes do Gestor de Recursos onde o ID da atividade não é explicitamente passado. O registo de Atividades dá detalhes sobre a identidade com que a operação foi iniciada. A imagem seguinte mostra como utilizar o ID de atividade e encontrar as operações associadas ao mesmo no registo de Atividade:

![Use o ID de atividade e encontre as operações](./media/audit-control-plane-logs/find-operations-with-activity-id.png)

## <a name="control-plane-operations-for-azure-cosmos-account"></a>Controle operações de aviões para a conta Azure Cosmos

Seguem-se as operações de controlo dos aviões disponíveis ao nível da conta. A maioria das operações são rastreadas ao nível da conta. Estas operações estão disponíveis como métricas no monitor Azure:

* Região adicionada
* Região removida
* Conta eliminada
* Região falhou
* Conta criada
* Rede virtual eliminada
* Definições de rede de conta atualizadas
* Definições de replicação de conta atualizadas
* Chaves de conta atualizadas
* Definições de backup da conta atualizadas
* Configurações de diagnóstico de conta atualizadas

## <a name="control-plane-operations-for-database-or-containers"></a>Controlar as operações dos aviões para bases de dados ou contentores

Seguem-se as operações de controlo dos aviões disponíveis na base de dados e no nível do contentor. Estas operações estão disponíveis como métricas no monitor Azure:

* Base de Dados SQL Atualizada
* Recipiente SQL atualizado
* Entrada de base de dados SQL atualizada
* Entrada de recipiente SQL atualizada
* Base de dados SQL eliminada
* Recipiente SQL eliminado
* Cassandra Keyspace Atualizado
* Tabela Cassandra Atualizada
* Cassandra Keyspace Entrada atualizada
* Entrada de tabela Cassandra atualizada
* Cassandra Keyspace apagada
* Mesa Cassandra apagada
* Base de Dados Gremlin Atualizada
* Gráfico de Gremlin Atualizado
* Entrada de base de dados Gremlin atualizada
* Entrada de gráfico de gremlin atualizada
* Base de Dados Gremlin eliminada
* Gráfico de Gremlin eliminado
* Base de Dados de Mongo Atualizada
* Coleção Mongo Atualizada
* Entrada de base de dados de Mongo atualizada
* Mongo Collection Throughput Atualizado
* Base de Dados mongo eliminada
* Coleção Mongo apagada
* Tabela AzureTable Atualizada
* Entrada de tabela azuretable atualizada
* Tabela AzureTable eliminada

## <a name="diagnostic-log-operations"></a>Operações de registo de diagnóstico

Seguem-se os nomes de operação em registos de diagnóstico para diferentes operações:

* RegionAddStart, RegionAddComplete
* RegionRemoveStart, RegionRemoveComplete
* AccountDeleteStart, AccountDeleteComplete
* RegiãoFailoverStart, RegionFailoverComplete
* AccountCreateStart, AccountCreateComplete
* AccountUpdateStart, AccountUpdateComplete
* VirtualNetworkDeleteStart, VirtualNetworkDeleteComplete
* DiagnosticLogUpdateStart, DiagnosticLogUpdateComplete

Para operações específicas da API, a operação é nomeada com o seguinte formato:

* ApiKind + ApiKindResourceType + OperationType + Início/Completo
* ApiKind + ApiKindResourceType + "Reposição" + operaçãoType + Início/Completo

**Exemplo** 

* CassandraKeyspacesUpdateStart, CassandraKeyspacesUpdateComplete
* CassandraKeyspacesThroughputUpdateStart, CassandraKeyspacesThroughputUpdateComplete

A propriedade *ResourceDetails* contém todo o corpo de recursos como uma carga útil de pedido e contém todas as propriedades solicitadas para atualizar

## <a name="next-steps"></a>Passos seguintes

* [Explore o Monitor Azure para o Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* [Monitor e depuração com métricas em Azure Cosmos DB](use-metrics.md)
