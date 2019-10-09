---
title: Azure Time Series Insights Visualizar consulta de dados | Microsoft Docs
description: Azure Time Series Insights Visualizar a consulta de dados.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/07/2019
ms.custom: seodec18
ms.openlocfilehash: 7b7d041b678ccf2a476afc6d0744f6971349677e
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72034874"
---
# <a name="data-querying"></a>Consulta de dados

Azure Time Series Insights visualização permite consultar dados em eventos e metadados armazenados no ambiente por meio de APIs de superfície pública. Essas APIs também são usadas no [Gerenciador de visualização Time Series insights](./time-series-insights-update-explorer.md).

Três categorias de API principais estão disponíveis no Time Series Insights:

* **APIs de ambiente**: Habilita consultas do próprio ambiente de Time Series Insights. Exemplos de consultas são a lista de ambientes aos quais o chamador tem acesso e os metadados do ambiente.

* **APIs de consulta de modelo de série temporal (TSM-Q)** : Habilita operações de criação, leitura, atualização e exclusão em metadados armazenados na parte do ambiente do modelo de série temporal. Os exemplos são instâncias, tipos e hierarquias.

* **APIs de consulta de série temporal (TSQ)** : Permite a recuperação de dados de eventos conforme eles são registrados do provedor de origem. Essas APIs podem executar operações para transformar, combinar e executar cálculos em dados de série temporal.

A [linguagem TSX (expressão de série temporal)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx) é uma quarta categoria poderosa. Ele usa modelos de série temporal para habilitar a composição de computação avançada.

## <a name="azure-time-series-insights-preview-core-apis"></a>APIs principais do Azure Time Series Insights Preview

As APIs principais a seguir têm suporte.

[Visão geral da consulta da série @no__t 1Time](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>APIs de ambiente

As seguintes APIs de ambiente estão disponíveis:

* [Obter API do ambiente](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environments-api): Retorna a lista de ambientes aos quais o chamador está autorizado a acessar.
* [Obter API de disponibilidade do ambiente](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environment-availability-api): Retorna a distribuição da contagem de eventos sobre o carimbo de data/hora do evento `$ts`. Essa API ajuda a determinar se há algum evento no carimbo de data/hora retornando a contagem de eventos, se existir.
* [Obter API do esquema de evento](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-event-schema-api): Retorna os metadados do esquema de evento para um determinado intervalo de pesquisa. Essa API ajuda a recuperar todos os metadados e propriedades disponíveis no esquema para o intervalo de pesquisa fornecido.

## <a name="time-series-model-query-tsm-q-apis"></a>APIs de consulta de modelo de série temporal (TSM-Q)

As seguintes APIs de consulta de modelo de série temporal estão disponíveis:

* [API de configurações de modelo](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api): Habilita Get e patch no tipo padrão e no nome do modelo do ambiente.
* [API de tipos](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api): Habilita CRUD nos tipos de série temporal e suas variáveis associadas.
* [API de hierarquias](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api): Habilita CRUD nas hierarquias de série temporal e seus caminhos de campo associados.
* [API de instâncias](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api): Habilita CRUD em instâncias de série temporal e seus campos de instância associados.

## <a name="time-series-query-tsq-apis"></a>APIs de consulta de série temporal (TSQ)

As seguintes APIs de consulta de série temporal estão disponíveis:

* [API de obtenção de eventos](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-events-api): Permite a consulta e a recuperação de Time Series Insights dados de eventos conforme eles são gravados em Time Series Insights do provedor de origem.

* [Obter a API da série](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-series-api): Permite a consulta e a recuperação de dados de Time Series Insights de eventos capturados usando dados registrados na conexão. Os valores retornados são baseados nas variáveis que foram definidas no modelo ou embutidas em linha.

    >[!NOTE]
    > A cláusula de agregação é ignorada mesmo que seja especificada em um modelo ou fornecida em linha.

  A API obter série retorna um valor de série temporal para cada variável para cada intervalo. Um valor de série temporal é um formato que Time Series Insights usa para o JSON de saída de uma consulta. Os valores retornados são baseados na ID da série temporal e no conjunto de variáveis que foram fornecidas.

* [API da série agregada](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#aggregate-series-api): Permite a consulta e a recuperação de dados de Time Series Insights de eventos capturados por amostragem e agregação de dados gravados.

  A API da série agregada retorna um valor de série temporal para cada variável para cada intervalo. Os valores são baseados na ID da série temporal e no conjunto de variáveis que foram fornecidas. A API da série agregada alcança a redução usando variáveis armazenadas no modelo de série temporal ou fornecidas em linha para agregar ou dados de exemplo.

  Tipos de agregação com suporte: `Min`, `Max`, `Sum`, `Count`, `Average`

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [armazenamento e a entrada](./time-series-insights-update-storage-ingress.md) na versão prévia do Azure Time Series insights.

- Leia o artigo Time Series Insights Preview [Data Modeling](./time-series-insights-update-tsm.md) .

- Descubra [as práticas recomendadas ao escolher uma ID de série temporal](./time-series-insights-update-how-to-id.md).
