---
title: Azure Storage métricas migração / Microsoft Docs
description: Saiba como migrar métricas antigas para novas métricas que são geridas pelo Azure Monitor.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/30/2018
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 10768ca4c6fbe4afc322fa9a7045c7cc4fe6f175
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83681314"
---
# <a name="azure-storage-metrics-migration"></a>Migração de métricas de armazenamento Azure

Alinhado com a estratégia de unificar a experiência do monitor no Azure, o Azure Storage integra métricas na plataforma Azure Monitor. No futuro, o serviço das métricas antigas terminará com uma notificação antecipada baseada na Política Azure. Se depender de métricas de armazenamento antigas, tem de migrar antes da data de final de serviço para manter as suas informações métricas.

Este artigo mostra como migrar das métricas antigas para as novas métricas.

## <a name="understand-old-metrics-that-are-managed-by-azure-storage"></a>Compreender as métricas antigas geridas pelo Armazenamento do Microsoft Azure

O Azure Storage recolhe valores métricos antigos, agrega-os e armazena-os em $Metric tabelas dentro da mesma conta de armazenamento. Pode utilizar o portal Azure para configurar um gráfico de monitorização. Também pode utilizar os SDKs de armazenamento Azure para ler os dados de $Metric tabelas que são baseadas no esquema. Para mais informações, consulte [Storage Analytics](./storage-analytics.md).

As métricas antigas fornecem métricas de capacidade apenas no armazenamento da Azure Blob. As métricas antigas fornecem métricas de transação no armazenamento de Blob, armazenamento de mesa, ficheiros Azure e armazenamento de fila.

As métricas antigas são desenhadas num esquema plano. O design resulta em valor métrico zero quando não se tem os padrões de tráfego desencadeando a métrica. Por exemplo, o valor **Do ServerTimeoutError** está definido para 0 em $Metric tabelas mesmo quando não recebe erros de tempo do servidor do tráfego ao vivo para uma conta de armazenamento.

## <a name="understand-new-metrics-managed-by-azure-monitor"></a>Compreender as novas métricas geridas pelo Azure Monitor

Para novas métricas de armazenamento, o Azure Storage emite os dados métricos para a extremidade traseira do Azure Monitor. O Azure Monitor fornece uma experiência de monitorização unificada, incluindo dados do portal, bem como a ingestão de dados. Para mais detalhes, pode consultar este [artigo.](../../monitoring-and-diagnostics/monitoring-overview-metrics.md)

As novas métricas fornecem métricas de capacidade e métricas de transação em Blob, Table, File, Queue e armazenamento premium.

Multi-dimensão é uma das funcionalidades que o Azure Monitor fornece. O Azure Storage adota o design na definição de novo esquema métrico. Para dimensões suportadas em métricas, pode encontrar detalhes em [métricas de armazenamento Azure no Azure Monitor](./storage-metrics-in-azure-monitor.md). O design multidimensional proporciona eficiência de custos tanto na largura de banda como na capacidade de armazenamento de métricas. Consequentemente, se o seu tráfego não tiver desencadeado métricas relacionadas, os dados métricos relacionados não serão gerados. Por exemplo, se o seu tráfego não tiver desencadeado quaisquer erros de tempo limite do servidor, o Azure Monitor não devolve quaisquer dados quando consulta o valor das **Transações métricas** com o Dimension **ResponseType** igual ao **ServerTimeoutError**.

## <a name="metrics-mapping-between-old-metrics-and-new-metrics"></a>Mapeamento das métricas entre as métricas antigas e as novas

Se ler dados métricos programáticamente, tem de adotar o novo esquema métrico nos seus programas. Para melhor compreender as alterações, pode consultar o mapeamento listado na tabela seguinte:

**Métricas de capacidade**

| Métrica antiga | Nova métrica |
| ------------------- | ----------------- |
| **Capacidade**            | **BlobCapacity** com a dimensão **BlobType** igual a **BlockBlob** ou **PageBlob** |
| **Conta de Objetos**        | **BlobCount** com a dimensão **BlobType** igual a **BlockBlob** ou **PageBlob** |
| **Contagem de contentores**      | **Contagem de contentores** |

As seguintes métricas são novas ofertas que as métricas antigas não suportam:
* **Capacidade de Mesa**
* **MesaCount**
* **TabelaseconagemCount**
* **Capacidade de Fila**
* **Contagem de filas**
* **QueueMessageCount**
* **Capacidade de Ficheiros**
* **Contagem de ficheiros**
* **FileShareCount**
* **Capacidade Usada**

**Métricas de transação**

