---
title: incluir ficheiro
description: incluir ficheiro
services: data-factory
author: chez-charlie
ms.service: data-factory
ms.topic: include
ms.date: 10/28/2020
ms.author: chez
ms.custom: include file
ms.openlocfilehash: 4cbe179d015c71094e6813666a6fd0cee3e5e3c2
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92909049"
---
A Azure Data Factory é um serviço multitenant que tem os seguintes limites padrão em vigor para garantir que as subscrições do cliente estão protegidas das cargas de trabalho uns dos outros. Para elevar os limites até ao máximo para a sua subscrição, contacte o suporte.

### <a name="version-2"></a>Versão 2

| Recurso | Limite predefinido | Limite máximo |
| -------- | ------------- | ------------- |
| Fábricas de dados numa subscrição do Azure | 800 | 800 |
| Número total de entidades, tais como oleodutos, conjuntos de dados, gatilhos, serviços ligados, Pontos Finais Privados e tempos de integração, dentro de uma fábrica de dados | 5000 | [Suporte de contacto](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Núcleos totais do CPU para Runtimes de Integração Azure-SSIS sob uma subscrição | 256 | [Suporte de contacto](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gasoduto simultâneo funciona por fábrica de dados que é partilhado entre todos os oleodutos da fábrica | 10,000  | 10,000 |
| Atividade externa simultânea funciona por subscrição por [região de runtime de integração Azure](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location)<br><small>As atividades externas são geridas em tempo de integração, mas executadas em serviços ligados, incluindo Databricks, procedimento armazenado, HDInsights, Web, entre outros. Este limite não se aplica ao IR auto-alojado.</small> | 3.000 | 3.000 |
| Atividade do Pipeline simultâneo é executada por subscrição por [região de runtime de integração Azure](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location) <br><small>As atividades de pipeline executam em tempo de integração, incluindo Lookup, GetMetadata e Delete. Este limite não se aplica ao IR auto-alojado.</small> | 1,000 | 1,000                                                        |
| Operações de autoria simultânea por subscrição por [região de runtime de integração Azure](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location)<br><small>Incluindo a ligação de teste, a lista de pastas de navegação e a lista de tabelas, dados de pré-visualização. Este limite não se aplica ao IR auto-alojado.</small> | 200 | 200                                                          |
| Unidades de Integração de Dados Simultâneas<sup>1</sup> consumo por subscrição por [região de runtime de integração Azure](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)| Grupo regional<sup>12</sup>: 6.000<br>Grupo regional<sup>2 2</sup>: 3.000<br>Grupo regional 3<sup>2</sup>: 1.500 | Grupo regional<sup>12</sup>: 6.000<br/>Grupo regional<sup>2 2</sup>: 3.000<br/>Grupo regional 3<sup>2</sup>: 1.500 |
| Atividades máximas por gasoduto, que incluem atividades interiores para contentores | 40 | 40 |
| Número máximo de tempos de integração ligados que podem ser criados contra um único tempo de integração auto-hospedado | 100 | [Suporte de contacto](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Parâmetros máximos por gasoduto | 50 | 50 |
| Itens ForEach | 100.000 | 100.000 |
| ForEach paralelismo | 20 | 50 |
| Corridas máximas em fila por gasoduto | 100 | 100 |
| Caracteres por expressão | 8,192 | 8,192 |
| Intervalo mínimo de disparo de janela de caindo | 15 min | 15 min |
| Prazo máximo para a atividade do gasoduto | 7 dias | 7 dias |
| Bytes por objeto para objetos de gasoduto<sup>3</sup> | 200 KB | 200 KB |
| Bytes por objeto para conjunto de dados e objetos de serviço ligados<sup>3</sup> | 100 KB | 2.000 KB |
| Unidades de Integração de Dados<sup>1</sup> por função de cópia executadas | 256 | 256 |
| Escreva chamadas API | 1.200/h | 1.200/h<br/><br/> Este limite é imposto pelo Azure Resource Manager, não pela Azure Data Factory. |
| Leia chamadas de API | 12.500/h | 12.500/h<br/><br/> Este limite é imposto pelo Azure Resource Manager, não pela Azure Data Factory. |
| Consultas de monitorização por minuto | 1,000 | 1,000 |
| Tempo máximo da sessão de depurar fluxo de dados | 8 horas | 8 horas |
| Número simultâneo de fluxos de dados por período de execução de integração | 50 | [Suporte de contacto](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Número simultâneo de sessões de depuro de fluxo de dados por utilizador por fábrica | 3 | 3 |
| Limite de TTL irtl do fluxo de dados | 4 horas |  4 horas |

<sup>1</sup> A unidade de integração de dados (DIU) é utilizada numa operação de cópia nuvem-nuvem, saiba mais com unidades de integração de [dados (versão 2)](../articles/data-factory/copy-activity-performance.md#data-integration-units). Para obter informações sobre a faturação, consulte [os preços da Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

<sup>2</sup> O Tempo de [Execução da Integração Azure](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) está [globalmente disponível](https://azure.microsoft.com/global-infrastructure/services/) para garantir a conformidade, eficiência e custos reduzidos de saída de rede. 

| Grupo regional | Regiões |
| -------- | ------ |
| Região grupo 1 | Central EUA, Leste dos EUA, Leste DOS EUA, Norte da Europa, Europa Ocidental, Eua Ocidental, Oeste dos EUA 2 |
| Grupo regional 2 | Austrália Leste, Austrália Sudeste, Brasil Sul, Índia Central, Japão Leste, Norte-Americano norte-americano, Sudeste Asiático, Centro-Oeste dos EUA |
| Grupo regional 3 | Canadá Central, Ásia Oriental, França Central, Coreia Central, Reino Unido Sul |

<sup>3</sup> Pipeline, conjunto de dados e objetos de serviço ligados representam um agrupamento lógico da sua carga de trabalho. Os limites para estes objetos não se relacionam com a quantidade de dados que pode mover e processar com a Azure Data Factory. A Data Factory foi concebida para escalar para lidar com petabytes de dados.

### <a name="version-1"></a>Versão 1

| **Recurso** | **Limite predefinido** | **Limite máximo** |
| --- | --- | --- |
| Gasodutos dentro de uma fábrica de dados |2.500 |[Suporte de contacto](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Conjuntos de dados dentro de uma fábrica de dados |5000 |[Suporte de contacto](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Fatias simultâneas por conjunto de dados |10 |10 |
| Bytes por objeto para objetos de gasoduto<sup>1</sup> |200 KB |200 KB |
| Bytes por objeto para conjunto de dados e objetos de serviço ligados<sup>1</sup> |100 KB |2.000 KB |
| Núcleos de cluster a pedido do Azure HDInsight dentro de uma subscrição<sup>2</sup> |60 |[Suporte de contacto](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Unidades de movimento de dados em nuvem por atividade de cópia executam<sup>3</sup> |32 |32 |
| Contagem de repetições para a atividade do gasoduto corre |1,000 |Maxint (32 bit) |

<sup>1</sup> Pipeline, conjunto de dados e objetos de serviço ligados representam um agrupamento lógico da sua carga de trabalho. Os limites para estes objetos não se relacionam com a quantidade de dados que pode mover e processar com a Azure Data Factory. A Data Factory foi concebida para escalar para lidar com petabytes de dados.

<sup>2</sup> Os núcleos HDInsight a pedido são atribuídos fora da subscrição que contém a fábrica de dados. Como resultado, o limite anterior é o limite de núcleo imposto pela Data Factory para os núcleos HDInsight a pedido. É diferente do limite central que está associado à sua subscrição do Azure.

<sup>3</sup> A unidade de movimento de dados em nuvem (DMU) para a versão 1 é utilizada numa operação de cópia nuvem-nuvem, saiba mais com as unidades de movimento de dados cloud [(versão 1)](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units). Para obter informações sobre a faturação, consulte [os preços da Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

| **Recurso** | **Limite inferior predefinido** | **Limite mínimo** |
| --- | --- | --- |
| Intervalo de agendamento |15 minutos |15 minutos |
| Intervalo entre tentativas de repetição |1 segundo |1 segundo |
| Retry value timeout |1 segundo |1 segundo |

#### <a name="web-service-call-limits"></a>Limites de chamada de serviço web
O Gestor de Recursos Azure tem limites para chamadas API. Pode efetuar chamadas API a uma taxa dentro dos limites da [API do Gestor de Recursos Azure](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits).
