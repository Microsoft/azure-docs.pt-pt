---
title: Migração de métricas de armazenamento azure [ Microsoft Docs
description: Aprenda a migrar métricas antigas para novas métricas que são geridas pelo Azure Monitor.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/30/2018
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 537369c9466b1083723642ec9e93fcdf25056c5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68855332"
---
# <a name="azure-storage-metrics-migration"></a>Migração de métricas de armazenamento azure

Alinhado com a estratégia de unificar a experiência de monitor no Azure, o Azure Storage integra métricas à plataforma Azure Monitor. No futuro, o serviço das métricas antigas terminará com uma notificação antecipada baseada na política do Azure. Se você confiar em métricas de armazenamento antigas, você precisa migrar antes da data final do serviço para manter a sua informação métrica.

Este artigo mostra-lhe como migrar das métricas antigas para as novas métricas.

## <a name="understand-old-metrics-that-are-managed-by-azure-storage"></a>Compreender as métricas antigas geridas pelo Armazenamento do Microsoft Azure

O Azure Storage recolhe valores métricos antigos e agrega-os e armazena-os em tabelas $Metric dentro da mesma conta de armazenamento. Pode utilizar o portal Azure para configurar um gráfico de monitorização. Também pode utilizar os SDKs de Armazenamento Azure para ler os dados de $Metric tabelas baseadas no esquema. Para mais informações, consulte [Storage Analytics](./storage-analytics.md).

As métricas antigas fornecem métricas de capacidade apenas no armazenamento de Azure Blob. As métricas antigas fornecem métricas de transação no armazenamento de Blob, armazenamento de mesa, ficheiros Azure e armazenamento de fila.

Métricas antigas são projetadas em um esquema plano. O design resulta em valor métrico zero quando não se tem os padrões de tráfego que desencadeiam a métrica. Por exemplo, o valor **ServerTimeoutError** está definido para 0 em tabelas $Metric mesmo quando não recebe erros de tempo de servidor desde o tráfego ao vivo até uma conta de armazenamento.

## <a name="understand-new-metrics-managed-by-azure-monitor"></a>Compreender as novas métricas geridas pelo Azure Monitor

Para novas métricas de armazenamento, o Azure Storage emite os dados métricos para a extremidade traseira do Monitor Azure. O Azure Monitor fornece uma experiência de monitorização unificada, incluindo dados do portal, bem como a ingestão de dados. Para mais detalhes, pode consultar este [artigo.](../../monitoring-and-diagnostics/monitoring-overview-metrics.md)

As novas métricas fornecem métricas de capacidade e métricas de transação em Blob, Table, File, Queue e armazenamento premium.

Multidimensionais é uma das funcionalidades que o Azure Monitor fornece. O Azure Storage adota o design na definição de novos esquemas métricos. Para dimensões suportadas nas métricas, pode encontrar detalhes nas métricas de [Armazenamento Azure no Monitor Azure](./storage-metrics-in-azure-monitor.md). O design multidimensionado proporciona eficiência de custos tanto na largura de banda a partir da ingestão como da capacidade de armazenar métricas. Consequentemente, se o seu tráfego não tiver desencadeado métricas relacionadas, os dados métricos relacionados não serão gerados. Por exemplo, se o seu tráfego não tiver desencadeado erros de tempo limite do servidor, o Monitor Azure não devolve quaisquer dados quando consulta o valor das **Transações métricas** com **dimensionado ResponseType** igual ao **ServerTimeoutError**.

## <a name="metrics-mapping-between-old-metrics-and-new-metrics"></a>Mapeamento das métricas entre as métricas antigas e as novas

Se ler os dados métricos programáticamente, precisa adotar o novo esquema métrico nos seus programas. Para melhor compreender as alterações, pode consultar o mapeamento listado na tabela seguinte:

**Métricas de capacidade**

| Métrica antiga | Nova métrica |
| ------------------- | ----------------- |
| **Capacidade**            | **BlobCapacity** com a dimensão **BlobType** igual a **BlockBlob** ou **PageBlob** |
| **Contagem de Objetos**        | **BlobCount** com a dimensão **BlobType** igual a **BlockBlob** ou **PageBlob** |
| **Contagem de contentores**      | **Contagem de contentores** |

As seguintes métricas são novas ofertas que as métricas antigas não suportam:
* **Capacidade de Mesa**
* **Contagem de tabelas**
* **Contagem de tabelas**
* **Capacidade de fila**
* **Contagem de filas**
* **Contagem de mensagens de fila**
* **Capacidade de Arquivo**
* **Contagem de Ficheiros**
* **FileShareCount**
* **Capacidade Utilizada**

**Métricas de transação**

| Métrica antiga | Nova métrica |
| ------------------- | ----------------- |
| **Erro de Autorização Anónima** | Transações com a dimensão **ResponseType** igual a **AutorizaçãoErro** e **autenticação** de dimensão igual a **Anónimo** |
| **Erro de Cliente Anónimo** | Transações com a dimensão **ResponseType** igual ao **ClienteOtherError** e **dimensão Autenticação** igual a **Anónimo** |
| **Erro de TimeoutError anónimo do Cliente** | Transações com a dimensão **ResponseType** igual a **ClientTimeoutError** e **dimensionAuthenticação** igual a **Anónimo** |
| **Erro de Rede Anónimo** | Transações com a dimensão **ResponseType** igual a **NetworkError** e **dimensionAuthenticação** igual a **Anónimo** |
| **Erro de outro servidor anónimo** | Transações com a dimensão **ResponseType** igual ao **ServerOtherError** e **à autenticação** de dimensão igual a **Anónimo** |
| **Erro de tempo de saída do Servidor Anónimo** | Transações com a dimensão **ResponseType** igual a **ServerTimeoutError** e dimensionAuthenticação igual a **Anónimo** **Authentication** |
| **Sucesso Anónimo** | Transações com a dimensão **ResponseType** igual a **Sucesso** e **dimensão Autenticação** igual a **Anónimo** |
| **Erro De Throttling Anónimo** | Transações com a dimensão **ResponseType** igual a **ClientThrottlingError** ou **ServerBusyError** e **dimensionação De autenticação** igual a **Anónimo** |
| **Erro de Autorização** | Transações com a dimensão **ResponseType** igual a **AuthorizationError** |
| **Disponibilidade** | **Disponibilidade** |
| **AverageE2ELatency** | **SuccessE2ELatency** |
| **AverageServerLatency** | **SuccessServerLatency** |
| **Erro clientotherError** | Transações com a dimensão **ResponseType** igual ao **ClienteOtherError** |
| **Error Timeout do cliente** | Transações com a dimensão **ResponseType** igual a **ClientTimeoutError** |
| **Error de rede** | Transações com a dimensão **ResponseType** igual a **NetworkError** |
| **Erro de percentauthorization** | Transações com a dimensão **ResponseType** igual a **AuthorizationError** |
| **PercentclientOtherError** | Transações com a dimensão **ResponseType** igual ao **ClienteOtherError** |
| **Erro de Rede Por Percent** | Transações com a dimensão **ResponseType** igual a **NetworkError** |
| **PercentServerOtherError** | Transações com a dimensão **ResponseType** igual a **ServerOtherError** |
| **PercentSuccess** | Transações com a dimensão **ResponseType** igual ao **Sucesso** |
| **PercentThrottlingError** | Transações com a dimensão **ResponseType** igual a **ClientThrottlingError** ou **ServerBusyError** |
| **PercentTimeoutError** | Transações com a dimensão **ResponseType** igual a **ServerTimeoutError** ou **ResponseType** igual a **ClientTimeoutError** |
| **Erro de Autorização sas** | Transações com a dimensão **ResponseType** igual a **AutorizaçãoErro** e **autenticação** de dimensão igual a **SAS** |
| **Erro SASClientOtherError** | Transações com a dimensão **ResponseType** igual ao **ClienteOtherError** e dimensão **Autenticação** igual a **SAS** |
| **SASClientTimeoutError** | Transações com a dimensão **ResponseType** igual ao **ClienteTimeoutError** e dimensão **Autenticação** igual a **SAS** |
| **Erro sasNetwork** | Transações com a dimensão **ResponseType** igual a **NetworkError** e dimensionAuthenticação igual a **Authentication** **SAS** |
| **Erro SASServerOtherError** | Transações com a dimensão **ResponseType** igual ao **ServerOtherError** e **à autenticação** de dimensão igual a **SAS** |
| **SASServerTimeoutError** | Transações com a dimensão **ResponseType** igual ao **ServerTimeoutError** e **dimensão Autenticação** igual a **SAS** |
| **SASSuccess** | Transações com a dimensão **ResponseType** igual a **Sucesso** e dimensão **Autenticação** igual a **SAS** |
| **SASThrottlingError** | Transações com a dimensão **ResponseType** igual a **ClientThrottlingError** ou **ServerBusyError** e dimensão **Autenticação** igual a **SAS** |
| **Error serverOtherError** | Transações com a dimensão **ResponseType** igual a **ServerOtherError** |
| **Error de tempo de tempo de servidor** | Transações com a dimensão **ResponseType** igual a **ServerTimeoutError** |
| **Sucesso** | Transações com a dimensão **ResponseType** igual ao **Sucesso** |
| **ThrottlingError** | **Transações** com a dimensão **ResponseType** igual a **ClientThrottlingError** ou **ServerBusyError**|
| **TotalBillableRequests** | **Transações** |
| **TotalEgress** | **Saída** |
| **TotalIngress** | **Entrada** |
| **TotalRequests** | **Transações** |

## <a name="faq"></a>FAQ

### <a name="how-should-i-migrate-existing-alert-rules"></a>Como devo migrar as regras de alerta existentes?

Se criou regras clássicas de alerta baseadas em métricas de armazenamento antigas, precisa de criar novas regras de alerta com base no novo esquema métrico.

### <a name="is-new-metric-data-stored-in-the-same-storage-account-by-default"></a>Os novos dados métricos são armazenados na mesma conta de armazenamento por padrão?

Não. Para arquivar os dados métricos numa conta de armazenamento, utilize a API de Definição de Diagnóstico do [Monitor Azure](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate).

## <a name="next-steps"></a>Passos seguintes

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Métricas de armazenamento no Monitor Azure](./storage-metrics-in-azure-monitor.md)
