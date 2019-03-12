---
title: incluir ficheiro
description: incluir ficheiro
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 1/10/2019
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 42c1856f30484532e1ace2e84187bcaaacdf4c72
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553835"
---
O Azure Data Factory é um serviço multi-inquilino que tem os seguintes limites predefinidos no local para se certificar de subscrições de cliente estão protegidas de cargas de trabalho entre si. Para aumentar os limites até o máximo para a sua subscrição, contacte o suporte.

### <a name="version-2"></a>Versão 2

| Recurso | Limite predefinido | Limite máximo |
| -------- | ------------- | ------------- |
| Fábricas de dados numa subscrição do Azure | 50 | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Número total de entidades, como pipelines, conjuntos de dados, acionadores, serviços ligados e runtimes de integração, dentro de uma fábrica de dados | 5.000 | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Núcleos de CPU total para Runtimes de integração de SSIS do Azure sob uma subscrição | 256 | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Execuções de pipeline em simultâneo por fábrica de dados que é partilhada entre todos os pipelines na fábrica de | 10,000  | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Atividades máximas por pipeline, que inclui atividades internas para contentores | 40 | 40 |
| Número máximo de runtimes de integração ligado que podem ser criados em relação a um runtime de integração autoalojado único | 20 | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Parâmetros máximos por pipeline | 50 | 50 |
| Itens ForEach | 100 000 | 100 000 |
| Paralelismo ForEach | 20 | 50 |
| Carateres por expressão | 8,192 | 8,192 |
| Intervalo de Acionador de janela em cascata mínimo | 15 min | 15 min |
| Tempo limite máximo para a atividade do pipeline é executado | 7 dias | 7 dias |
| Bytes por objeto para objetos do pipeline<sup>1</sup> | 200 KB | 200 KB |
| Bytes por objeto para o conjunto de dados e objetos do serviço ligado<sup>1</sup> | 100 KB | 2.000 KB |
| Unidades de integração de dados por execução de atividade de cópia<sup>3</sup> | 256 | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Escrever chamadas de API | 2500/h<br/><br/> Este limite é imposto pelo Azure Resource Manager, não o Azure Data Factory. | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Chamadas de API de leitura | 12,500/h<br/><br/> Este limite é imposto pelo Azure Resource Manager, não o Azure Data Factory. | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Monitorização de consultas por minuto | 1,000 | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Operações CRUD de entidade por minuto | 50 | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |


### <a name="version-1"></a>Versão 1

| **Recurso** | **Limite predefinido** | **Limite máximo** |
| --- | --- | --- |
| Fábricas de dados numa subscrição do Azure |50 |[Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Pipelines numa fábrica de dados |2,500 |[Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Conjuntos de dados dentro de uma fábrica de dados |5.000 |[Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Setores simultâneos por conjunto de dados |10 |10 |
| Bytes por objeto para objetos do pipeline<sup>1</sup> |200 KB |200 KB |
| Bytes por objeto para dados de definir e objetos do serviço de ligado<sup>1</sup> |100 KB |2.000 KB |
| Núcleos de cluster a pedido do Azure HDInsight numa subscrição<sup>2</sup> |60 |[Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Unidades de movimento de dados por execução de atividade de cópia da cloud<sup>3</sup> |32 |[Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Número de execuções de atividades do pipeline de tentativas |1,000 |MaxInt (32 bits) |

<sup>1</sup>pipeline, conjunto de dados e objetos do serviço ligado representam um agrupamento lógico de sua carga de trabalho. Limites para esses objetos sem relação com a quantidade de dados podem mover e processar com o Azure Data Factory. Fábrica de dados foi concebida para escalar para processar petabytes de dados.

<sup>2</sup>núcleos de HDInsight a pedido são alocados fora a subscrição que contém a fábrica de dados. Como resultado, o limite de anterior é o limite de núcleos imposta por Data Factory para núcleos de HDInsight a pedido. É diferente do que o limite de núcleos que está associada à sua subscrição do Azure.

<sup>3</sup>a unidade de integração de dados (DIU) para a versão 2 ou a unidade de movimento de dados (DMU) de cloud para a versão 1 é utilizada numa operação de cópia na cloud para a cloud. É uma medida que representa o poder de uma única unidade no Data Factory. Ele combina a CPU, memória e alocações de recursos de rede. Pode obter um maior débito de cópia utilizando DMUs mais em alguns cenários. Para obter mais informações, consulte [unidades de integração de dados (versão 2)](../articles/data-factory/copy-activity-performance.md#data-integration-units) e [unidades de movimento de dados (versão 1) da Cloud](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units). Para obter informações sobre faturação, consulte [preços do Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

<sup>4</sup>o integration runtime (IR) é a infraestrutura de computação utilizada pelo Azure Data Factory para proporcionar capacidades de integração de dados em ambientes de rede diferentes, como o movimento de dados, distribuição de atividades para serviços, computação e execução dos pacotes do SSIS. Para obter mais informações, consulte [descrição geral do runtime de integração](../articles/data-factory/concepts-integration-runtime.md).

| **Recurso** | **Limite inferior predefinido** | **Limite mínimo** |
| --- | --- | --- |
| Intervalo de agendamento |15 minutos |15 minutos |
| Intervalo entre tentativas de repetição |1 segundo |1 segundo |
| Repita o valor de tempo limite |1 segundo |1 segundo |

#### <a name="web-service-call-limits"></a>Limites de chamada de serviço Web
O Gestor de recursos do Azure tem limites para chamadas de API. Pode fazer chamadas à API a uma taxa dentro de [limita a API do Azure Resource Manager](../articles/azure-subscription-service-limits.md#resource-group-limits).
