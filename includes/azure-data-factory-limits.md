---
title: incluir ficheiro
description: incluir ficheiro
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 01/08/2020
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 217e48ab21439f03e0b52c894c9aace5b51b1502
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79086208"
---
O Azure Data Factory é um serviço multi-inquilino que tem os seguintes limites predefinidos para garantir que as subscrições dos clientes estão protegidas das cargas de trabalho uns dos outros. Para elevar os limites ao máximo no âmbito da sua subscrição, contacte o suporte.

### <a name="version-2"></a>Versão 2

| Recurso | Limite predefinido | Limite máximo |
| -------- | ------------- | ------------- |
| Fábricas de dados numa subscrição do Azure | 800 | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Número total de entidades, como pipelines, conjuntos de dados, acionadores, serviços ligados e runtimes de integração, numa fábrica de dados | 5\.000 | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Total de núcleos de CPU para Azure-SSIS Integration Runtimes numa subscrição | 256 | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Execuções de pipelines simultâneas por fábrica de dados que são partilhadas entre todos os pipelines na fábrica | 10,000  | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Execuções de atividades externas simultâneas por subscrição por [ região de Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>As atividades externas são geridas no runtime de integração, mas executadas em serviços ligados, incluindo o Databricks, procedimentos armazenados, HDInsight, Web, entre outros.</small> | 3000 | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Execuções de atividades de pipeline simultâneas por subscrição por [região do Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) <br><small>As atividades de pipelines são executadas no runtime de integração, incluindo Lookup, GetMetadata e Delete. </small>| 1000 | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Operações de criação de pipeline simultâneas por subscrição por [região do Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Incluem ligação de teste, procura em listas de pastas e listas de tabelas e pré-visualização de dados. | 200 | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Consumo de unidades de Integração de Dados Simultâneas<sup>1</sup> por subscrição por [região do Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)| Grupo de regiões 1<sup>2</sup>: 6000<br>Grupo de regiões 2<sup>2</sup>: 3000<br>Grupo de regiões 3<sup>2</sup>: 1500 | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Máximo de atividades por pipeline, que incluem atividades interiores para contentores | 40 | 40 |
| Número máximo de runtimes de integração ligados que podem ser criados contra um runtime de integração autoalojado individual | 100 | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Parâmetros máximos por pipeline | 50 | 50 |
| Itens ForEach | 100 000 | 100 000 |
| Paralelismo ForEach | 20 | 50 |
| Execuções em fila máximas por pipeline | 100 | 100 |
| Carateres por expressão | 8,192 | 8,192 |
| Intervalo mínimo de acionador de janela em cascata | 15 min | 15 min |
| Tempo limite máximo para as execuções de atividades de pipeline | 7 dias | 7 dias |
| Bytes por objeto para objetos de pipeline<sup>3</sup> | 200 KB | 200 KB |
| Bytes por objeto para objetos de conjuntos de dados e serviços ligados<sup>3</sup> | 100 KB | 2000 KB |
| Unidades de Integração de Dados<sup>1</sup> por execução de atividade | 256 | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Escrever chamadas à API | 1200/h<br/><br/> Este limite é imposto pelo Azure Resource Manager, não pelo Azure Data Factory. | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Ler chamadas à API | 12 500/h<br/><br/> Este limite é imposto pelo Azure Resource Manager, não pelo Azure Data Factory. | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Monitorização de consultas por minuto | 1,000 | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Operações CRUD de entidades por minuto | 50 | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Tempo máximo de sessão de depuração de fluxo de dados | 8 horas | 8 horas |
| Número simultâneo de fluxos de dados por fábrica | 50 | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Número simultâneo de sessões de depuração de fluxos de dados por utilizador por fábrica | 3 | 3 |
| Limite de TTL do Azure IR de Fluxo de Dados | 4 horas | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |

<sup>1</sup> A unidade de integração de dados (DIU) é utilizada em operações cloud para cloud. Saiba mais sobre as [Unidades de integração de dados (versão 2)](../articles/data-factory/copy-activity-performance.md#data-integration-units). Para obter informações sobre a faturação, veja [Preços do Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

<sup>2</sup> [O Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) está [disponível globalmente](https://azure.microsoft.com/global-infrastructure/services/) para garantir a conformidade dos dados, a eficiência e a redução dos custos de entrada de rede. 

| Grupo de regiões | Regiões | 
| -------- | ------ |
| Grupo de regiões 1 | E.U.A. Central, E.U.A. Leste, E.U.A. Leste 2, Europa do Norte, Europa Ocidental, E.U.A. Oeste, E.U.A. Oeste 2 |
| Grupo de regiões 2 | Leste da Austrália, Austrália Sudeste, Sul do Brasil, Índia Central, Leste do Japão, E.U.A. Centro-Oeste, E.U.A. Centro-Sul, Sudeste Asiático, E.U.A. Centro-Oeste |
| Grupo de regiões 3 | Canadá Central, Ásia Leste, França Central, Coreia do Sul Central, Sul do Reino Unido |

<sup>3</sup> Os objetos de pipelines, conjuntos de dados e serviços ligados representam um agrupamento lógico da sua carga de trabalho. Os limites a estes objetos não estão relacionados com a quantidade de dados que pode mover e processar com o Azure Data Factory. O Data Factory foi concebido para se dimensionar de modo a lidar com petabytes de dados.

### <a name="version-1"></a>Versão 1

| **Recurso** | **Limite predefinido** | **Limite máximo** |
| --- | --- | --- |
| Pipelines numa fábrica de dados |2500 |[Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Conjuntos de dados numa fábrica de dados |5\.000 |[Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Segmentações de dados por conjunto de dados |10 |10 |
| Bytes por objeto para objetos de pipeline<sup>1</sup> |200 kb |200 kb |
| Bytes por objeto para objetos de conjuntos de dados e serviços ligados<sup>1</sup> |100 KB |2000 KB |
| Núcleos de cluster a pedido do Azure HDInsight numa subscrição<sup>2</sup> |60 |[Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Unidades de movimento de dados de cloud por execução de atividade<sup>3</sup> |32 |[Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Contagem de repetir para execuções de atividades de pipeline |1,000 |MaxInt (32 bits) |

<sup>1</sup> Os objetos de pipelines, conjuntos de dados e serviços ligados representam um agrupamento lógico da sua carga de trabalho. Os limites a estes objetos não estão relacionados com a quantidade de dados que pode mover e processar com o Azure Data Factory. O Data Factory foi concebido para se dimensionar de modo a lidar com petabytes de dados.

<sup>2</sup> Os núcleos do HDInsight a pedido são alocados fora da subscrição que contém a fábrica de dados. Como resultado, o limite anterior é o limite de núcleos aplicado pelo Data Factory para núcleos do HDInsight a pedido. É diferente do limite de núcleos associado à sua subscrição do Azure.

<sup>3</sup> A unidade de movimento de dados de cloud (DMU) da versão 1 é utilizada em operações de cópia de cloud para cloud. Saiba mais sobre as [Unidades de movimento de dados de cloud (versão 1)](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units). Para obter informações sobre a faturação, veja [Preços do Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

| **Recurso** | **Limite inferior predefinido** | **Limite mínimo** |
| --- | --- | --- |
| Intervalo de agendamento |15 minutos |15 minutos |
| Intervalo entre tentativas de repetição |1 segundo |1 segundo |
| Valor de tempo limite de repetições |1 segundo |1 segundo |

#### <a name="web-service-call-limits"></a>Limites a chamadas de serviços Web
O Azure Resource Manager tem limites para as chamadas à API. Pode fazer chamadas à API a uma taxa que esteja dentro dos [limites de API do Azure Resource Manager](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits).
