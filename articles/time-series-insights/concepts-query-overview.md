---
title: Dados de Consulta - Azure Time Series Insights Gen2 | Microsoft Docs
description: Conceitos de consulta de dados e visão geral da API REST em Azure Time Series Insights Gen2.
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/22/2021
ms.custom: seodec18
ms.openlocfilehash: b1b055fa7f083bd8bccda16498e2894d5d67eace
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374138"
---
# <a name="querying-data-from-azure-time-series-insights-gen2"></a>Consultar dados no Azure Time Series Insights Gen2

Azure Time Series Insights Gen2 permite a consulta de dados sobre eventos e metadados armazenados no ambiente através de APIs de superfície pública. Estas APIs também são utilizadas pelo [Azure Time Series Insights TSI Explorer](./concepts-ux-panels.md).

Três categorias primárias de API estão disponíveis no Azure Time Series Insights Gen2:

* **APIs ambiente**: Estas APIs permitem consultas sobre o ambiente da Azure Time Series Insights Gen2. Estes podem ser usados para recolher a lista de ambientes a que o chamador tem acesso e metadados ambientais.
* **Séries de tempo Model-Query (TSM-Q) APIs**: Permite criar, ler, atualizar e eliminar (CRUD) operações em metadados armazenados no Modelo da Série De Tempo do ambiente. Estes podem ser usados para aceder e editar os casos, tipos e hierarquias.
* **APIs de Séries de Tempo (TSQ):** Permite a recuperação de dados de telemetria ou eventos tal como é registado a partir do fornecedor de origem e permite cálculos e agregações performativas nos dados utilizando funções avançadas de escalar e agregação.

Azure Time Series Insights Gen2 usa uma rica linguagem de expressão baseada em cordas, [Expressão de Séries De Tempo (TSX)](/rest/api/time-series-insights/reference-time-series-expression-syntax), para expressar cálculos em [Variáveis séries de tempo](./concepts-variables.md).

## <a name="azure-time-series-insights-gen2-apis-overview"></a>Azure Time Series Insights Gen2 APIs visão geral

As APIs do núcleo seguinte são suportadas.

