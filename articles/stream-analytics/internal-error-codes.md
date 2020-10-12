---
title: Resolução de problemas com códigos de erro Azure Stream Analytics
description: Resolução de problemas Problemas Azure Stream Analytics problemas com códigos de erro internos.
ms.author: mamccrea
author: mamccrea
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 9dc3d873ddfef9a729f583cd914ca4700d562ff3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86045234"
---
# <a name="azure-stream-analytics-internal-error-codes"></a>Códigos de erro internos Azure Stream Analytics

Pode utilizar registos de atividades e registos de recursos para ajudar a depurar comportamentos inesperados do seu trabalho Azure Stream Analytics. Este artigo lista a descrição de cada código de erro interno. Erros internos são erros genéricos que são lançados dentro da plataforma Stream Analytics quando o Stream Analytics não consegue distinguir se o erro é um erro de Disponibilidade Interna ou um erro no sistema.

## <a name="cosmosdboutputbatchsizetoolarge"></a>CosmosDBOutputBatchSizeTooLarge

* **Causa:** O tamanho do lote usado para escrever para Cosmos DB é muito grande. 
* **Recomendação**: Reda parada com um tamanho de lote menor.

## <a name="next-steps"></a>Passos seguintes

* [Resolver problemas em ligações de entrada](stream-analytics-troubleshoot-input.md)
* [Resolução de problemas Saídas Azure Stream Analytics](stream-analytics-troubleshoot-output.md)
* [Resolução de problemas Azure Stream Análises](stream-analytics-troubleshoot-query.md)
* [Resolução de problemas Azure Stream Analytics usando registos de recursos](stream-analytics-job-diagnostic-logs.md)
* [Erros de dados do Azure Stream Analytics](data-errors.md)