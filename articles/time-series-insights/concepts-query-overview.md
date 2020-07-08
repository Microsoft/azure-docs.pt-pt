---
title: Consulta de dados em Preview - Azure Time Series Insights / Microsoft Docs
description: Conceitos de consulta de dados e visão geral da API HTTP REST na pré-visualização do Azure Time Series Insights.
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/25/2020
ms.custom: seodec18
ms.openlocfilehash: b6da701081f13055aee3dc40eb3712f2ce2aec60
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86050107"
---
# <a name="data-querying-in-azure-time-series-insights-preview"></a>Consulta de dados em Azure Time Series Insights Preview

O Azure Time Series Insights permite a consulta de dados sobre eventos e metadados armazenados no ambiente através de APIs de superfície pública. Estas APIs também são utilizadas pelo [Time Series Insights Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer).

Três categorias primárias de API estão disponíveis em Time Series Insights:

* **APIs ambiente**: Estas APIs permitem consultas sobre o próprio ambiente de Insights de Séries Tempotadas. Estes podem ser usados para recolher a lista de ambientes a que o chamador tem acesso e metadados ambientais.
* **ApIs de modelos de séries de tempo (TSM-Q):** Permite criar, ler, atualizar e eliminar (CRUD) operações em metadados armazenados no Modelo da Série De Tempo do ambiente. Estes podem ser usados para aceder e editar os casos, tipos e hierarquias.
* **APIs de Séries de Tempo (TSQ):** Permite a recuperação de dados de telemetria ou eventos tal como é registado a partir do fornecedor de origem e permite cálculos e agregações performativas nos dados utilizando funções avançadas de escalar e agregação.

Time Series Insights usa uma linguagem de expressão rica em cadeias, [Expressão de Séries De Tempo (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax), para expressar cálculos.

## <a name="azure-time-series-insights-core-apis"></a>Azure Time Series Insights core APIs

As APIs do núcleo seguinte são suportadas.

[![Visão geral da consulta da série de tempo](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>APIs ambientais

* [Obter Ambientes API](https://docs.microsoft.com/rest/api/time-series-insights/management/environments/get): Devolve a lista de ambientes a que o ouvinte está autorizado a aceder.
* [Obter Ambientes Disponibilidade API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability): Devolve a distribuição da contagem de eventos sobre o tempo do `$ts` evento. Esta API ajuda a determinar se existem eventos no ambiente, devolvendo a contagem de eventos quebrados em intervalos de tempo, se houver algum.
* [Obtenha o Evento Schema API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema): Devolve os metadados de esquema de evento para um determinado período de pesquisa. Esta API ajuda a recuperar todos os metadados e propriedades disponíveis no esquema para o período de pesquisa dado.

## <a name="time-series-model-query-tsm-q-apis"></a>ApIs modelo de séries de tempo (TSM-Q)

A maioria destas APIs suportam a operação de execução de lotes para permitir operações CRUD em várias entidades do Modelo série de tempo:

* [Definições de modelo API](https://docs.microsoft.com/rest/api/time-series-insights/preview#model-settings-api): Ativa *o GET* e o *PATCH* no tipo predefinido e no nome do modelo do ambiente.
* [Tipos API](https://docs.microsoft.com/rest/api/time-series-insights/preview#types-api): Permite CRUD nos tipos de Séries Temporizadas e nas suas variáveis associadas.
* [Hierarquias API](https://docs.microsoft.com/rest/api/time-series-insights/preview#hierarchies-api): Permite CRUD nas hierarquias da Série Temporal e seus caminhos de campo associados.
* [Instâncias API](https://docs.microsoft.com/rest/api/time-series-insights/preview#instances-api): Permite CRUD em instâncias de Séries Temporizadas e nos seus campos de instâncias associados. Além disso, a API de Instâncias suporta as seguintes operações:
  * [Pesquisa](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/search): Recupera uma lista parcial de acessos em casos de séries de tempo com base em atributos de exemplo.
  * [Sugestione:](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/suggest)Pesquisa e sugere uma lista parcial de acessos em casos de séries de tempo com base em atributos de instância.

## <a name="time-series-query-tsq-apis"></a>Consultas de Séries De Tempo (TSQ) APIs

Estas APIs estão disponíveis em todas as lojas da nossa solução de armazenamento multicamadas no Time Series Insights. Os parâmetros URL de consulta são utilizados para especificar o [tipo de loja](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters) que a consulta deve executar em:

* [Get Events API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents): Permite consulta e recuperação de eventos crus e os horários de eventos associados à medida que são gravados em Time Series Insights do fornecedor de origem. Esta API permite a recuperação de eventos crus para um determinado ID da Série de Tempo e período de pesquisa. Esta API suporta a paginação para recuperar o conjunto completo de dados de resposta para a entrada selecionada. 

* [Obter API série](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries): Permite consulta e recuperação de valores calculados e dos timetamps de eventos associados, aplicando cálculos definidos por variáveis em eventos brutos. Estas variáveis podem ser definidas no Modelo de Séries Tempotamos ou em linha na consulta. Esta API suporta a paginação para recuperar o conjunto completo de dados de resposta para a entrada selecionada. 

* [Agregação Série API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries): Permite consulta e recuperação de valores agregados e dos intervalos de tempo associados, aplicando cálculos definidos por variáveis em eventos brutos. Estas variáveis podem ser definidas no Modelo de Séries Tempotamos ou em linha na consulta. Esta API suporta a paginação para recuperar o conjunto completo de dados de resposta para a entrada selecionada. 
  
  Para um intervalo de pesquisa especificado, esta API devolve uma resposta agregada por variável por intervalo para um ID da Série de Tempo. O número de intervalos no conjunto de dados de resposta é calculado contando carrapatos de época (o número de milissegundos que decorreram desde a época Unix - 1 de janeiro de 1970) e dividindo os carrapatos pelo tamanho do intervalo especificado na consulta.

  Os tempos devolvidos no conjunto de resposta são dos limites do intervalo esquerdo, não dos eventos amostrados do intervalo. 

## <a name="next-steps"></a>Próximos passos

- Leia mais sobre diferentes variáveis que podem ser definidas no [Modelo série de tempo.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-tsm)

- Leia mais sobre como consultar dados do [Time Series Insights Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer).
