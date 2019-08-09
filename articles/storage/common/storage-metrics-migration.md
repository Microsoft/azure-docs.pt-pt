---
title: Migração de métricas do armazenamento do Azure | Microsoft Docs
description: Saiba como migrar métricas antigas para novas métricas que são gerenciadas pelo Azure Monitor.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/30/2018
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 537369c9466b1083723642ec9e93fcdf25056c5e
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855332"
---
# <a name="azure-storage-metrics-migration"></a>Migração de métricas do armazenamento do Azure

Alinhado com a estratégia de unificar a experiência de monitor no Azure, o armazenamento do Azure integra métricas à plataforma de Azure Monitor. No futuro, o serviço das métricas antigas terminará com uma notificação antecipada com base na política do Azure. Se você depender de métricas de armazenamento antigas, precisará migrar antes da data de término do serviço para manter suas informações de métrica.

Este artigo mostra como migrar das métricas antigas para as novas métricas.

## <a name="understand-old-metrics-that-are-managed-by-azure-storage"></a>Entender as métricas antigas que são gerenciadas pelo armazenamento do Azure

O armazenamento do Azure coleta valores de métrica antigos e agrega-os e os armazena em $Metric tabelas dentro da mesma conta de armazenamento. Você pode usar o portal do Azure para configurar um gráfico de monitoramento. Você também pode usar os SDKs de armazenamento do Azure para ler os dados de $Metric tabelas baseadas no esquema. Para obter mais informações, consulte [análise de armazenamento](./storage-analytics.md).

As métricas antigas fornecem métricas de capacidade somente no armazenamento de BLOBs do Azure. As métricas antigas fornecem métricas de transação no armazenamento de BLOBs, armazenamento de tabelas, arquivos do Azure e armazenamento de fila.

Métricas antigas são projetadas em um esquema simples. O design resulta em um valor de métrica zero quando você não tem os padrões de tráfego que disparam a métrica. Por exemplo, o valor **ServerTimeoutError** é definido como 0 em tabelas $Metric mesmo quando você não recebe erros de tempo limite do servidor do tráfego ao vivo para uma conta de armazenamento.

## <a name="understand-new-metrics-managed-by-azure-monitor"></a>Entender as novas métricas gerenciadas pelo Azure Monitor

