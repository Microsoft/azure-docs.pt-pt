---
title: Códigos de erro de configuração - Azure Stream Analytics
description: Resolução de problemas Problemas Azure Stream Analytics problemas com códigos de erro de configuração.
ms.author: mamccrea
author: mamccrea
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 80179506c133de92b56d476c9aa99d55c3e3bbd9
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305795"
---
# <a name="azure-stream-analytics-configuration-error-codes"></a>Códigos de erro de configuração Azure Stream Analytics

Pode utilizar registos de atividades e registos de recursos para ajudar a depurar comportamentos inesperados do seu trabalho Azure Stream Analytics. Este artigo lista a descrição de cada código de erro de configuração. Os erros de configuração estão relacionados com a configuração do seu trabalho, ou configurações de entrada e saída.

## <a name="eventhubunauthorizedaccess"></a>EventHubUnautizedAccess

* **Causa:** O Event Hub lançou um erro *de acesso não autorizado.*

## <a name="eventhubreceiverepochconflict"></a>EventHubReceiverEpochConflict

* **Causa** : Existe mais de um recetor Event Hub com diferentes valores de época.
* **Recomendação** : Certifique-se de que o *Service Bus Explorer* ou uma aplicação *EventProcessorHost* não está ligada enquanto o seu trabalho de Stream Analytics estiver em execução.

## <a name="eventhubreceiverquotaexceeded"></a>EventHubReceiverQuotaExceed

* **Causa** : Stream Analytics não pode ligar-se a uma partição porque o número máximo de recetores permitidos por partição em um grupo de consumidores foi atingido.
* **Recomendação** : Certifique-se de que outros trabalhos da Stream Analytics ou do Service Bus Explorer não estão a utilizar o mesmo grupo de consumidores.

## <a name="eventhuboutputthrottled"></a>EventHubOutputThrottled

* **Causa** : Ocorreu um erro ao escrever dados para o Event Hub devido a estrangulamento.
* **Recomendação** : Se isto acontecer de forma consistente, atualize a produção.

## <a name="eventhuboutputinvalidconnectionconfig"></a>EventHubOutputInvalidConnectionConfig

* **Causa** : A configuração de ligação fornecida está incorreta.
* **Recomendação:** Corrija a configuração e reinicie o trabalho.

## <a name="eventhuboutputinvalidhostname"></a>EventHubOutputInvalidHostname

* **Causa** : O anfitrião do Event Hub é inacessível.
* **Recomendação** : Certifique-se de que o nome do hospedeiro fornecido está correto.

## <a name="eventhuboutputunexpectedpartitioncount"></a>EventHubOutputUnexpectedPartitionCount

* **Causa:** O remetente do EventHub encontrou uma contagem inesperada de partição do EventHub.
* **Recomendação** : Reinicie o seu trabalho stream Analytics se a contagem de divisórias do EventHub tiver mudado.

## <a name="cosmosdbpartitionkeynotfound"></a>CosmosDBPartitionKeyNotFound

* **Causa:** Stream Analytics não conseguiu encontrar a chave de partição de uma determinada coleção de Cosmos DB na base de dados.
* **Recomendação** : Certifique-se de que existe uma chave de partição válida especificada para a coleção em Cosmos DB.

## <a name="cosmosdbinvalidpartitionkeycolumn"></a>CosmosDBInvalidPartitionKeyColumn

* **Causa:** Lançado quando uma chave de partição não é nem um nó de folha nem no nível superior.

## <a name="cosmosdbinvalididcolumn"></a>CosmosDBInvalidIdColumn

* **Causa** : A saída de consulta não pode conter o id da \[ coluna] se uma coluna diferente for escolhida como propriedade principal.

## <a name="cosmosdbdatabasenotfound"></a>CosmosDBDatabaseNotFound

* **Causa:** Stream Analytics não consegue encontrar uma base de dados CosmosDB.

## <a name="cosmosdbcollectionnotfound"></a>CosmosDBCollectionNotFound

* **Causa:** Stream Analytics não consegue encontrar uma determinada coleção de Cosmos DB numa base de dados.

## <a name="cosmosdboutputwritethrottling"></a>CosmosDBOutputWriteThrottling

* **Causa** : Ocorreu um erro ao escrever dados devido ao estrangulamento por Cosmos DB.
* **Recomendação** : Atualize o nível de desempenho da recolha e afina o desempenho da sua base de dados.

## <a name="sqldatabaseconnectionstringerror"></a>SQLDatabaseConnectionStringError

* **Causa** : O trabalho stream Analytics encontrou um erro de autenticação.
* **Recomendação** : Certifique-se de que o fio de ligação SQL Database está correto.

## <a name="sqldatabasemanagedidentityauthenticationerror"></a>SQLDatabaseManagedIdentityAuthenticationError

* **Causa** : O trabalho stream Analytics encontrou um erro de autenticação. 
* **Recomendação** : Certifique-se de que o nome da conta está configurado corretamente e que a Identidade Gerida do trabalho tem acesso à Base de Dados SQL.

## <a name="sqldatabaseoutputnotableerror"></a>SQLDatabaseOutputNoTableError

* **Causa:** Stream Analytics não consegue encontrar a informação do esquema para uma determinada tabela.

## <a name="sqldwoutputinvalidserviceedition"></a>SQLDWOutputInvalidServiceEdition

* **Causa** : SqL Database não é suportado.
* **Recomendação** : Utilize piscina SQL dedicada.

## <a name="next-steps"></a>Passos seguintes

* [Resolver problemas em ligações de entrada](stream-analytics-troubleshoot-input.md)
* [Resolução de problemas Saídas Azure Stream Analytics](stream-analytics-troubleshoot-output.md)
* [Resolução de problemas Azure Stream Análises](stream-analytics-troubleshoot-query.md)
* [Resolução de problemas Azure Stream Analytics usando registos de recursos](stream-analytics-job-diagnostic-logs.md)
* [Erros de dados do Azure Stream Analytics](data-errors.md)
