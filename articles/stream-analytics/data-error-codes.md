---
title: Códigos de erro de dados - Azure Stream Analytics
description: Resolução de problemas Problemas Azure Stream Analytics problemas com códigos de erro de dados.
ms.author: sidram
author: sidramadoss
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 33e617c8c3589d76d649dd2ea2236a4247b12500
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98016495"
---
# <a name="azure-stream-analytics-data-error-codes"></a>Códigos de erro de dados do Azure Stream Analytics

Pode utilizar registos de atividades e registos de recursos para ajudar a depurar comportamentos inesperados do seu trabalho Azure Stream Analytics. Este artigo lista a descrição de cada código de erro de erro de dados. Erros de dados ocorrem quando há dados maus no fluxo, como um esquema de registo inesperado.

## <a name="inputdeserializationerror"></a>InputDeserializationError

* **Causa:** Houve um erro ao deseritar os dados de entrada.

## <a name="inputeventtimestampnotfound"></a>InputEventTimestampNotFound

* **Causa**: Stream Analytics é incapaz de obter uma hora de tempo para obter o recurso. 

## <a name="inputeventtimestampbyovervaluenotfound"></a>InputEventTimestampByOverValueNotFound

* **Causa:** Stream Analytics é incapaz de obter valor de `TIMESTAMP BY OVER COLUMN` .

## <a name="inputeventlatebeyondthreshold"></a>InputEventLateBeyondThreshold

* **Causa:** Um evento de entrada foi enviado mais tarde do que a tolerância configurada.

## <a name="inputeventearlybeyondthreshold"></a>InputEventEarlyBeyondThreshold

* **Causa**: A hora de chegada do evento de entrada é mais cedo do que o limiar de tempo de tempo de aplicação do evento de entrada.

## <a name="azurefunctionmessagesizeexceeded"></a>AzureFunctionMessageSizeExceed

* **Causa**: A saída da mensagem para as funções Azure excede o limite de tamanho.

## <a name="eventhuboutputrecordexceedssizelimit"></a>EventHubOutputRecordExceedsSizeLimit

* **Causa**: Um registo de saída excede o limite máximo de tamanho ao escrever para o Event Hub.

## <a name="cosmosdboutputinvalidid"></a>CosmosDBOutputInvalidId

* **Causa**: O valor ou o tipo de coluna em particular é inválido.
* **Recomendação**: Forneça cordas únicas não vazias que não sejam superiores a 255 caracteres.

## <a name="cosmosdboutputinvalididcharacter"></a>CosmosDBOutputInvalidIdCharacter

* **Causa**: O ID do documento do registo de saída contém um carácter inválido.

## <a name="cosmosdboutputmissingid"></a>CosmosDBOutputMissingId

* **Causa**: O registo de saída não contém o id da \[ coluna] para usar como propriedade principal.

## <a name="cosmosdboutputmissingidcolumn"></a>CosmosDBOutputMissingIdColumn

* **Causa:** O registo de saída não contém a propriedade de Documento ID. 
* **Recomendação**: Certifique-se de que a saída de consulta contém a coluna com uma corda única não vazia inferior a '255'.

## <a name="cosmosdboutputmissingpartitionkey"></a>CosmosDBOutputMissingPartitionKey

* **Causa:** Falta o registo de saída da coluna para utilizar como propriedade chave de partição.

## <a name="cosmosdboutputsinglerecordtoolarge"></a>CosmosDBOutputSingleRecordTooLarge

* **Causa:** Um único disco escrito para Cosmos DB é muito grande.

## <a name="sqldatabaseoutputdataerror"></a>SQLDatabaseOutputDataError

* **Causa:** Stream Analytics não pode escrever eventos(s) para a Base de Dados SQL devido a problemas nos dados.

## <a name="next-steps"></a>Próximos passos

* [Resolver problemas em ligações de entrada](stream-analytics-troubleshoot-input.md)
* [Resolução de problemas Saídas Azure Stream Analytics](stream-analytics-troubleshoot-output.md)
* [Resolução de problemas Azure Stream Análises](stream-analytics-troubleshoot-query.md)
* [Resolução de problemas Azure Stream Analytics usando registos de recursos](stream-analytics-job-diagnostic-logs.md)
* [Erros de dados do Azure Stream Analytics](data-errors.md)
