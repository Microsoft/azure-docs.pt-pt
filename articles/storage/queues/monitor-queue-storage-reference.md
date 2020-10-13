---
title: Referência de dados de monitorização de armazenamento da fila Azure / Microsoft Docs
description: Referência de registo e métricas para monitorização de dados do armazenamento da Fila Azure.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/02/2020
ms.author: normesta
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: 52ee08e0cce23aebbac1564a5c3ed894d0e487d7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91711415"
---
# <a name="azure-queue-storage-monitoring-data-reference"></a>Referência de dados de monitorização de armazenamento de fila Azure

Consulte [o Monitor Azure Storage](monitor-queue-storage.md) para obter informações sobre a recolha e análise de dados de monitorização para o Armazenamento Azure.

## <a name="metrics"></a>Métricas

As tabelas a seguir listam as métricas da plataforma recolhidas para o Azure Storage. 

### <a name="capacity-metrics"></a>Métricas de capacidade

Os valores das métricas de capacidade são enviados para o Azure Monitor a cada hora. Os valores são atualizados diariamente. O grão de tempo define o intervalo de tempo para o qual são apresentados os valores das métricas. O grão de tempo suportado para todas as métricas de capacidade é de uma hora (PT1H).

O Azure Storage fornece as seguintes métricas de capacidade no Monitor Azure.

#### <a name="account-level"></a>Nível de conta

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="queue-storage"></a>Armazenamento de filas

Esta tabela mostra [métricas de armazenamento de fila.](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccountsqueueservices)

| Métrica | Descrição |
| ------------------- | ----------------- |
| Capacidade de Fila | A quantidade de armazenamento de fila utilizada pela conta de armazenamento. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| Contagem de filas   | O número de filas na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| QueueMessageCount | O número aproximado de mensagens de fila no serviço de fila da conta de armazenamento. <br/><br/>Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |

### <a name="transaction-metrics"></a>Métricas de transação

As métricas de transação são emitidas em cada pedido para uma conta de armazenamento de Azure Storage para Azure Monitor. No caso de não haver atividade na sua conta de armazenamento, não haverá dados sobre métricas de transação no período. Todas as métricas de transação estão disponíveis tanto a nível de serviço de armazenamento de conta como de fila. O grão de tempo define o intervalo de tempo que os valores métricos são apresentados. Os grãos de tempo suportados para todas as métricas de transação são PT1H e PT1M.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Dimensões das métricas

O Azure Storage suporta as seguintes dimensões para métricas no Azure Monitor.

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

## <a name="resource-logs-preview"></a>Registos de recursos (pré-visualização)

> [!NOTE]
> Os registos de armazenamento Azure no Azure Monitor estão em pré-visualização pública e estão disponíveis para testes de pré-visualização em todas as regiões de nuvem pública. Para se inscrever na pré-visualização, consulte [esta página](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).  Esta pré-visualização permite registos para blobs (incluindo Azure Data Lake Storage Gen2), ficheiros, filas, tabelas, contas de armazenamento premium em v1 de uso geral e contas de armazenamento v2 de uso geral. As contas de armazenamento clássicas não são suportadas.

A tabela que se segue lista as propriedades dos registos de recursos de armazenamento Azure quando são recolhidas em Registos monitores Azure ou Azure Storage. As propriedades descrevem a operação, o serviço e o tipo de autorização que foi usada para realizar a operação.

### <a name="fields-that-describe-the-operation"></a>Campos que descrevem a operação

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>Campos que descrevem como a operação foi autenticada

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

### <a name="fields-that-describe-the-service"></a>Campos que descrevem o serviço

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## <a name="see-also"></a>Consulte também

- Consulte [o armazenamento da fila Azure](monitor-queue-storage.md) monitor para uma descrição da monitorização do armazenamento do Azure.
- Consulte [os recursos de Monitor Azure com o Azure Monitor](../../azure-monitor/insights/monitor-azure-resource.md) para obter informações sobre a monitorização dos recursos do Azure.