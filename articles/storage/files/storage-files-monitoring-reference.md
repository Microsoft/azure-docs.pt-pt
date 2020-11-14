---
title: Azure Files monitorizando a referência de dados Microsoft Docs
description: Referência de registo e métricas para monitorização de dados a partir de Ficheiros Azure.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/02/2020
ms.author: normesta
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: f6dc523050fdcdbf54a6b9864f0a752698a85eed
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94628939"
---
# <a name="azure-files-monitoring-data-reference"></a>Azure Files monitorizando a referência de dados

Consulte [os Ficheiros Azure de Monitorização](storage-files-monitoring.md) para obter informações sobre a recolha e análise de dados de monitorização de Ficheiros Azure.

## <a name="metrics"></a>Métricas

As tabelas seguintes listam as métricas da plataforma recolhidas para os Ficheiros Azure. 

### <a name="capacity-metrics"></a>Métricas de capacidade

Os valores das métricas de capacidade são atualizados diariamente (até 24 Horas). O grão de tempo define o intervalo de tempo para o qual são apresentados os valores das métricas. O grão de tempo suportado para todas as métricas de capacidade é de uma hora (PT1H).

A Azure Files fornece as seguintes métricas de capacidade no Azure Monitor.

#### <a name="account-level"></a>Nível de conta

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="azure-files"></a>Ficheiros do Azure

Esta tabela mostra [as métricas do Azure Files](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices).

| Métrica | Descrição |
| ------------------- | ----------------- |
| Capacidade de Ficheiros | A quantidade de armazenamento de ficheiros utilizado pela conta de armazenamento. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| Contagem de ficheiros   | O número de ficheiros na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| FileShareCapacityQuota | O limite superior da quantidade de armazenamento que pode ser usado pelo Azure Files Service em bytes. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024|
| FileShareCount | O número de ações de ficheiros na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| FileShareProvisionedIOPS | O número de IOPS provisido numa parte de ficheiro. Esta métrica aplica-se apenas ao armazenamento de ficheiros premium. <br/><br/> Unidade: bytes <br/> Tipo de agregação: Média |
| FileShareSnapshotCount | O número de instantâneos presentes na parte do serviço Azure Files da conta de armazenamento. <br/><br/> Unidade:Contagem <br/> Tipo de agregação: Média | 
|FileShareSnapshotSize|A quantidade de armazenamento utilizada pelas fotos no serviço Azure Files da conta de armazenamento. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média|

### <a name="transaction-metrics"></a>Métricas de transação

As métricas de transação são emitidas em cada pedido para uma conta de armazenamento de Azure Storage para Azure Monitor. No caso de não haver atividade na sua conta de armazenamento, não haverá dados sobre métricas de transação no período. Todas as métricas de transação estão disponíveis tanto a nível de serviço da conta como do serviço Azure Files. O grão de tempo define o intervalo de tempo que os valores métricos são apresentados. Os grãos de tempo suportados para todas as métricas de transação são PT1H e PT1M.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Dimensões das métricas

A Azure Files suporta as seguintes dimensões para métricas no Azure Monitor.

> [!NOTE] 
> A dimensão De Partilha de Ficheiros não está disponível para ações de ficheiros padrão (apenas ações de ficheiro premium). Ao utilizar ações de ficheiros padrão, as métricas fornecidas são para todas as ações de ficheiros na conta de armazenamento. Para obter métricas por partilha para ações de ficheiros padrão, crie uma ação de ficheiro por conta de armazenamento.

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

## <a name="see-also"></a>Ver também

- Consulte [os ficheiros Azure monitor de monitorização](storage-files-monitoring-reference.md) para uma descrição da monitorização do Armazenamento Azure.
- Consulte [os recursos de Monitor Azure com o Azure Monitor](../../azure-monitor/insights/monitor-azure-resource.md) para obter informações sobre a monitorização dos recursos do Azure.