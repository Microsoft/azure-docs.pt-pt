---
title: Migração de métricas de armazenamento do Azure | Documentos da Microsoft
description: Saiba como migrar métricas antigas para a nova métrica que é gerenciada pelo Azure Monitor.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 03/30/2018
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 58ac15c1aba715c9a5b67e723401b531e76608b2
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153601"
---
# <a name="azure-storage-metrics-migration"></a>Migração de métricas de armazenamento do Azure

Alinhado com a estratégia de unificar a experiência de monitorização no Azure, armazenamento do Azure integra-se o métricas para a plataforma do Azure Monitor. No futuro, o serviço das métricas de antigos vai terminar com uma notificação antecipada com base na política do Azure. Se contar com as métricas de armazenamento antigo, terá de migrar antes da data de fim de serviço para manter as informações da sua métrica.

Este artigo mostra como migrar de métricas de antigas para a nova métrica.

## <a name="understand-old-metrics-that-are-managed-by-azure-storage"></a>Compreender o antigas métricas que são geridas pelo armazenamento do Azure

O armazenamento do Azure recolhe os valores de métrica antigos e agrega e armazena-os nas tabelas de $Metric dentro da mesma conta de armazenamento. Pode utilizar o portal do Azure para configurar um gráfico de monitorização. Também pode utilizar os SDKs de armazenamento do Azure para ler os dados das tabelas de $Metric que baseiam-se no esquema. Para obter mais informações, consulte [a análise de armazenamento](./storage-analytics.md).

Métricas antigas fornecem métricas de capacidade apenas no armazenamento de Blobs do Azure. Métricas antigas fornecem métricas de transação no armazenamento de BLOBs, armazenamento de tabelas, ficheiros do Azure e o armazenamento de filas.

Métricas antigas destinam-se de um esquema simples. Os resultados de design em zero valor métrico quando não tem os padrões de tráfego acionar a métrica. Por exemplo, o **ServerTimeoutError** valor está definido como 0 nas tabelas de $Metric até mesmo quando não receber quaisquer erros de tempo limite do servidor do tráfego em direto para uma conta de armazenamento.

## <a name="understand-new-metrics-managed-by-azure-monitor"></a>Compreender as novas métricas geridas pelo Azure Monitor

