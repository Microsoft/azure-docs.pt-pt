---
title: Consulta de dados em Pré-visualização - Azure Time Series Insights  Microsoft Docs
description: Conceitos de consulta de dados e visão geral da Http REST API na Pré-visualização de Insights da Série De Tempo Azure.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 898515f49672a19ed8bf1c62439128b6727afc73
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77087400"
---
# <a name="data-querying-in-azure-time-series-insights-preview"></a>Consulta de dados na Pré-visualização de Insights da Série de Tempo azure

A Pré-visualização de Insights da Série de Tempo Azure permite a consulta de dados sobre eventos e metadados armazenados no ambiente através de APIs de superfície pública. Estas APIs também são usadas no explorador de [pré-visualização](./time-series-insights-update-explorer.md)de Insights da Série de Tempo.

Três categorias primárias de API estão disponíveis em Time Series Insights:

* **APIs ambiente**: Estas APIs permitem consultas no próprio ambiente time series Insights. Exemplos de consultas são a lista de ambientes a que o chamador tem acesso e metadados ambientais.
* **APIs de Modelos de Série de Tempo (TSM-Q)** : Permite criar, ler, atualizar e eliminar operações (CRUD) em metadados armazenados no ambiente parte do modelo da série de tempo. Exemplos são casos, tipos e hierarquias.
* APIs de Consulta de **Série seletiva (TSQ)** : Permite a recuperação de dados de telemetria ou eventos tal como são registados pelo fornecedor de origem ou reduzindo os dados utilizando funções estratosgráficas e agregadas armazenadas em parte de variáveis. Estas APIs podem realizar operações para transformar, combinar e aplicar computações em dados da série de tempo.

Time Series Insights usa uma linguagem de expressão baseada em cordas ricas, Expressão da [Série de Tempo (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx)para expressar cálculos.

## <a name="azure-time-series-insights-preview-core-apis"></a>Apis de pré-visualização da série de tempo azure

As seguintes APIs fundamentais são suportadas.

[visão geral da série de tempo ![](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>APIs do Ambiente

Estão disponíveis as seguintes APIs ambiente:

* [Obter Ambientes API](/rest/api/time-series-insights/management/environments/get): Devolve a lista de ambientes a que o chamador está autorizado a aceder.
* [Obtenha Ambientes Disponibilidade API:](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability)Devolve a distribuição da contagem de eventos ao longo do horário do evento `$ts`. Esta API ajuda a determinar se há algum evento no timestamp devolvendo a contagem de eventos, se houver.
* [Obtenha evento Schema API](/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema): Devolve os metadados do evento para um determinado período de pesquisa. Esta API ajuda a recuperar todos os metadados e propriedades disponíveis no esquema para o período de pesquisa dado.

## <a name="time-series-model-query-tsm-q-apis"></a>Apis de modelo de série de tempo (TSM-Q)

Estão disponíveis as seguintes APIs de Modelo-Consulta de Série seletiva. A maioria destas APIs suportam a operação de execução de lotes para permitir operações crud de lote em várias entidades modelo de séries de tempo:

* [Definições do modelo API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api): Ativa *o GET* e o *PATCH* no tipo predefinido e no nome do modelo do ambiente.
* [Tipos API:](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api)Permite o CRUD nos tipos de Séries temporais e respetivas variáveis associadas.
* [Hierarquias API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api): Permite o CRUD nas hierarquias da Time Series e seus caminhos de campo associados.
* [Instâncias API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api): Permite o CRUD em instâncias de Série seletiva e os seus campos de instância associados. Adicionalmente, a API de instâncias apoia as seguintes operações:
  * [Pesquisa](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/search): Recupera uma lista parcial de acessos em busca de instâncias de séries de tempo com base em atributos de instância.
  * [Sugestão](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/suggest): Pesquisas e sugere uma lista parcial de acessos em busca de instâncias de séries de tempo com base em atributos de instâncias.

## <a name="time-series-query-tsq-apis"></a>Apis de consulta de série de tempo (TSQ)

Estão disponíveis as seguintes APIs de Consulta de Série seletiva. Estas APIs estão disponíveis em todos os armazenamentos multicamadas suportados em Time Series Insights. Os parâmetros url de consulta são usados para especificar o tipo de [loja](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters) que a consulta deve executar em:

* [Obtenha Eventos API](/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents): Permite a consulta e a recuperação de dados da Time Series Insights de eventos à medida que são gravados em Time Series Insights do fornecedor de origem. Esta API permite a recuperação de eventos brutos para um determinado ID da Série de Tempo e intervalo de pesquisa. Esta API suporta a paginação para recuperar o conjunto de dados completo para a entrada selecionada. 

* [Obtenha a Série API](/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries): Permite a consulta e a recuperação dos dados da Time Series Insights de eventos capturados utilizando dados gravados no fio. Os valores que são devolvidos baseiam-se nas variáveis que foram definidas no modelo ou fornecidas inline. Esta API suporta a paginação para recuperar o conjunto de dados completo para a entrada selecionada. Esta API ajuda na definição de propriedades ou colunas calculadas.

    >[!NOTE]
    > A cláusula de agregação é ignorada mesmo que seja especificada num modelo ou fornecida inline.

  O Get Series API devolve um valor de Série de Tempo para cada variável para cada intervalo. Um valor da Série de Tempo é um formato que a Time Series Insights utiliza para a saída JSON a partir de uma consulta. Os valores que são devolvidos baseiam-se no ID da Série De Tempo e no conjunto de variáveis que foram fornecidas.

* [Série API agregado](/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregatevariable): Permite a consulta e a recuperação dos dados da Time Series Insights de eventos capturados através da amostragem e agregação de dados gravados. Esta API apoia a execução contínua utilizando [tokens de continuação.](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#queryresultpage)

  A Série API da Série AGREGAda devolve um valor de Série De Tempo para cada variável para cada intervalo. Os valores baseiam-se no ID da Série De Tempo e no conjunto de variáveis que foram fornecidas. A API da Série Agregada obtém a redução utilizando variáveis armazenadas no Modelo da Série De Tempo ou fornecidas inline para agregar ou recolher dados de amostras.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [armazenamento e ingresso](./time-series-insights-update-storage-ingress.md) na Pré-visualização de Insights da Série De Tempo Azure.
- Leia o artigo de [modelação](./time-series-insights-update-tsm.md) de dados de pré-visualização da Série De Tempo Insights.
- Descubra [as melhores práticas para escolher um ID](./time-series-insights-update-how-to-id.md)da Série De Tempo .
