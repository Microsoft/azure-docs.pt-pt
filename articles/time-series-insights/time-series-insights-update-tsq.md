---
title: Consulta de dados em Pré-visualização - Azure Time Series Insights [ Microsoft Docs
description: Conceitos de consulta de dados e visão geral da Http REST API na Pré-visualização de Insights da Série De Tempo Azure.
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/25/2020
ms.custom: seodec18
ms.openlocfilehash: 23094ec71dac5780def10e16b90de0b818ef3c68
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80284896"
---
# <a name="data-querying-in-azure-time-series-insights-preview"></a>Consulta de dados na Pré-visualização de Insights da Série de Tempo azure

A Azure Time Series Insights permite a consulta de dados sobre eventos e metadados armazenados no ambiente através de APIs de superfície pública. Estas APIs também são usadas pelo [Time Series Insights Explorer.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer)

Três categorias primárias de API estão disponíveis em Time Series Insights:

* **APIs ambiente**: Estas APIs permitem consultas no próprio ambiente time series Insights. Estes podem ser usados para recolher a lista de ambientes a que o chamador tem acesso e metadados ambientais.
* **APIs de Modelos de Série de Tempo (TSM-Q)**: Permite criar, ler, atualizar e eliminar operações (CRUD) em metadados armazenados no Modelo de Série seletiva do ambiente. Estes podem ser usados para aceder e editar os casos, tipos e hierarquias.
* **APIs da Série De Tempo (TSQ)**: Permite a recuperação de dados de telemetria ou eventos tal como são registados pelo fornecedor de origem e permite computações e agregações performantes nos dados utilizando funções avançadas de escalar e agregados.

Time Series Insights usa uma linguagem de expressão baseada em cordas ricas, Expressão da [Série de Tempo (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)para expressar cálculos.

## <a name="azure-time-series-insights-core-apis"></a>Apis core da série de tempo azure

As seguintes APIs fundamentais são suportadas.

[![Visão geral da consulta da série temporal](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>APIs do Ambiente

* [Obter Ambientes API](https://docs.microsoft.com/rest/api/time-series-insights/management/environments/get): Devolve a lista de ambientes a que o chamador está autorizado a aceder.
* [Obtenha Ambientes Disponibilidade API:](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability)Devolve a distribuição da `$ts`contagem de eventos ao longo do tempo do evento . Esta API ajuda a determinar se há algum evento no ambiente devolvendo a contagem de eventos quebrados em intervalos de tempo, se houver.
* [Obtenha evento Schema API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema): Devolve os metadados do evento para um determinado período de pesquisa. Esta API ajuda a recuperar todos os metadados e propriedades disponíveis no esquema para o período de pesquisa dado.

## <a name="time-series-model-query-tsm-q-apis"></a>Apis de modelo de série de tempo (TSM-Q)

A maioria destas APIs suportam a operação de execução de lotes para permitir operações CRUD de lote em várias entidades do Modelo de Série de Tempo:

* [Definições do modelo API](https://docs.microsoft.com/rest/api/time-series-insights/preview#model-settings-api): Ativa *o GET* e o *PATCH* no tipo predefinido e no nome do modelo do ambiente.
* [Tipos API:](https://docs.microsoft.com/rest/api/time-series-insights/preview#types-api)Permite o CRUD nos tipos de Séries temporais e respetivas variáveis associadas.
* [Hierarquias API](https://docs.microsoft.com/rest/api/time-series-insights/preview#hierarchies-api): Permite o CRUD nas hierarquias da Time Series e seus caminhos de campo associados.
* [Instâncias API](https://docs.microsoft.com/rest/api/time-series-insights/preview#instances-api): Permite o CRUD em instâncias de Série seletiva e os seus campos de instância associados. Adicionalmente, a API de instâncias apoia as seguintes operações:
  * [Pesquisa](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/search): Recupera uma lista parcial de acessos em busca de instâncias de séries de tempo com base em atributos de instância.
  * [Sugestão](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/suggest): Pesquisas e sugere uma lista parcial de acessos em busca de instâncias de séries de tempo com base em atributos de instâncias.

## <a name="time-series-query-tsq-apis"></a>Apis de consulta de série de tempo (TSQ)

Estas APIs estão disponíveis em ambas as lojas na nossa solução de armazenamento multicamadas em Time Series Insights. Os parâmetros url de consulta são usados para especificar o tipo de [loja](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters) que a consulta deve executar em:

* [Obtenha Eventos API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents): Permite a consulta e a recuperação de eventos brutos e os selos de tempo associados à medida que são gravados em Time Series Insights do fornecedor de origem. Esta API permite a recuperação de eventos brutos para um determinado ID da Série de Tempo e intervalo de pesquisa. Esta API suporta a paginação para recuperar o conjunto completo de dados de resposta para a entrada selecionada. 

* [Obter Série API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries): Permite a consulta e recuperação de valores computacionados e os selos de tempo associados do evento aplicando cálculos definidos por variáveis em eventos brutos. Estas variáveis podem ser definidas no Modelo da Série De Tempo ou fornecidas inline na consulta. Esta API suporta a paginação para recuperar o conjunto completo de dados de resposta para a entrada selecionada. 

* [Série API agregado](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries): Permite a consulta e a recuperação dos valores agregados e dos intervalos associados aplicando cálculos definidos por variáveis em eventos brutos. Estas variáveis podem ser definidas no Modelo da Série De Tempo ou fornecidas inline na consulta. Esta API suporta a paginação para recuperar o conjunto completo de dados de resposta para a entrada selecionada. 
  
  Para um intervalo e intervalo de pesquisa especificados, este API devolve uma resposta agregada por variável por intervalo para um ID da Série de Tempo. O número de intervalos no conjunto de dados de resposta é calculado contando carrapatos de época (o número de milissegundos que decorreram desde a época Unix - 1 de janeiro de 1970) e dividindo os carrapatos pelo intervalo de tempo especificado na consulta.

  Os carimbos de tempo devolvidos no conjunto de resposta são dos limites do intervalo esquerdo, não dos eventos amostrados a partir do intervalo. 

## <a name="next-steps"></a>Passos seguintes

- Leia mais sobre diferentes variáveis que podem ser definidas no Modelo série [de tempo](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-tsm).
- Leia mais sobre como consultar dados do [Time Series Insights Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer).
