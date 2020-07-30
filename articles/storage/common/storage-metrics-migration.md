---
title: Passe das métricas de Storage Analytics para as métricas do Monitor Azure Microsoft Docs
description: Saiba como fazer a transição das métricas storage Analytics (métricas clássicas) para métricas no Azure Monitor.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 07/28/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: a1f977cef614a52853407c0d0665399f1a249c53
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87422067"
---
# <a name="transition-to-metrics-in-azure-monitor"></a>Transição para métricas no Monitor Azure

O Azure Storage integra agora métricas na plataforma Azure Monitor. Este artigo ajuda-o a fazer a transição.

## <a name="steps-to-complete-the-transition"></a>Passos para completar a transição

Para a transição para métricas no Azure Monitor, recomendamos a seguinte abordagem.

1. Conheça algumas das [principais diferenças](#key-differences-between-classic-metrics-and-metrics-in-azure-monitor) entre métricas clássicas e métricas no Azure Monitor. 

2. Compilar uma lista de métricas clássicas que utiliza atualmente.

3. Identifique [quais métricas no Azure Monitor](#metrics-mapping-between-old-metrics-and-new-metrics) forneça os mesmos dados que as métricas que utiliza atualmente. 
   
4. Crie [gráficos](https://docs.microsoft.com/learn/modules/gather-metrics-blob-storage/2-viewing-blob-metrics-in-azure-portal) ou dashboards para visualizar [dados](https://docs.microsoft.com/learn/modules/gather-metrics-blob-storage/4-using-dashboards-in-the-azure-portal) métricos.

   > [!NOTE]
   > As métricas no Monitor Azure são ativadas por padrão, por isso não há nada que precise fazer para começar a capturar métricas. No entanto, deve criar gráficos ou dashboards para visualizar essas métricas. 
 
5. Se criou regras de alerta baseadas em métricas clássicas de armazenamento, então [crie regras](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview) de alerta baseadas em métricas no Azure Monitor. 

6. Depois de poder ver todas as suas métricas no Azure Monitor, pode desligar a exploração madeireira clássica. 

<a id="key-differences-between-classic-metrics-and-metrics-in-azure-monitor"></a>

## <a name="classic-metrics-vs-metrics-in-azure-monitor"></a>Métricas clássicas vs. métricas no Azure Monitor

Esta secção descreve algumas diferenças fundamentais entre estas duas plataformas métricas.

A principal diferença está na forma como as métricas são geridas. As métricas clássicas são geridas pelo Azure Storage, enquanto as métricas no Azure Monitor são geridas pelo Azure Monitor. Com métricas clássicas, o Azure Storage recolhe valores métricos, agrega-os e, em seguida, armazena-os em mesas que estão localizadas na conta de armazenamento. Com métricas no Azure Monitor, o Azure Storage envia dados métricos para a parte traseira do Azure Monitor. O Azure Monitor fornece uma experiência de monitorização unificada que inclui dados do portal Azure, bem como dados que são ingeridos. 

No que diz respeito ao suporte de métricas, as métricas clássicas fornecem métricas **de capacidade** apenas para o armazenamento de Azure Blob. As métricas no Azure Monitor fornecem métricas de capacidade para blob, mesa, arquivo, fila e armazenamento premium. As métricas clássicas fornecem métricas de **transação** em Blob, Table, Azure File e armazenamento de fila. As métricas no Azure Monitor adicionam armazenamento premium a essa lista.

Se a atividade na sua conta não desencadear uma métrica, as métricas clássicas mostrarão um valor de zero (0) para essa métrica. As métricas no Azure Monitor omitirão completamente os dados, o que leva a relatórios mais limpos. Por exemplo, com métricas clássicas, se não forem reportados erros de tempo do servidor, o `ServerTimeoutError` valor na tabela de métricas é definido para 0. O Azure Monitor não devolve quaisquer dados quando consulta o valor da métrica `Transactions` com dimensão `ResponseType` igual a `ServerTimeoutError` . 

Para saber mais sobre as métricas no Azure Monitor, consulte [métricas no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

<a id="metrics-mapping-between-old-metrics-and-new-metrics"></a>

## <a name="map-classic-metrics-to-metrics-in-azure-monitor"></a>Mapear métricas clássicas para métricas no Azure Monitor

 Utilize estas tabelas para identificar quais métricas no Azure Monitor fornecem os mesmos dados que as métricas que utiliza atualmente. 

**Métricas de capacidade**

| Métrica clássica | Métrica em Monitor Azure |
| ------------------- | ----------------- |
| `Capacity`            | `BlobCapacity`com a dimensão `BlobType` igual a `BlockBlob` ou`PageBlob` |
| `ObjectCount`        | `BlobCount`com a dimensão `BlobType` igual a `BlockBlob` ou`PageBlob` |
| `ContainerCount`      | `ContainerCount` |

> [!NOTE]
> Há também várias novas métricas de capacidade que não estavam disponíveis como métricas clássicas. Para ver a lista completa, consulte [métricas.](../common/monitor-storage-reference.md#metrics)

**Métricas de transação**

| Métrica clássica | Métrica em Monitor Azure |
| ------------------- | ----------------- |
| `AnonymousAuthorizationError` | Transações com dimensão `ResponseType` igual a `AuthorizationError` e dimensão iguais `Authentication` a`Anonymous` |
| `AnonymousClientOtherError` | Transações com dimensão `ResponseType` igual a `ClientOtherError` e dimensão iguais `Authentication` a`Anonymous` |
| `AnonymousClientTimeoutError` | Transações com dimensão `ResponseType` igual a `ClientTimeoutError` e dimensão iguais `Authentication` a`Anonymous` |
| `AnonymousNetworkError` | Transações com dimensão `ResponseType` igual a `NetworkError` e dimensão iguais `Authentication` a`Anonymous` |
| `AnonymousServerOtherError` | Transações com dimensão `ResponseType` igual a `ServerOtherError` e dimensão iguais `Authentication` a`Anonymous` |
| `AnonymousServerTimeoutError` | Transações com dimensão `ResponseType` igual a `ServerTimeoutError` e dimensão iguais `Authentication` a`Anonymous` |
| `AnonymousSuccess` | Transações com dimensão `ResponseType` igual a `Success` e dimensão iguais `Authentication` a`Anonymous` |
| `AnonymousThrottlingError` | Transações com dimensão `ResponseType` igual a `ClientThrottlingError` ou `ServerBusyError` dimensão iguais `Authentication` a`Anonymous` |
| `AuthorizationError` | Transações com dimensão `ResponseType` igual a`AuthorizationError` |
| `Availability` | `Availability` |
| `AverageE2ELatency` | `SuccessE2ELatency` |
| `AverageServerLatency` | `SuccessServerLatency` |
| `ClientOtherError` | Transações com dimensão `ResponseType` igual a`ClientOtherError` |
| `ClientTimeoutError` | Transações com dimensão `ResponseType` igual a`ClientTimeoutError` |
| `NetworkError` | Transações com dimensão `ResponseType` igual a`NetworkError` |
| `PercentAuthorizationError` | Transações com dimensão `ResponseType` igual a`AuthorizationError` |
| `PercentClientOtherError` | Transações com dimensão `ResponseType` igual a`ClientOtherError` |
| `PercentNetworkError` | Transações com dimensão `ResponseType` igual a`NetworkError` |
| `PercentServerOtherError` | Transações com dimensão `ResponseType` igual a`ServerOtherError` |
| `PercentSuccess` | Transações com dimensão `ResponseType` igual a`Success` |
| `PercentThrottlingError` | Transações com dimensão `ResponseType` igual a `ClientThrottlingError` ou`ServerBusyError` |
| `PercentTimeoutError` | Transações com dimensão `ResponseType` igual `ServerTimeoutError` ou igual `ResponseType` a`ClientTimeoutError` |
| `SASAuthorizationError` | Transações com dimensão `ResponseType` igual a `AuthorizationError` e dimensão iguais `Authentication` a`SAS` |
| `SASClientOtherError` | Transações com dimensão `ResponseType` igual a `ClientOtherError` e dimensão iguais `Authentication` a`SAS` |
| `SASClientTimeoutError` | Transações com dimensão `ResponseType` igual a `ClientTimeoutError` e dimensão iguais `Authentication` a`SAS` |
| `SASNetworkError` | Transações com dimensão `ResponseType` igual a `NetworkError` e dimensão iguais `Authentication` a`SAS` |
| `SASServerOtherError` | Transações com dimensão `ResponseType` igual a `ServerOtherError` e dimensão iguais `Authentication` a`SAS` |
| `SASServerTimeoutError` | Transações com dimensão `ResponseType` igual a `ServerTimeoutError` e dimensão iguais `Authentication` a`SAS` |
| `SASSuccess` | Transações com dimensão `ResponseType` igual a `Success` e dimensão iguais `Authentication` a`SAS` |
| `SASThrottlingError` | Transações com dimensão `ResponseType` igual a `ClientThrottlingError` ou `ServerBusyError` dimensão iguais `Authentication` a`SAS` |
| `ServerOtherError` | Transações com dimensão `ResponseType` igual a`ServerOtherError` |
| `ServerTimeoutError` | Transações com dimensão `ResponseType` igual a`ServerTimeoutError` |
| `Success` | Transações com dimensão `ResponseType` igual a`Success` |
| `ThrottlingError` | `Transactions`com a dimensão `ResponseType` igual a `ClientThrottlingError` ou`ServerBusyError`|
| `TotalBillableRequests` | `Transactions` |
| `TotalEgress` | `Egress` |
| `TotalIngress` | `Ingress` |
| `TotalRequests` | `Transactions` |

## <a name="next-steps"></a>Próximos passos

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Métricas de armazenamento no Monitor Azure](./storage-metrics-in-azure-monitor.md)
