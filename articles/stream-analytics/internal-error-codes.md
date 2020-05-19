---
title: Resolução de problemas com códigos de erro do Azure Stream Analytics
description: Problemas De saca-se em problemas com códigos de erro internos.
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: c8c792d548fc953cb5d059f322ad1f3139c91562
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597738"
---
# <a name="azure-stream-analytics-internal-error-codes"></a>Códigos de erro internos Azure Stream Analytics

Pode utilizar registos de atividade e registos de recursos para ajudar a desimpedir comportamentos inesperados do seu trabalho no Azure Stream Analytics. Este artigo lista a descrição de cada código de erro interno. Erros internos são erros genéricos que são lançados dentro da plataforma Stream Analytics quando o Stream Analytics não consegue distinguir se o erro é um erro de Disponibilidade Interna ou um bug no sistema.

## <a name="kafkainvalidrequest"></a>Pedido KafkaInvalid

* **Causa**: O pedido enviado ao servidor Kafka é inválido.

## <a name="kafkainputerror"></a>KafkaInputError

* **Causa:** A entrada de Kafka encontrou um problema.

## <a name="cosmosdboutputbatchsizetoolarge"></a>CosmosDBOutputBatchSizeTooLarge

* **Causa:** O tamanho do lote usado para escrever para Cosmos DB é muito grande. 
* **Recomendação**: Retentar com um tamanho de lote menor.

## <a name="next-steps"></a>Passos seguintes

* [Resolver problemas em ligações de entrada](stream-analytics-troubleshoot-input.md)
* [Saídas de Troubleshoot Azure Stream Analytics](stream-analytics-troubleshoot-output.md)
* [Consultas de Troubleshoot Azure Stream Analytics](stream-analytics-troubleshoot-query.md)
* [Troubleshoot Azure Stream Analytics usando registos de recursos](stream-analytics-job-diagnostic-logs.md)
* [Erros de dados do Azure Stream Analytics](data-errors.md)