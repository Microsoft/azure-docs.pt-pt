---
title: Como auditar operações de aviões de controlo da Azure Cosmos DB
description: Saiba como auditar as operações de planos de controlo, tais como adicionar uma região, atualização de entrada, falha da região, adicionar um VNet etc. em Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: sngun
ms.openlocfilehash: 64ad8e6b1101d8486268c857b3a7752e1801f52c
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420249"
---
# <a name="how-to-audit-azure-cosmos-db-control-plane-operations"></a>Como auditar operações de aviões de controlo da Azure Cosmos DB

As operações de controlo do avião incluem alterações na conta ou contentor da Azure Cosmos. Por exemplo, criar uma conta Azure Cosmos, adicionar uma região, atualizar o rendimento, falha da região, adicionar um VNet etc. são algumas das operações de controle do avião. Este artigo explica como auditar as operações de controlo de aviões em Azure Cosmos DB.

## <a name="disable-key-based-metadata-write-access"></a>Desativar o acesso de escrita de metadados baseados na chave
 
Antes de auditar as operações de controlo de aviões em Azure Cosmos DB, desative os metadados baseados na chave, a escrever acesso na sua conta. Quando o acesso de escrita de metadados baseados na chave é desativado, os clientes que ligam à conta Azure Cosmos através de chaves de conta são impedidos de aceder à conta. Você pode desativar `disableKeyBasedMetadataWriteAccess` o acesso de escrita, definindo a propriedade como verdadeira. Depois de definir esta propriedade, as alterações a qualquer recurso podem ocorrer a partir de um utilizador com a função e credenciais de controlo de acesso baseados em Funções adequadas(RBAC). Para saber mais sobre como definir esta propriedade, consulte as [alterações de Prevenção do artigo dos SDKs.](role-based-access-control.md#preventing-changes-from-cosmos-sdk)

 Considere os seguintes pontos ao desligar o acesso de escrita dos metadados:

* Avalie e certifique-se de que as suas aplicações não fazem chamadas de metadados que alteram os recursos acima referidos (por exemplo, criem recolha, atualização de entrada, ...) utilizando as teclas SDK ou conta.

* Atualmente, o portal Azure utiliza chaves de conta para operações de metadados e, por isso, estas operações serão bloqueadas. Em alternativa, utilize as implementações do modelo Azure CLI, SDKs ou Resource Manager para efetuar tais operações.

## <a name="enable-diagnostic-logs-for-control-plane-operations"></a>Ativar registos de diagnóstico para operações de controlo de aviões

Pode ativar registos de diagnóstico para operações de controlo do avião utilizando o portal Azure. Utilize os seguintes passos para permitir a exploração de madeira nas operações de planos de controlo:

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

## <a name="next-steps"></a>Passos seguintes

* [Explore o Monitor Azure para o Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* [Monitor e depuração com métricas em Azure Cosmos DB](use-metrics.md)