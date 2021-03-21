---
title: Referência de dados de monitorização de armazenamento de fila Azure
description: Referência de registo e métricas para monitorização de dados do Azure Queue Storage.
author: normesta
services: azure-monitor
ms.author: normesta
ms.date: 10/02/2020
ms.topic: reference
ms.service: azure-monitor
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: 95f20737b044140fe12ea939e71cd2397cb4826d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100576693"
---
# <a name="azure-queue-storage-monitoring-data-reference"></a>Referência de dados de monitorização de armazenamento de fila Azure

Consulte [o Monitor Azure Storage](monitor-queue-storage.md) para obter informações sobre a recolha e análise de dados de monitorização para o Armazenamento Azure.

## <a name="metrics"></a>Métricas

As tabelas a seguir listam as métricas da plataforma recolhidas para o Azure Storage.

### <a name="capacity-metrics"></a>Métricas de capacidade

Os valores das métricas de capacidade são atualizados diariamente (até 24 horas). O grão de tempo define o intervalo de tempo para o qual são apresentados os valores das métricas. O grão de tempo suportado para todas as métricas de capacidade é de uma hora (PT1H).

O Azure Storage fornece as seguintes métricas de capacidade no Monitor Azure.

#### <a name="account-level-capacity-metrics"></a>Métricas de capacidade de nível de conta

[!INCLUDE [Account-level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="queue-storage-metrics"></a>Métricas de armazenamento de fila

Esta tabela mostra [métricas de armazenamento de fila.](../../azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccountsqueueservices)

| Metric | Descrição |
| ------------------- | ----------------- |
| **Capacidade de Fila** | A quantidade de Armazenamento de Fila utilizado pela conta de armazenamento. <br><br> Unidade: `Bytes` <br> Tipo de agregação: `Average` <br> Exemplo de valor: `1024` |
| **Contagem de filas** | O número de filas na conta de armazenamento. <br><br> Unidade: `Count` <br> Tipo de agregação: `Average` <br> Exemplo de valor: `1024` |
| **QueueMessageCount** | O número aproximado de mensagens de fila na conta de armazenamento. <br><br> Unidade: `Count` <br> Tipo de agregação: `Average` <br> Exemplo de valor: `1024` |

### <a name="transaction-metrics"></a>Métricas de transação

As métricas de transação são emitidas em cada pedido para uma conta de armazenamento de Azure Storage para Azure Monitor. No caso de não haver atividade na sua conta de armazenamento, não haverá dados sobre métricas de transação no período. Todas as métricas de transação estão disponíveis tanto a nível de serviço de conta como de armazenamento de fila. O grão de tempo define o intervalo de tempo que os valores métricos são apresentados. Os grãos de tempo suportados para todas as métricas de transação são PT1H e PT1M.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Dimensões das métricas

O Azure Storage suporta as seguintes dimensões para métricas no Azure Monitor.

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

## <a name="resource-logs-preview"></a>Registos de recursos (pré-visualização)

> [!NOTE]
> Os registos de armazenamento Azure no Azure Monitor estão em pré-visualização pública e estão disponíveis para testes de pré-visualização em todas as regiões de nuvem pública. Esta pré-visualização permite registos para blobs (incluindo Azure Data Lake Storage Gen2), ficheiros, filas, tabelas, contas de armazenamento premium em v1 de uso geral e contas de armazenamento v2 de uso geral. As contas de armazenamento clássicas não são suportadas.

A tabela que se segue lista as propriedades dos registos de recursos de armazenamento Azure quando são recolhidas em Registos monitores Azure ou Azure Storage. As propriedades descrevem a operação, o serviço e o tipo de autorização que foi usada para realizar a operação.

### <a name="fields-that-describe-the-operation"></a>Campos que descrevem a operação

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>Campos que descrevem como a operação foi autenticada

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

### <a name="fields-that-describe-the-service"></a>Campos que descrevem o serviço

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## <a name="see-also"></a>Ver também

- Consulte [o armazenamento da fila Azure](monitor-queue-storage.md) monitor para uma descrição da monitorização do armazenamento da fila Azure.
- Consulte [os recursos de Monitor Azure com o Azure Monitor](../../azure-monitor/essentials/monitor-azure-resource.md) para obter informações sobre a monitorização dos recursos do Azure.