Para novas métricas de armazenamento, o armazenamento do Azure emite os dados de métrica para o Azure Monitor de back-end. O Azure Monitor proporciona uma experiência unificada de monitorização, incluindo os dados a partir do portal, bem como a ingestão de dados. Para obter mais detalhes, pode consultar este [artigo](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Novas métricas fornecem métricas de capacidade e métricas de transação no Blob, tabela, ficheiro, fila e o armazenamento premium.

Multidimensional é um dos recursos que fornece o Azure Monitor. O armazenamento do Azure adota o design na definição de esquema de métrica de novo. Para obter dimensões suportadas nas métricas, pode encontrar detalhes [métricas de armazenamento do Azure no Azure Monitor](./storage-metrics-in-azure-monitor.md). Design de dimensão multi fornece a eficiência de custos em ambos os largura de banda de ingestão e a capacidade de armazenar as métricas. Conseqüentemente, se o tráfego não acionou métricas relacionadas, os dados de métrica relacionados não serão gerados. Por exemplo, se o tráfego não acionou quaisquer erros de tempo limite do servidor, Azure Monitor não devolve quaisquer dados ao consultar o valor de métrica **transações** com a dimensão **ResponseType** igual a **ServerTimeoutError**.

## <a name="metrics-mapping-between-old-metrics-and-new-metrics"></a>Mapeamento de métricas entre métricas antigas e nova métrica

Se ler programaticamente dados métricos, terá de adotar o novo esquema de métrico em seus programas. Para compreender melhor as alterações, pode consultar o mapeamento listado na tabela a seguir:

**Métricas de capacidade**

| Métrica antiga | Nova métrica |
| ------------------- | ----------------- |
| **Capacidade**            | **BlobCapacity** com a dimensão **BlobType** igual a **BlockBlob** ou **PageBlob** |
| **ObjectCount**        | **BlobCount** com a dimensão **BlobType** igual a **BlockBlob** ou **PageBlob** |
| **ContainerCount**      | **ContainerCount** |

As métricas seguintes são novas ofertas que não suportam as métricas antigas:
* **TableCapacity**
* **TableCount**
* **TableEntityCount**
* **QueueCapacity**
* **QueueCount**
* **QueueMessageCount**
* **FileCapacity**
* **FileCount**
* **FileShareCount**
* **UsedCapacity**

**Métricas de transação**

| Métrica antiga | Nova métrica |
| ------------------- | ----------------- |
| **AnonymousAuthorizationError** | Transações com a dimensão **ResponseType** igual a **AuthorizationError** e dimensão **autenticação** igual a **anónimo** |
| **AnonymousClientOtherError** | Transações com a dimensão **ResponseType** igual a **ClientOtherError** e dimensão **autenticação** igual a **anónimo** |
| **AnonymousClientTimeoutError** | Transações com a dimensão **ResponseType** igual a **ClientTimeoutError** e dimensão **autenticação** igual a **anónimo** |
| **AnonymousNetworkError** | Transações com a dimensão **ResponseType** igual a **NetworkError** e dimensão **autenticação** igual a **anónimo** |
| **AnonymousServerOtherError** | Transações com a dimensão **ResponseType** igual a **ServerOtherError** e dimensão **autenticação** igual a **anónimo** |
| **AnonymousServerTimeoutError** | Transações com a dimensão **ResponseType** igual a **ServerTimeoutError** e dimensão **autenticação** igual a **anónimo** |
| **AnonymousSuccess** | Transações com a dimensão **ResponseType** igual a **êxito** e dimensão **autenticação** igual a **anónimo** |
| **AnonymousThrottlingError** | Transações com a dimensão **ResponseType** igual a **ClientThrottlingError** ou **ServerBusyError** e dimensão **autenticação** igual a **anónimo** |
| **AuthorizationError** | Transações com a dimensão **ResponseType** igual a **AuthorizationError** |
| **Disponibilidade** | **Disponibilidade** |
| **Apresentam uma AverageE2ELatency** | **SuccessE2ELatency** |
| **AverageServerLatency** | **SuccessServerLatency** |
| **ClientOtherError** | Transações com a dimensão **ResponseType** igual a **ClientOtherError** |
| **ClientTimeoutError** | Transações com a dimensão **ResponseType** igual a **ClientTimeoutError** |
| **NetworkError** | Transações com a dimensão **ResponseType** igual a **NetworkError** |
| **PercentAuthorizationError** | Transações com a dimensão **ResponseType** igual a **AuthorizationError** |
| **PercentClientOtherError** | Transações com a dimensão **ResponseType** igual a **ClientOtherError** |
| **PercentNetworkError** | Transações com a dimensão **ResponseType** igual a **NetworkError** |
| **PercentServerOtherError** | Transações com a dimensão **ResponseType** igual a **ServerOtherError** |
| **PercentSuccess** | Transações com a dimensão **ResponseType** igual a **sucesso** |
| **PercentThrottlingError** | Transações com a dimensão **ResponseType** igual a **ClientThrottlingError** ou **ServerBusyError** |
| **PercentTimeoutError** | Transações com a dimensão **ResponseType** igual a **ServerTimeoutError** ou **ResponseType** igual a **ClientTimeoutError** |
| **SASAuthorizationError** | Transações com a dimensão **ResponseType** igual a **AuthorizationError** e dimensão **autenticação** igual a **SAS** |
| **SASClientOtherError** | Transações com a dimensão **ResponseType** igual a **ClientOtherError** e dimensão **autenticação** igual a **SAS** |
| **SASClientTimeoutError** | Transações com a dimensão **ResponseType** igual a **ClientTimeoutError** e dimensão **autenticação** igual a **SAS** |
| **SASNetworkError** | Transações com a dimensão **ResponseType** igual a **NetworkError** e dimensão **autenticação** igual a **SAS** |
| **SASServerOtherError** | Transações com a dimensão **ResponseType** igual a **ServerOtherError** e dimensão **autenticação** igual a **SAS** |
| **SASServerTimeoutError** | Transações com a dimensão **ResponseType** igual a **ServerTimeoutError** e dimensão **autenticação** igual a **SAS** |
| **SASSuccess** | Transações com a dimensão **ResponseType** igual a **êxito** e dimensão **autenticação** igual a **SAS** |
| **SASThrottlingError** | Transações com a dimensão **ResponseType** igual a **ClientThrottlingError** ou **ServerBusyError** e dimensão **autenticação** igual a **SAS** |
| **ServerOtherError** | Transações com a dimensão **ResponseType** igual a **ServerOtherError** |
| **ServerTimeoutError** | Transações com a dimensão **ResponseType** igual a **ServerTimeoutError** |
| **Êxito** | Transações com a dimensão **ResponseType** igual a **sucesso** |
| **ThrottlingError** | **Transações** com a dimensão **ResponseType** igual a **ClientThrottlingError** ou **ServerBusyError**|
| **TotalBillableRequests** | **Transações** |
| **TotalEgress** | **Saída** |
| **TotalIngress** | **Entrada** |
| **TotalRequests** | **Transações** |

## <a name="faq"></a>FAQ

### <a name="how-should-i-migrate-existing-alert-rules"></a>Como posso migrar a regras de alerta existentes?

Se tiver criado as regras de alerta clássicas com base em métricas de armazenamento antigo, terá de criar regras com base no novo esquema de métrica de novo alerta.

### <a name="is-new-metric-data-stored-in-the-same-storage-account-by-default"></a>São novos dados métricos armazenados na mesma conta de armazenamento por predefinição?

Não. Para arquivar os dados de métrica para uma conta de armazenamento, utilize o [API de definição de diagnóstico do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate).

## <a name="next-steps"></a>Passos Seguintes

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Métricas do armazenamento no Azure Monitor](./storage-metrics-in-azure-monitor.md)
