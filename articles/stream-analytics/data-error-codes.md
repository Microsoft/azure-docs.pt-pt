---
title: Códigos de erro de dados - Azure Stream Analytics
description: Problemas De sacado Azure Stream Analytics emite problemas com códigos de erro de dados.
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: f7383a56a11ac9b567c80e73cc84944174c30ac8
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597017"
---
# <a name="azure-stream-analytics-configuration-error-codes"></a>Códigos de erro de configuração Azure Stream Analytics

Pode utilizar registos de atividade e registos de recursos para ajudar a desimpedir comportamentos inesperados do seu trabalho no Azure Stream Analytics. Este artigo lista a descrição de cada código de erro de dados. Erros de dados ocorrem quando há dados maus no fluxo, como um esquema de registo inesperado.

## <a name="inputdeserializationerror"></a>InputDeserializationError

* **Causa:** Houve um erro ao desserializar os dados de entrada.

## <a name="inputeventtimestampnotfound"></a>InputEventTimestampNotFound

* **Causa:** Stream Analytics é incapaz de obter uma marca de tempo para o recurso. 

## <a name="inputeventtimestampbyovervaluenotfound"></a>InputEventTimestampByOverValueNotFound

* **Causa:** Stream Analytics é incapaz de obter valor de `TIMESTAMP BY OVER COLUMN` .

## <a name="inputeventlatebeyondthreshold"></a>InputEventLateBeyondThreshold

* **Causa**: Um evento de entrada foi enviado mais tarde do que a tolerância configurada.

## <a name="inputeventearlybeyondthreshold"></a>InputEventEarlyBeyondThreshold

* **Causa**: A hora de chegada do evento de entrada é mais cedo do que o limiar de carimbo de tempo de aplicação do evento de entrada.

## <a name="azurefunctionmessagesizeexceeded"></a>Tamanho sinuoso do AzureFunctionMessageExceeded

* **Causa**: A saída da mensagem para funções Azure excede o limite de tamanho.

## <a name="eventhuboutputrecordexceedssizelimit"></a>EventHubOutputRecordExceedsSizeLimit

* **Causa**: Um registo de saída excede o limite máximo de tamanho ao escrever para o Event Hub.

## <a name="cosmosdboutputinvalidid"></a>CosmosDBOutputInvalidId

* **Causa**: O valor ou o tipo de uma coluna específica é inválido.
* **Recomendação**: Forneça cordas não vazias únicas que não sejam superiores a 255 caracteres.

## <a name="cosmosdboutputinvalididcharacter"></a>CosmosDBOutputInvalidIdCharacter

* **Causa**: O ID documento do registo de saída contém um carácter inválido.

## <a name="cosmosdboutputmissingid"></a>CosmosDBOutputMissingId

* **Causa**: O registo de saída não contém o id da \[ coluna] para usar como a principal propriedade chave.

## <a name="cosmosdboutputmissingidcolumn"></a>CosmosDBOutputMissingIdColumn

* **Causa:** O registo de saída não contém a propriedade documental. 
* **Recomendação**: Certifique-se de que a saída de consulta contém a coluna com uma corda não vazia única inferior a caracteres de 255.

## <a name="cosmosdboutputmissingpartitionkey"></a>CosmosDBOutputFaltapartpartitionKey

* **Causa**: O registo de saída está a faltar uma coluna para utilizar como propriedade chave de partição.

## <a name="cosmosdboutputsinglerecordtoolarge"></a>CosmosDBOutputSingleRecordTooLarge

* **Causa:** Um único disco escrito para Cosmos DB é muito grande.

## <a name="sqldatabaseoutputdataerror"></a>Erro de dados de saída de base de dados sqlDatabase

* **Causa**: Stream Analytics não pode escrever eventos na Base de Dados SQL devido a problemas nos dados.

## <a name="next-steps"></a>Passos seguintes

* [Resolver problemas em ligações de entrada](stream-analytics-troubleshoot-input.md)
* [Saídas de Troubleshoot Azure Stream Analytics](stream-analytics-troubleshoot-output.md)
* [Consultas de Troubleshoot Azure Stream Analytics](stream-analytics-troubleshoot-query.md)
* [Troubleshoot Azure Stream Analytics usando registos de recursos](stream-analytics-job-diagnostic-logs.md)
* [Erros de dados do Azure Stream Analytics](data-errors.md)
