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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79086208"
---
A Azure Data Factory é um serviço multiarrendatário que tem os seguintes limites de incumprimento para garantir que as subscrições dos clientes estão protegidas das cargas de trabalho uns dos outros. Para elevar os limites até ao máximo para a sua subscrição, suporte de contato.

### <a name="version-2"></a>Versão 2

| Recurso | Limite predefinido | Limite máximo |
| -------- | ------------- | ------------- |
| Fábricas de dados numa subscrição do Azure | 800 | [Suporte de contato](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Número total de entidades, tais como oleodutos, conjuntos de dados, gatilhos, serviços ligados e tempos de execução de integração, dentro de uma fábrica de dados | 5000 | [Suporte de contato](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Total de núcleos de CPU para A Integração Azure-SSIS Runtimes sob uma subscrição | 256 | [Suporte de contato](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Gasoduto simultâneo funciona por fábrica de dados que é partilhada entre todos os oleodutos da fábrica | 10,000  | [Suporte de contato](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Atividade externa simultânea funciona por subscrição por região de Runtime de [Integração Azure](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>As atividades externas são geridas no tempo de execução da integração, mas executam em serviços ligados, incluindo Databricks, procedimento armazenado, HDInsights, Web, entre outros.</small> | 3.000 | [Suporte de contato](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Atividade de Pipeline simultâneo funciona por subscrição por região de Runtime de [Integração Azure](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) <br><small>As atividades do pipeline executam no tempo de execução da integração, incluindo Lookup, GetMetadata e Delete.</small>| 1000 | [Suporte de contato](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Operações de autoria simultâneas por subscrição por região de Runtime de [Integração Azure](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Incluindo a ligação de teste, a lista de pastas e a lista de tabelas, os dados de pré-visualização. | 200 | [Suporte de contato](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Unidades de Integração de Dados Simultâneos<sup>1</sup> consumo por subscrição por [região de Execução de Integração Azure](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)| Grupo regional<sup>12</sup>: 6000<br>Grupo regional<sup>2 2</sup>: 3000<br>Grupo regional 3<sup>2</sup>: 1500 | [Suporte de contato](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Atividades máximas por gasoduto, que incluem atividades interiores para contentores | 40 | 40 |
| Número máximo de tempos de execução de integração ligados que podem ser criados contra um único tempo de execução de integração auto-hospedado | 100 | [Suporte de contato](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Parâmetros máximos por gasoduto | 50 | 50 |
| Itens ForEach | 100 000 | 100 000 |
| Por cada paralelismo | 20 | 50 |
| Corridas máximas em fila por gasoduto | 100 | 100 |
| Caracteres por expressão | 8,192 | 8,192 |
| Intervalo mínimo do gatilho da janela caindo | 15 min | 15 min |
| Prazo máximo para funcionações de atividade sinuosidade | 7 dias | 7 dias |
| Bytes por objeto para objetos de gasoduto<sup>3</sup> | 200 KB | 200 KB |
| Bytes por objeto para dataset e objetos de serviço ligados<sup>3</sup> | 100 KB | 2.000 KB |
| Unidades de Integração de Dados<sup>1</sup> por execução de atividade de cópia | 256 | [Suporte de contato](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Escrever chamadas API | 1.200/h<br/><br/> Este limite é imposto pelo Gestor de Recursos Azure, e não pela Azure Data Factory. | [Suporte de contato](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Ler chamadas da API | 12.500/h<br/><br/> Este limite é imposto pelo Gestor de Recursos Azure, e não pela Azure Data Factory. | [Suporte de contato](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Consultas de monitorização por minuto | 1,000 | [Suporte de contato](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Operações DE CRUD da entidade por minuto | 50 | [Suporte de contato](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Tempo máximo da sessão de depuração do fluxo de dados | 8 horas | 8 horas |
| Número simultâneo de fluxos de dados por fábrica | 50 | [Suporte de contato](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Número simultâneo de sessões de depuração de fluxo de dados por utilizador por fábrica | 3 | 3 |
| Limite de Fluxo de Dados Azure IR TTL | 4 horas | [Suporte de contato](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |

<sup>1</sup> A unidade de integração de dados (DIU) é utilizada numa operação de cópia cloud-to-cloud, aprenda mais com as unidades de integração de [dados (versão 2)](../articles/data-factory/copy-activity-performance.md#data-integration-units). Para obter informações sobre faturação, consulte os preços da Fábrica de [Dados azure](https://azure.microsoft.com/pricing/details/data-factory/).

<sup>2</sup> [O Tempo de Execução de Integração Azure](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) está [globalmente disponível](https://azure.microsoft.com/global-infrastructure/services/) para garantir a conformidade, eficiência e redução dos custos de egress da rede. 

| Grupo da região | Regiões | 
| -------- | ------ |
| Grupo regional 1 | Eua Central, Leste dos EUA, LESTE US2, Norte da Europa, Europa Ocidental, Oeste dos EUA, Oeste DOS EUA |
| Grupo regional 2 | Austrália Leste, Austrália Sudeste, Brasil Sul, Índia Central, Japão Leste, Northcentral EUA, Southcentral EUA, Sudeste Asiático, Oeste Dos EUA |
| Grupo 3 da região | Canadá Central, Ásia Oriental, França Central, Coreia Central, Reino Unido Sul |

<sup>3</sup> O pipeline, o conjunto de dados e os objetos de serviço ligados representam um agrupamento lógico da sua carga de trabalho. Os limites para estes objetos não se relacionam com a quantidade de dados que pode mover e processar com a Azure Data Factory. A Data Factory foi concebida para escalar para lidar com petabytes de dados.

### <a name="version-1"></a>Versão 1

| **Recurso** | **Limite predefinido** | **Limite máximo** |
| --- | --- | --- |
| Oleodutos dentro de uma fábrica de dados |2.500 |[Suporte de contato](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Conjuntos de dados dentro de uma fábrica de dados |5000 |[Suporte de contato](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Fatias simultâneas por conjunto de dados |10 |10 |
| Bytes por objeto para objetos de gasoduto<sup>1</sup> |200 KB |200 KB |
| Bytes por objeto para conjunto de dados e objetos de serviço ligados<sup>1</sup> |100 KB |2.000 KB |
| Núcleos de cluster Azure HDInsight a pedido dentro de uma subscrição<sup>2</sup> |60 |[Suporte de contato](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Unidades de movimento de dados em nuvem por atividade de cópia executar<sup>3</sup> |32 |[Suporte de contato](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Contagem de retry para corridas de atividade de gasoduto |1,000 |MaxInt (32 bits) |

<sup>1</sup> O pipeline, o conjunto de dados e os objetos de serviço ligados representam um agrupamento lógico da sua carga de trabalho. Os limites para estes objetos não se relacionam com a quantidade de dados que pode mover e processar com a Azure Data Factory. A Data Factory foi concebida para escalar para lidar com petabytes de dados.

<sup>2</sup> Os núcleos HDInsight a pedido são atribuídos fora da subscrição que contém a fábrica de dados. Como resultado, o limite anterior é o limite central imposto pela Fábrica de Dados para os núcleos HDInsight a pedido. É diferente do limite central que está associado à sua subscrição azure.

<sup>3</sup> A unidade de movimento de dados em nuvem (DMU) para a versão 1 é utilizada numa operação de cópia cloud-to-cloud, saiba mais com as unidades de movimento de [dados cloud (versão 1)](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units). Para obter informações sobre faturação, consulte os preços da Fábrica de [Dados azure](https://azure.microsoft.com/pricing/details/data-factory/).

| **Recurso** | **Limite inferior padrão** | **Limite mínimo** |
| --- | --- | --- |
| Intervalo de agendamento |15 minutos |15 minutos |
| Intervalo entre tentativas de repetição |1 segundo |1 segundo |
| Retry timeout value |1 segundo |1 segundo |

#### <a name="web-service-call-limits"></a>Limites de chamada de serviço web
O Gestor de Recursos Azure tem limites para chamadas aPi. Pode fazer chamadas aPi a um ritmo dentro dos [limites da API do Gestor](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits)de Recursos Azure .
