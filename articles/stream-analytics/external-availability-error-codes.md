---
title: Códigos de erro de disponibilidade externa - Azure Stream Analytics
description: Problemas De saca do Azure Stream Analytics problemas com códigos de erro de disponibilidade externa.
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 092b548e73330fe80ef10b2e25d9ab7883b256ac
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597794"
---
# <a name="azure-stream-analytics-external-availability-error-codes"></a>Códigos de erro de disponibilidade externa azure Stream Analytics

Pode utilizar registos de atividade e registos de recursos para ajudar a desimpedir comportamentos inesperados do seu trabalho no Azure Stream Analytics. Este artigo lista a descrição de cada código de erro de disponibilidade externa. Erros de disponibilidade externos ocorrem quando um serviço dependente está indisponível.

## <a name="externalserviceunavailable"></a>Serviços ExternosIndisponíveis

* **Causa**: Um serviço está temporariamente indisponível.
* **Recomendação**: Stream Analytics continuará a tentar chegar ao serviço. 

## <a name="kafkaservernotavailable"></a>KafkaServerNotDisponível

* **Causa**: O servidor Kafka não está disponível.

## <a name="eventhubmessagingerror"></a>EventHubMessagingError

* **Causa**: Stream Analytics encontrou erro ao comunicar com o EventHub. 


## <a name="next-steps"></a>Passos seguintes

* [Resolver problemas em ligações de entrada](stream-analytics-troubleshoot-input.md)
* [Saídas de Troubleshoot Azure Stream Analytics](stream-analytics-troubleshoot-output.md)
* [Consultas de Troubleshoot Azure Stream Analytics](stream-analytics-troubleshoot-query.md)
* [Troubleshoot Azure Stream Analytics usando registos de recursos](stream-analytics-job-diagnostic-logs.md)
* [Erros de dados do Azure Stream Analytics](data-errors.md)
