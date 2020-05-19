---
title: Códigos de erro de configuração - Azure Stream Analytics
description: Problemas De saca-se Em que o Azure Stream Analytics emite problemas com códigos de erro de configuração.
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 9951a6ddfbd1c562184e2b7e02a9da98f7d94b12
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598067"
---
# <a name="azure-stream-analytics-configuration-error-codes"></a>Códigos de erro de configuração Azure Stream Analytics

Pode utilizar registos de atividade e registos de recursos para ajudar a desimpedir comportamentos inesperados do seu trabalho no Azure Stream Analytics. Este artigo lista a descrição de cada código de erro de configuração. Os erros de configuração estão relacionados com a configuração do seu trabalho, ou configurações de entrada e saída.

## <a name="kafkainvalidpermission"></a>KafkaInvalidPer

* **Causa:** O trabalho da Stream Analytics não tem a permissão adequada para realizar certas ações.

## <a name="kafkainvalidconfiguration"></a>KafkaInvalidConfiguração

* **Causa**: Certas configurações no adaptador Kafka são inválidas.

## <a name="kafkainvalidtimestamptype"></a>KafkaInvalidTimeStampType

* **Causa**: O tipo de carimbo de tempo utilizado por Kafka é inválido.

## <a name="eventhubunauthorizedaccess"></a>EventHubUnauthorizedAccess

* **Causa:** O Centro de Eventos lançou um erro de *acesso não autorizado.*

## <a name="eventhubreceiverepochconflict"></a>EventHubReceiverEpochConflict

* **Causa**: Existe mais de um recetor Do Event Hub com diferentes valores de época.
* **Recomendação**: Certifique-se de que o *Service Bus Explorer* ou uma aplicação *EventProcessorHost* não estão ligados enquanto o seu trabalho de Stream Analytics estiver em execução.

## <a name="eventhubreceiverquotaexceeded"></a>EventHubReceiverQuotaExceeded

* **Causa**: Stream Analytics não pode ligar-se a uma divisória porque o número máximo de recetores permitidos por partição num grupo de consumidores foi atingido.
* **Recomendação**: Certifique-se de que outros trabalhos da Stream Analytics ou do Service Bus Explorer não estão a utilizar o mesmo grupo de consumidores.

## <a name="eventhuboutputthrottled"></a>EventHubOutputThrottled

* **Causa**: Ocorreu um erro ao escrever dados ao Event Hub devido à estrangulamento.
* **Recomendação**: Se isto acontecer de forma consistente, atualize a entrada.

## <a name="eventhuboutputinvalidconnectionconfig"></a>EventHubOutputInvalidConnectionConfig

* **Causa**: A configuração de ligação fornecida está incorreta.
* **Recomendação**: Corrija a configuração e reinicie o trabalho.

## <a name="eventhuboutputinvalidhostname"></a>Nome de hostname EventHubOutputInvalid

* **Causa:** O anfitrião do Centro de Eventos é inacessível.
* **Recomendação**: Certifique-se de que o nome do anfitrião fornecido está correto.

## <a name="eventhuboutputunexpectedpartitioncount"></a>Contagem de partições inesperadas eventhuboutput

* **Causa**: O remetente EventHub encontrou uma contagem inesperada de partição EventHub.
* **Recomendação**: Reinicie o seu trabalho de Stream Analytics se a contagem de partições do EventHub tiver mudado.

## <a name="cosmosdbpartitionkeynotfound"></a>CosmosDBPartitionKeyNotFound

* **Causa**: Stream Analytics não conseguiu encontrar a chave de partição de uma determinada coleção Cosmos DB na base de dados.
* **Recomendação**: Certifique-se de que existe uma chave de partição válida especificada para a recolha em Cosmos DB.

## <a name="cosmosdbinvalidpartitionkeycolumn"></a>CosmosDBInvalidPartitionKeyColumn

* **Causa**: Atirada quando uma chave de partição não é nem um nó de folha nem no nível superior.

## <a name="cosmosdbinvalididcolumn"></a>CosmosDBInvalidIdColumn

* **Causa**: A saída de consulta não pode conter o id da \[ coluna] se uma coluna diferente for escolhida como a principal propriedade-chave.

## <a name="cosmosdbdatabasenotfound"></a>CosmosDBDatabaseNotFound

* **Causa:** Stream Analytics não consegue encontrar uma base de dados CosmosDB.

## <a name="cosmosdbcollectionnotfound"></a>CosmosDBCollectionNotFound

* **Causa:** Stream Analytics não consegue encontrar uma coleção de DB cosmos em particular numa base de dados.

## <a name="cosmosdboutputwritethrottling"></a>CosmosDBOutputWriteThrottling

* **Causa**: Ocorreu um erro ao escrever dados devido à estrangulamento por Cosmos DB.
* **Recomendação**: Atualize o nível de desempenho da coleção e sintonize o desempenho da sua base de dados.

## <a name="sqldatabaseconnectionstringerror"></a>Erro de stringerror de conexão sqlDatabase

* **Causa**: O trabalho da Stream Analytics encontrou um erro de autenticação.
* **Recomendação**: Certifique-se de que a cadeia de ligação sql base de dados está correta.

## <a name="sqldatabasemanagedidentityauthenticationerror"></a>Erro de autenticação de identidade gerido por SQLDatabase

* **Causa**: O trabalho da Stream Analytics encontrou um erro de autenticação. 
* **Recomendação**: Certifique-se de que o nome da conta está configurado corretamente e que a Identidade Gerida do trabalho tem acesso à Base de Dados SQL.

## <a name="sqldatabaseoutputnotableerror"></a>Erro sqlDatabaseOutputnotable

* **Causa:** Stream Analytics não encontra a informação do esquema para uma determinada tabela.

## <a name="sqldwoutputinvalidserviceedition"></a>SQLDWOutputInvalidServiceEdition

* **Causa**: A Base de Dados SQL não é suportada.
* **Recomendação**: Utilize piscina SQL synapse.

## <a name="next-steps"></a>Passos seguintes

* [Resolver problemas em ligações de entrada](stream-analytics-troubleshoot-input.md)
* [Saídas de Troubleshoot Azure Stream Analytics](stream-analytics-troubleshoot-output.md)
* [Consultas de Troubleshoot Azure Stream Analytics](stream-analytics-troubleshoot-query.md)
* [Troubleshoot Azure Stream Analytics usando registos de recursos](stream-analytics-job-diagnostic-logs.md)
* [Erros de dados do Azure Stream Analytics](data-errors.md)