[![Visão geral da consulta da série de tempo](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>APIs ambientais

* [Obter Ambientes API](/rest/api/time-series-insights/management(gen1/gen2)/accesspolicies/listbyenvironment): Devolve a lista de ambientes a que o ouvinte está autorizado a aceder.
* [Obter Ambientes Disponibilidade API](/rest/api/time-series-insights/dataaccessgen2/query/getavailability): Devolve a distribuição da contagem de eventos sobre o tempo do `$ts` evento. Esta API ajuda a determinar se existem eventos no ambiente, devolvendo a contagem de eventos quebrados em intervalos de tempo, se houver algum.
* [Obtenha o Evento Schema API](/rest/api/time-series-insights/dataaccessgen2/query/geteventschema): Devolve os metadados de esquema de evento para um determinado período de pesquisa. Esta API ajuda a recuperar todos os metadados e propriedades disponíveis no esquema para o período de pesquisa dado.

## <a name="time-series-model-query-tsm-q-apis"></a>Model-Query ApIs da Série De Tempo (TSM-Q)

A maioria destas APIs suportam a operação de execução de lotes para permitir operações CRUD em várias entidades do Modelo série de tempo:

* [Definições de modelo API](/rest/api/time-series-insights/reference-model-apis): Ativa *o GET* e o *PATCH* no tipo predefinido e no nome do modelo do ambiente.
* [Tipos API](/rest/api/time-series-insights/reference-model-apis#types-api): Permite CRUD nos tipos de Séries Temporizadas e nas suas variáveis associadas.
* [Hierarquias API](/rest/api/time-series-insights/reference-model-apis#hierarchies-api): Permite CRUD nas hierarquias da Série Temporal e seus caminhos de campo associados.
* [Instâncias API](/rest/api/time-series-insights/reference-model-apis#instances-api): Permite CRUD em instâncias de Séries Temporizadas e nos seus campos de instâncias associados. Além disso, a API de Instâncias suporta as seguintes operações:
  * [Pesquisa](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/search): Recupera uma lista parcial de acessos em casos de séries de tempo com base em atributos de exemplo.
  * [Sugestione:](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/suggest)Pesquisa e sugere uma lista parcial de acessos em casos de séries de tempo com base em atributos de instância.

## <a name="time-series-query-tsq-apis"></a>Consultas de Séries De Tempo (TSQ) APIs

Estas APIs estão disponíveis em ambas as lojas (Quente e Fria) na nossa solução de armazenamento multicamadas. 

* [Get Events API](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents): Permite consulta e recuperação de eventos crus e os relógios de eventos associados à medida que são gravados em Azure Time Series Insights Gen2 do fornecedor de origem. Esta API permite a recuperação de eventos crus para um determinado ID da Série de Tempo e período de pesquisa. Esta API suporta a paginação para recuperar o conjunto completo de dados de resposta para a entrada selecionada.

  > [!IMPORTANT]
  > Como parte das [próximas alterações às regras de achatamento e fuga de JSON, as](./ingestion-rules-update.md)matrizes serão armazenadas como tipo **Dinâmico.** As propriedades de carga útil armazenadas como este tipo são **apenas acessíveis através da API Get Events**.

* [Obter API série](/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries): Permite consulta e recuperação de valores calculados e dos timetamps de eventos associados, aplicando cálculos definidos por variáveis em eventos brutos. Estas variáveis podem ser definidas no Modelo de Séries Tempotamos ou em linha na consulta. Esta API suporta a paginação para recuperar o conjunto completo de dados de resposta para a entrada selecionada.

* [Agregação Série API](/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries): Permite consulta e recuperação de valores agregados e dos intervalos de tempo associados, aplicando cálculos definidos por variáveis em eventos brutos. Estas variáveis podem ser definidas no Modelo de Séries Tempotamos ou em linha na consulta. Esta API suporta a paginação para recuperar o conjunto completo de dados de resposta para a entrada selecionada.
  
  Para um intervalo de pesquisa especificado, esta API devolve uma resposta agregada por intervalo por variável para um ID da Série de Tempo. O número de intervalos no conjunto de dados de resposta é calculado contando carrapatos de época (o número de milissegundos que decorreram desde a época Unix - 1 de janeiro de 1970) e dividindo os carrapatos pelo tamanho do intervalo especificado na consulta.

  Os tempos devolvidos no conjunto de resposta são dos limites do intervalo esquerdo, não dos eventos amostrados do intervalo.


### <a name="selecting-store-type"></a>Selecionando tipo de loja

As APIs acima só podem ser executadas contra um dos dois tipos de armazenamento (Frio ou Quente) numa única chamada. Os parâmetros URL de consulta são utilizados para especificar o [tipo de loja](/rest/api/time-series-insights/dataaccessgen2/query/execute#uri-parameters) em que a consulta deve ser executada. 

Se não for especificado nenhum parâmetro, a consulta será executada na Cold Store, por padrão. Se uma consulta abranger um intervalo de tempo sobreposto tanto a Loja Cold como a Warm, é aconselhável encaminhar a consulta para o Cold store para obter a melhor experiência, uma vez que a loja Warm apenas conterá dados parciais. 

O [Azure Time Series Insights Explorer](./concepts-ux-panels.md) e o Power BI [Connector](./how-to-connect-power-bi.md) fazem chamadas para as APIs acima e selecionarão automaticamente o parâmetro de loja correto, se for caso disso. 


## <a name="next-steps"></a>Passos seguintes

* Leia mais sobre diferentes variáveis que podem ser definidas no [Modelo série de tempo.](./concepts-model-overview.md)
* Leia mais sobre como consultar dados do [Azure Time Series Insights Explorer](./concepts-ux-panels.md).
