---
title: Códigos de erro de disponibilidade externa - Azure Stream Analytics
description: Resolução de problemas Problemas Azure Stream Analytics problemas com códigos de erro de disponibilidade externa.
ms.author: sidram
author: sidramadoss
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 71625c4b81fc0795c376a89397e98659f4c72ff0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020558"
---
# <a name="azure-stream-analytics-external-availability-error-codes"></a>Códigos de erro de disponibilidade externa Azure Stream Analytics

Pode utilizar registos de atividades e registos de recursos para ajudar a depurar comportamentos inesperados do seu trabalho Azure Stream Analytics. Este artigo lista a descrição de cada código de erro de disponibilidade externa. Erros de disponibilidade externos ocorrem quando um serviço dependente não está disponível.

## <a name="externalserviceunavailable"></a>ExternalServiceUn disponível

* **Causa:** Um serviço está temporariamente indisponível.
* **Recomendação**: Stream Analytics continuará a tentar chegar ao serviço.

## <a name="eventhubmessagingerror"></a>EventHubMessagingError

* **Causa:** Stream Analytics encontrou erro ao comunicar com o EventHub. 


## <a name="next-steps"></a>Passos seguintes

* [Resolver problemas em ligações de entrada](stream-analytics-troubleshoot-input.md)
* [Resolução de problemas Saídas Azure Stream Analytics](stream-analytics-troubleshoot-output.md)
* [Resolução de problemas Azure Stream Análises](stream-analytics-troubleshoot-query.md)
* [Resolução de problemas Azure Stream Analytics usando registos de recursos](stream-analytics-job-diagnostic-logs.md)
* [Erros de dados do Azure Stream Analytics](data-errors.md)