Para novas métricas de armazenamento, o armazenamento do Azure emite os dados de métrica para o back-end de Azure Monitor. O Azure Monitor fornece uma experiência de monitoramento unificada, incluindo dados do portal, bem como ingestão de dados. Para obter mais detalhes, você pode consultar este [artigo](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

As novas métricas fornecem métricas de capacidade e métricas de transação em BLOB, tabela, arquivo, fila e armazenamento Premium.

Multi-Dimension é um dos recursos que o Azure Monitor fornece. O armazenamento do Azure adota o design para definir o novo esquema de métrica. Para dimensões com suporte em métricas, você pode encontrar detalhes em métricas de [armazenamento do Azure no Azure monitor](./storage-metrics-in-azure-monitor.md). O design de várias dimensões fornece a eficiência de custos na largura de banda da ingestão e da capacidade de armazenar métricas. Consequentemente, se o tráfego não tiver disparado métricas relacionadas, os dados de métrica relacionados não serão gerados. Por exemplo, se o tráfego não tiver disparado nenhum erro de tempo limite do servidor, Azure Monitor não retornará nenhum dado quando você consultar o valor de **Transações** de métrica com dimensão **ResponseType** igual a **ServerTimeoutError**.

## <a name="metrics-mapping-between-old-metrics-and-new-metrics"></a>Mapeamento de métricas entre métricas antigas e novas métricas

Se você ler dados de métrica programaticamente, será necessário adotar o novo esquema de métrica em seus programas. Para entender melhor as alterações, você pode consultar o mapeamento listado na tabela a seguir:

**Métricas de capacidade**

| Métrica antiga | Nova métrica |
| ------------------- | ----------------- |
| **Capacidade**            | **Capacidade** com a dimensão **BlobType** igual a **BlockBlob** ou **PageBlob** |
| **ObjectCount**        | **BlobCount** com a dimensão **BlobType** igual a **BlockBlob** ou **PageBlob** |
| **ContainerCount**      | **ContainerCount** |

As seguintes métricas são novas ofertas às quais as métricas antigas não oferecem suporte:
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
| **AnonymousAuthorizationError** | Transações com dimensão **ResponseType** igual a **AuthorizationError** e **autenticação** de dimensão igual a **anônimo** |
| **AnonymousClientOtherError** | Transações com dimensão **ResponseType** igual a **ClientOtherError** e **autenticação** de dimensão igual a **anônimo** |
| **AnonymousClientTimeoutError** | Transações com dimensão **ResponseType** igual a **ClientTimeoutError** e **autenticação** de dimensão igual a **anônimo** |
| **AnonymousNetworkError** | Transações com dimensão **ResponseType** igual a **NetworkError** e **autenticação** de dimensão igual a **anônimo** |
| **AnonymousServerOtherError** | Transações com dimensão **ResponseType** igual a **ServerOtherError** e **autenticação** de dimensão igual a **anônimo** |
| **AnonymousServerTimeoutError** | Transações com dimensão **ResponseType** igual a **ServerTimeoutError** e **autenticação** de dimensão igual a **anônimo** |
| **AnonymousSuccess** | Transações com dimensão **ResponseType** igual a **êxito** e **autenticação** de dimensão igual a **anônimo** |
| **AnonymousThrottlingError** | Transações com dimensão **ResponseType** igual a **ClientThrottlingError** ou **ServerBusyError** e **autenticação** de dimensão igual a **Anonymous** |
| **AuthorizationError** | Transações com dimensão **ResponseType** igual a **AuthorizationError** |
| **Disponibilidade** | **Disponibilidade** |
| **AverageE2ELatency** | **SuccessE2ELatency** |
| **AverageServerLatency** | **SuccessServerLatency** |
| **ClientOtherError** | Transações com dimensão **ResponseType** igual a **ClientOtherError** |
| **ClientTimeoutError** | Transações com dimensão **ResponseType** igual a **ClientTimeoutError** |
| **NetworkError** | Transações com dimensão **ResponseType** igual a **NetworkError** |
| **PercentAuthorizationError** | Transações com dimensão **ResponseType** igual a **AuthorizationError** |
| **PercentClientOtherError** | Transações com dimensão **ResponseType** igual a **ClientOtherError** |
| **PercentNetworkError** | Transações com dimensão **ResponseType** igual a **NetworkError** |
| **PercentServerOtherError** | Transações com dimensão **ResponseType** igual a **ServerOtherError** |
| **PercentSuccess** | Transações com dimensão **ResponseType** igual a **êxito** |
| **PercentThrottlingError** | Transações com dimensão **ResponseType** igual a **ClientThrottlingError** ou **ServerBusyError** |
| **PercentTimeoutError** | Transações com dimensão **ResponseType** igual a **ServerTimeoutError** ou **ResponseType** igual a **ClientTimeoutError** |
| **SASAuthorizationError** | Transações com dimensão **ResponseType** igual a **AuthorizationError** e **autenticação** de dimensão igual a **SAS** |
| **SASClientOtherError** | Transações com dimensão **ResponseType** igual a **ClientOtherError** e **autenticação** de dimensão igual a **SAS** |
| **SASClientTimeoutError** | Transações com dimensão **ResponseType** igual a **ClientTimeoutError** e **autenticação** de dimensão igual a **SAS** |
| **SASNetworkError** | Transações com dimensão **ResponseType** igual a **NetworkError** e **autenticação** de dimensão igual a **SAS** |
| **SASServerOtherError** | Transações com dimensão **ResponseType** igual a **ServerOtherError** e **autenticação** de dimensão igual a **SAS** |
| **SASServerTimeoutError** | Transações com dimensão **ResponseType** igual a **ServerTimeoutError** e **autenticação** de dimensão igual a **SAS** |
| **SASSuccess** | Transações com dimensão **ResponseType** igual a **êxito** e **autenticação** de dimensão igual a **SAS** |
| **SASThrottlingError** | Transações com dimensão **ResponseType** igual a **ClientThrottlingError** ou **ServerBusyError** e **autenticação** de dimensão igual a **SAS** |
| **ServerOtherError** | Transações com dimensão **ResponseType** igual a **ServerOtherError** |
| **ServerTimeoutError** | Transações com dimensão **ResponseType** igual a **ServerTimeoutError** |
| **Êxito** | Transações com dimensão **ResponseType** igual a **êxito** |
| **ThrottlingError** | **Transações** com dimensão **ResponseType** igual a **ClientThrottlingError** ou **ServerBusyError**|
| **TotalBillableRequests** | **Transações** |
| **TotalEgress** | **Saída** |
| **TotalIngress** | **Entrada** |
| **TotalRequests** | **Transações** |

## <a name="faq"></a>FAQ

### <a name="how-should-i-migrate-existing-alert-rules"></a>Como devo migrar as regras de alerta existentes?

Se você tiver criado regras de alerta clássicas com base em métricas de armazenamento antigas, será necessário criar novas regras de alerta com base no novo esquema de métrica.

### <a name="is-new-metric-data-stored-in-the-same-storage-account-by-default"></a>Os novos dados de métrica são armazenados na mesma conta de armazenamento por padrão?

Não. Para arquivar os dados de métrica em uma conta de armazenamento, use a [API de configuração de diagnóstico Azure monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate).

## <a name="next-steps"></a>Passos Seguintes

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Métricas de armazenamento no Azure Monitor](./storage-metrics-in-azure-monitor.md)
