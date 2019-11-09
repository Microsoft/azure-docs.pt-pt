---
title: Azure Time Series Insights Visualizar consulta de dados | Microsoft Docs
description: Azure Time Series Insights Visualizar a consulta de dados.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/21/2019
ms.custom: seodec18
ms.openlocfilehash: e660db5db3d1afc14a3c895e6786d1b6a8b82c13
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73832413"
---
# <a name="data-querying"></a>Consulta de dados

Azure Time Series Insights visualização permite consultar dados em eventos e metadados armazenados no ambiente por meio de APIs de superfície pública. Essas APIs também são usadas no [Gerenciador de visualização Time Series insights](./time-series-insights-update-explorer.md).

Três categorias de API principais estão disponíveis no Time Series Insights:

* **APIs de ambiente**: essas APIs habilitam consultas no próprio ambiente de time Series insights. Exemplos de consultas são a lista de ambientes aos quais o chamador tem acesso e os metadados do ambiente.
* **APIs de consulta de modelo de série temporal (TSM-Q)** : habilita operações de criação, leitura, atualização e exclusão (CRUD) em metadados armazenados na parte do ambiente do modelo de série temporal. Os exemplos são instâncias, tipos e hierarquias.
* **APIs de consulta de série temporal (TSQ)** : habilita a recuperação de dados de telemetria ou eventos conforme eles são gravados do provedor de origem ou pela redução dos dados usando funções escalares e de agregação armazenadas parte das variáveis. Essas APIs podem executar operações para transformar, combinar e aplicar cálculos em dados de série temporal.

Time Series Insights usa uma linguagem de expressão baseada em cadeia de caracteres avançada, [TSX (expressão de série temporal)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx), para expressar cálculos.

## <a name="azure-time-series-insights-preview-core-apis"></a>APIs principais do Azure Time Series Insights Preview

As APIs principais a seguir têm suporte.

[Visão geral de consulta de série temporal ![](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>APIs de ambiente

As seguintes APIs de ambiente estão disponíveis:

* [Obter API do ambiente](/rest/api/time-series-insights/management/environments/get): retorna a lista de ambientes aos quais o chamador está autorizado a acessar.
* [Obter API de disponibilidade do ambiente](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability): retorna a distribuição da contagem de eventos sobre o `$ts`do carimbo de data/hora do evento. Essa API ajuda a determinar se há algum evento no carimbo de data/hora retornando a contagem de eventos, se existir.
* [Obter API do esquema de evento](/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema): retorna os metadados do esquema de evento para um determinado intervalo de pesquisa. Essa API ajuda a recuperar todos os metadados e propriedades disponíveis no esquema para o intervalo de pesquisa fornecido.

## <a name="time-series-model-query-tsm-q-apis"></a>APIs de consulta de modelo de série temporal (TSM-Q)

As seguintes APIs de consulta de modelo de série temporal estão disponíveis. A maioria dessas APIs dá suporte à operação de execução em lote para habilitar operações CRUD de lote em várias entidades de modelo de série temporal:

* [API de configurações de modelo](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api): habilita *Get* e *patch* no tipo padrão e o nome do modelo do ambiente.
* [API de tipos](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api): habilita CRUD em tipos de série temporal e suas variáveis associadas.
* [API de hierarquias](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api): habilita CRUD em hierarquias de série temporal e seus caminhos de campo associados.
* [API de instâncias](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api): habilita CRUD em instâncias de série temporal e seus campos de instância associados. Além disso, a API de instâncias do oferece suporte às seguintes operações:
  * [Pesquisa](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/search): recupera uma lista parcial de ocorrências na pesquisa de instâncias de série temporal com base em atributos de instância.
  * [Sugerir](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/suggest): pesquisa e sugere uma lista parcial de ocorrências na pesquisa de instâncias de série temporal com base em atributos de instância.

## <a name="time-series-query-tsq-apis"></a>APIs de consulta de série temporal (TSQ)

As seguintes APIs de consulta de série temporal estão disponíveis. Essas APIs estão disponíveis em todos os armazenamentos em várias camadas com suporte no Time Series Insights. Os parâmetros de URL de consulta são usados para especificar o [tipo de repositório](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters) em que a consulta deve ser executada:

* [API Get Events](/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents): habilita a consulta e a recuperação de dados de time Series insights de eventos conforme eles são gravados em time Series insights do provedor de origem. Essa API permite a recuperação de eventos brutos para uma determinada ID de série temporal e intervalo de pesquisa. Essa API dá suporte à paginação para recuperar o conjunto de dados completo da entrada selecionada. 

* [Obter a API da série](/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries): habilita a consulta e a recuperação de dados de time Series insights de eventos capturados usando dados registrados na conexão. Os valores retornados são baseados nas variáveis que foram definidas no modelo ou embutidas em linha. Essa API dá suporte à paginação para recuperar o conjunto de dados completo da entrada selecionada. Essa API ajuda a definir propriedades ou colunas calculadas.

    >[!NOTE]
    > A cláusula de agregação é ignorada mesmo que seja especificada em um modelo ou fornecida em linha.

  A API obter série retorna um valor de série temporal para cada variável para cada intervalo. Um valor de série temporal é um formato que Time Series Insights usa para o JSON de saída de uma consulta. Os valores retornados são baseados na ID da série temporal e no conjunto de variáveis que foram fornecidas.

* [API da série de agregação](/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregatevariable): habilita a consulta e a recuperação de dados de time Series insights de eventos capturados por amostragem e agregação de dados gravados. Essa API dá suporte à execução de continuável usando [tokens de continuação](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#queryresultpage).

  A API da série agregada retorna um valor de série temporal para cada variável para cada intervalo. Os valores são baseados na ID da série temporal e no conjunto de variáveis que foram fornecidas. A API da série agregada alcança a redução usando variáveis armazenadas no modelo de série temporal ou fornecidas em linha para agregar ou dados de exemplo.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [armazenamento e a entrada](./time-series-insights-update-storage-ingress.md) na versão prévia do Azure Time Series insights.
- Leia o artigo Time Series Insights Preview [Data Modeling](./time-series-insights-update-tsm.md) .
- Descubra [as práticas recomendadas para escolher uma ID de série temporal](./time-series-insights-update-how-to-id.md).
