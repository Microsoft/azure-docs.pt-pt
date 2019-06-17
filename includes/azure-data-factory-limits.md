---
title: incluir ficheiro
description: incluir ficheiro
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 5/30/2019
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: ac0747dcff4e74363a58dc9aaf6da4dbd4c6a1c7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66427596"
---
O Azure Data Factory é um serviço multi-inquilino que tem os seguintes limites predefinidos no local para se certificar de subscrições de cliente estão protegidas de cargas de trabalho entre si. Para aumentar os limites até o máximo para a sua subscrição, contacte o suporte.

### <a name="version-2"></a>Versão 2

| Resource | Limite predefinido | Limite máximo |
| -------- | ------------- | ------------- |
| Fábricas de dados numa subscrição do Azure | 50 | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Número total de entidades, como pipelines, conjuntos de dados, acionadores, serviços ligados e runtimes de integração, dentro de uma fábrica de dados | 5,000 | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Núcleos de CPU total para Runtimes de integração de SSIS do Azure sob uma subscrição | 256 | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Execuções de pipeline em simultâneo por fábrica de dados que é partilhada entre todos os pipelines na fábrica de | 10,000  | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Execuções de atividades de externas em simultâneo por subscrição por [região do Runtime de integração do Azure](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Atividades externas são geridas no integration runtime, mas executar nos serviços ligados, incluindo o Databricks, procedimento armazenado, HDInsights e outras pessoas.</small> | 3000 | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Execuções de atividade do Pipeline em simultâneo por subscrição por [região do Runtime de integração do Azure](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) <br><small>Atividades do pipeline execute no tempo de execução de integração, incluindo Lookup, GetMetadata e eliminam. </small>| 1000 | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Simultâneas criação operações por subscrição por [região do Runtime de integração do Azure](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Incluindo Testar ligação, a lista de pastas de procura e a lista de tabelas, pré-visualizar os dados. | 200 | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Unidades de integração de dados em simultâneo<sup>1</sup> consumo por subscrição por [região do Runtime de integração do Azure](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)| Grupo de região 1<sup>2</sup>: 6000<br>Grupo 2 da região<sup>2</sup>: 3000<br>Grupo de região 3<sup>2</sup>: 1500 | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Atividades máximas por pipeline, que inclui atividades internas para contentores | 40 | 40 |
| Número máximo de runtimes de integração ligado que podem ser criados em relação a um runtime de integração autoalojado único | 100 | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Parâmetros máximos por pipeline | 50 | 50 |
| Itens ForEach | 100,000 | 100,000 |
| Paralelismo ForEach | 20 | 50 |
| Carateres por expressão | 8,192 | 8,192 |
| Intervalo de Acionador de janela em cascata mínimo | 15 min | 15 min |
| Tempo limite máximo para a atividade do pipeline é executado | 7 dias | 7 dias |
| Bytes por objeto para objetos do pipeline<sup>3</sup> | 200 KB | 200 KB |
| Bytes por objeto para o conjunto de dados e objetos do serviço ligado<sup>3</sup> | 100 KB | 2\.000 KB |
| Unidades de integração de dados<sup>1</sup> por execução de atividade de cópia | 256 | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Escrever chamadas de API | 2500/h<br/><br/> Este limite é imposto pelo Azure Resource Manager, não o Azure Data Factory. | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Chamadas de API de leitura | 12,500/h<br/><br/> Este limite é imposto pelo Azure Resource Manager, não o Azure Data Factory. | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Monitorização de consultas por minuto | 1,000 | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Operações CRUD de entidade por minuto | 50 | [Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |

<sup>1</sup> a unidade de integração de dados (DIU) é utilizada numa operação de cópia na cloud para a cloud, saiba mais no [unidades de integração de dados (versão 2)](../articles/data-factory/copy-activity-performance.md#data-integration-units). Para obter informações sobre faturação, consulte [preços do Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

<sup>2</sup> [Runtime de integração do azure](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) é [globalmente disponível](https://azure.microsoft.com/global-infrastructure/services/) para garantir a conformidade de dados, eficiência e a rede reduzido os custos de saída. 

| Grupo de região | Regiões | 
| -------- | ------ |
| Grupo de região 1 | E.U.A. central, E.U.A. leste, e.u.a. Leste 2, Europa do Norte, Europa Ocidental, E.U.A. oeste, E.U.A. oeste 2 |
| Grupo 2 da região | Leste da Austrália, Sudeste da Austrália, sul do Brasil, Índia Central, leste do Japão, Northcentral dos EUA, Centro-Sul, Sudeste asiático, oeste dos E.U.A. |
| Grupo de região 3 | Centro do Canadá, Ásia Oriental, França Central, Coreia Central, sul do Reino Unido |

<sup>3</sup> pipeline, conjunto de dados e objetos do serviço ligado representam um agrupamento lógico de sua carga de trabalho. Limites para esses objetos sem relação com a quantidade de dados podem mover e processar com o Azure Data Factory. Fábrica de dados foi concebida para escalar para processar petabytes de dados.

### <a name="version-1"></a>Versão 1

| **Recurso** | **Limite predefinido** | **Limite máximo** |
| --- | --- | --- |
| Fábricas de dados numa subscrição do Azure |50 |[Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Pipelines numa fábrica de dados |2,500 |[Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Conjuntos de dados dentro de uma fábrica de dados |5,000 |[Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Setores simultâneos por conjunto de dados |10 |10 |
| Bytes por objeto para objetos do pipeline<sup>1</sup> |200 KB |200 KB |
| Bytes por objeto para dados de definir e objetos do serviço de ligado<sup>1</sup> |100 KB |2\.000 KB |
| Núcleos de cluster a pedido do Azure HDInsight numa subscrição<sup>2</sup> |60 |[Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Unidades de movimento de dados por execução de atividade de cópia da cloud<sup>3</sup> |32 |[Contacte o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Número de execuções de atividades do pipeline de tentativas |1,000 |MaxInt (32 bits) |

<sup>1</sup> pipeline, conjunto de dados e objetos do serviço ligado representam um agrupamento lógico de sua carga de trabalho. Limites para esses objetos sem relação com a quantidade de dados podem mover e processar com o Azure Data Factory. Fábrica de dados foi concebida para escalar para processar petabytes de dados.

<sup>2</sup> núcleos de HDInsight a pedido são alocados fora a subscrição que contém a fábrica de dados. Como resultado, o limite de anterior é o limite de núcleos imposta por Data Factory para núcleos de HDInsight a pedido. É diferente do que o limite de núcleos que está associada à sua subscrição do Azure.

<sup>3</sup> a unidade de movimento de dados na cloud (DMU) para a versão 1 é utilizada numa operação de cópia de cloud para a cloud, saiba mais a partir da [unidades de movimento de dados (versão 1) da Cloud](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units). Para obter informações sobre faturação, consulte [preços do Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

| **Recurso** | **Limite inferior predefinido** | **Limite mínimo** |
| --- | --- | --- |
| Intervalo de agendamento |15 minutos |15 minutos |
| Intervalo entre tentativas de repetição |1 segundo |1 segundo |
| Repita o valor de tempo limite |1 segundo |1 segundo |

#### <a name="web-service-call-limits"></a>Limites de chamada de serviço Web
O Gestor de recursos do Azure tem limites para chamadas de API. Pode fazer chamadas à API a uma taxa dentro de [limita a API do Azure Resource Manager](../articles/azure-subscription-service-limits.md#resource-group-limits).
