---
title: Consulta de dados pré-visualização do Time Series Insights do Azure | Documentos da Microsoft
description: Consultar um dados de pré-visualização do Time Series Insights do Azure.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 05/06/2019
ms.custom: seodec18
ms.openlocfilehash: bbf682df2df7a8cdc9fedb36aa4244fc5c0e9488
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66244008"
---
# <a name="data-querying"></a>Consulta de dados

Pré-visualização de informações de série de tempo do Azure permite a consultas de dados em eventos e metadados armazenados no ambiente através de APIs de superfície públicas. Essas APIs também são usadas no [Explorador de pré-visualização do Time Series Insights](./time-series-insights-update-explorer.md).

Três categorias principais de API estão disponíveis no Time Series Insights:

* **APIs de ambiente**: Permite que as consultas do ambiente do Time Series Insights em si. Exemplos de consultas são a lista de ambientes, que o chamador tem acesso ao e metadados de ambiente.

* **APIs de modelo-Query (TSM-Q) de série de tempo**: Permite cria, ler, atualizar e eliminar operações em metadados armazenados na parte do ambiente do modelo de série de tempo. Os exemplos são instâncias, tipos e hierarquias.

* **Série de tempo de consulta (TSQ) APIs**: Permite a obtenção de dados de eventos como ele será gravado do fornecedor de origem. Essas APIs podem executar operações de transformar, combinar e efetuar cálculos nos dados de séries de tempo.

O [linguagem de expressão de série de tempo (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx) é uma quarta categoria poderosas. Utiliza modelos de série de tempo para habilitar a composição de computação avançada.

## <a name="azure-time-series-insights-preview-core-apis"></a>APIs principais do Time Series Insights pré-visualização do Azure

As seguintes principais que APIs são suportadas.

[![Descrição geral de consulta de série de tempo](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>APIs de ambiente

As APIs de ambiente seguintes estão disponíveis:

* [Obter ambiente API](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environments-api): Devolve a lista de ambientes de que o chamador está autorizado a aceder.
* [Obter ambiente disponibilidade API](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environment-availability-api): Devolve a distribuição de contagem de eventos durante o período de tempo do evento `$ts`. Esta API ajuda a determinar se existem quaisquer eventos o carimbo de hora ao devolver a contagem de eventos, se existir alguma.
* [Obter esquema de eventos API](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-event-schema-api): Devolve os metadados de esquema de eventos para um intervalo de pesquisa especificada. Esta API ajuda a recuperar todos os metadados e propriedades disponíveis no esquema do intervalo de pesquisa especificada.

## <a name="time-series-model-query-tsm-q-apis"></a>APIs de modelo-Query (TSM-Q) de série de tempo

As APIs de consulta do modelo de série de tempo seguintes estão disponíveis:

* [API de definições de modelo](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api): Permite obter e aplicar um patch no tipo de padrão e o nome do modelo do ambiente.
* [Tipos de API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api): Permite CRUD em tipos de séries de tempo e as respetivas variáveis associadas.
* [API de hierarquias](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api): Ativa o CRUD em hierarquias de séries de tempo e seus caminhos de campo associados.
* [API de instâncias](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api): Permite CRUD em instâncias de série de tempo e seus campos de instância associada.

## <a name="time-series-query-tsq-apis"></a>APIs de consulta (TSQ) de série de tempo

As APIs de consulta de série de tempo seguintes estão disponíveis:

* [Obter eventos API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-events-api): Permite a consulta e a obtenção de dados do Time Series Insights de eventos à medida que são registradas no Time Series Insights do fornecedor de origem.

* [Obter série API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-series-api): Consulta ativa e a obtenção de dados de Time Series Insights dos eventos capturados através da utilização de dados registados na conexão. Os valores que são devolvidos baseiam-se as variáveis que foram definidas no modelo ou fornecidas inline.

    >[!NOTE]
    > A cláusula de agregação é ignorada, mesmo caso de especificado num modelo ou fornecido inline.

  A API de série obter devolve um valor de séries de tempo para cada variável, para cada intervalo. Um valor de série de tempo é um formato que utiliza o Time Series Insights para a saída JSON de uma consulta. Os valores que são devolvidos baseiam-se o ID de série de tempo e o conjunto de variáveis que foram fornecidos.

* [Agregar série API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#aggregate-series-api): Consulta ativa e obtenção de dados de Time Series Insights dos eventos capturados por amostragem e agregar registados dados.

  A API de série agregado devolve um valor de séries de tempo para cada variável, para cada intervalo. Os valores baseiam-se o ID de série de tempo e o conjunto de variáveis que foram fornecidos. A API de série agregado alcança redução usando variáveis armazenada no modelo de série de tempo ou fornecida inline para dados de agregação ou de exemplo.

  Tipos de agregação suportados: `Min`, `Max`, `Sum`, `Count`, `Average`

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [armazenamento e de entrada](./time-series-insights-update-storage-ingress.md) na pré-visualização do Azure Time Series Insights.

- Leia a pré-visualização do Time Series Insights [modelação de dados](./time-series-insights-update-tsm.md) artigo.

- Descubra [melhores práticas ao escolher um ID de série de tempo](./time-series-insights-update-how-to-id.md).