| Métrica antiga | Nova métrica |
| ------------------- | ----------------- |
| **AnónimoAuthorizationError** | Transações com a dimensão **ResponseType** igual a **AutorizaçãoError** e **autenticação de** dimensão igual a **Anónimo** |
| **AnónimoClientOtherError** | Transações com a dimensão **ResponseType** igual ao **ClientOtherError** e **autenticação de** dimensão igual a **Anónimo** |
| **AnónimoClientTimeoutError** | Transações com a dimensão **ResponseType** igual ao **ClientTimeoutError** e **autenticação de** dimensão igual a **Anónimo** |
| **AnónimoNetworkError** | Transações com a dimensão **ResponseType** igual a **NetworkError** e **autenticação de** dimensão igual a **Anónimo** |
| **AnónimoServerOtherError** | Transações com a dimensão **ResponseType** igual a **ServerOtherError** e **autenticação de** dimensão igual a **Anónimo** |
| **AnónimoServerTimeoutError** | Transações com a dimensão **ResponseType** igual ao **ServerTimeoutError** e **autenticação de** dimensão igual a **Anónimo** |
| **AnónimoSuccess** | Transações com a dimensão **ResponseType** igual ao **Sucesso** e dimensão **Autenticação** igual a **Anónimo** |
| **Anônimos** | Transações com a dimensão **ResponseType** igual a **ClientThrottlingError** ou **ServerBusyError** e **autenticação de** dimensão igual a **Anónimo** |
| **AutorizaçãoError** | Transações com a dimensão **ResponseType** igual a **AuthorizationError** |
| **Disponibilidade** | **Disponibilidade** |
| **AverageE2ELatency** | **SucessoE2ELatency** |
| **AverageServerLatency** | **SucessoServerLatency** |
| **ClientOtherError** | Transações com a dimensão **ResponseType** igual a **ClientOtherError** |
| **ClientTimeoutError** | Transações com a dimensão **ResponseType** igual ao **ClientTimeoutError** |
| **NetworkError** | Transações com a dimensão **ResponseType** igual a **NetworkError** |
| **PercentAuthorizationError** | Transações com a dimensão **ResponseType** igual a **AuthorizationError** |
| **PercentClientOtherError** | Transações com a dimensão **ResponseType** igual a **ClientOtherError** |
| **PercentNetworkError** | Transações com a dimensão **ResponseType** igual a **NetworkError** |
| **PercentServerOtherError** | Transações com a dimensão **ResponseType** igual a **ServerOtherError** |
| **PercentSuccess** | Transações com a dimensão **ResponseType** igual ao **Sucesso** |
| **PercentThrottlingError** | Transações com a dimensão **ResponseType** igual a **ClientThrottlingError** ou **ServerBusyError** |
| **PercentTimeoutError** | Transações com a dimensão **ResponseType** igual a **ServerTimeoutError** ou **ResponseType** igual a **ClientTimeoutError** |
| **SASAuthorizationError** | Transações com a dimensão **ResponseType** igual a **AutorizaçãoError** e **autenticação de** dimensão igual a **SAS** |
| **SASClientOtherError** | Transações com a dimensão **ResponseType** igual ao **ClientOtherError** e **autenticação de** dimensão igual a **SAS** |
| **SASClientTimeoutError** | Transações com a dimensão **ResponseType** igual ao **ClientTimeoutError** e **autenticação de** dimensão igual a **SAS** |
| **SASNetworkError** | Transações com a dimensão **ResponseType** igual a **NetworkError** e **autenticação de** dimensão igual a **SAS** |
| **SASServerOtherError** | Transações com a dimensão **ResponseType** igual ao **ServerOtherError** e **autenticação de** dimensão igual a **SAS** |
| **SASServerTimeoutError** | Transações com a dimensão **ResponseType** igual ao **ServerTimeoutError** e **autenticação de** dimensão igual a **SAS** |
| **SASSuccess** | Transações com a dimensão **ResponseType** igual ao **Sucesso** e dimensão **Autenticação** igual a **SAS** |
| **SASThrottlingError** | Transações com a dimensão **ResponseType** igual a **ClientThrottlingError** ou **ServerBusyError** e **autenticação de** dimensão igual a **SAS** |
| **ServerOtherError** | Transações com a dimensão **ResponseType** igual a **ServerOtherError** |
| **ServerTimeoutError** | Transações com a dimensão **ResponseType** igual a **ServerTimeoutError** |
| **Sucesso** | Transações com a dimensão **ResponseType** igual ao **Sucesso** |
| **ThrottlingError** | **Transações** com a dimensão **ResponseType** igual a **ClientThrottlingError** ou **ServerBusyError**|
| **TotalBillableRequests** | **Transações** |
| **TotalEgress** | **Saída** |
| **TotalIngress** | **Entrada** |
| **TotalRequests** | **Transações** |

## <a name="faq"></a>FAQ

### <a name="how-should-i-migrate-existing-alert-rules"></a>Como devo migrar as regras de alerta existentes?

Se criou regras clássicas de alerta baseadas em métricas de armazenamento antigas, precisa de criar novas regras de alerta com base no novo esquema métrico.

### <a name="is-new-metric-data-stored-in-the-same-storage-account-by-default"></a>Os novos dados métricos são armazenados na mesma conta de armazenamento por defeito?

Não. Para arquivar os dados métricos numa conta de armazenamento, utilize a [API de Definição de Diagnóstico do Monitor Azure](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate).

## <a name="next-steps"></a>Próximos passos

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Métricas de armazenamento no Monitor Azure](./storage-metrics-in-azure-monitor.md)
