---
title: Planeie o seu ambiente Gen2 - Azure Time Series Insights | Microsoft Docs
description: As melhores práticas para configurar, gerir, planear e implementar o seu ambiente Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: c8d96d7b5952c020493af278ee1ea8ad5ff46716
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95016790"
---
# <a name="plan-your-azure-time-series-insights-gen2-environment"></a>Planeie o seu ambiente Azure Time Series Insights Gen2

Este artigo descreve as melhores práticas para planear e começar rapidamente usando a Azure Time Series Insights Gen2.

## <a name="best-practices-for-planning-and-preparation"></a>Melhores práticas de planeamento e preparação

As melhores práticas em torno do planeamento e preparação do seu ambiente são descritas ainda mais nos seguintes artigos:

* O que obtém quando [se disponibiliza um ambiente Azure Time Series Insights Gen2](#the-gen2-environment).
* Quais são as suas [propriedades de Séries Tempotamos e Timestamp](#configure-time-series-ids-and-timestamp-properties).
* Qual é o novo [Modelo da Série Do Tempo](#understand-the-time-series-model)e como construir o seu próprio.
* Como [enviar eventos de forma eficiente em JSON.](#shape-your-events)
* Azure Time Series Insights [opções de recuperação de desastres de negócios](#business-disaster-recovery).

A Azure Time Series Insights emprega um modelo de negócio pay-as-you-go. Para obter mais informações sobre encargos e capacidade, leia [os preços da Azure Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-gen2-environment"></a>O ambiente Gen2

Ao fornecer um ambiente Azure Time Series Insights Gen2, cria-se dois recursos Azure:

* Um ambiente Azure Time Series Insights Gen2
* Uma conta de armazenamento Azure

Como parte do processo de provisionamento, especifica se deseja ativar uma loja quente. A loja warm proporciona-lhe uma experiência de consulta hierárquico. Quando ativado, deve especificar um período de retenção entre 7 e 30 dias. As consultas executadas dentro do período de retenção da loja quente geralmente proporcionam tempos de resposta mais rápidos. Quando uma consulta se estende durante o período de retenção quente da loja, é servida a partir de uma loja de frio.

As consultas na loja quente são gratuitas, enquanto as consultas sobre a loja de frio incorrem em custos. É importante compreender os seus padrões de consulta e planear a configuração da sua loja quente em conformidade. Recomendamos que a análise interativa dos dados mais recentes resida na sua loja quente e análise de padrões e tendências de longo prazo residem no frio.

> [!NOTE]
> Para ler mais sobre como consultar os seus dados quentes, leia a [Referência API.](/rest/api/time-series-insights/dataaccessgen2/query/execute#uri-parameters)

Para começar, precisa de três itens adicionais:

* Um [modelo de série sonora](./concepts-model-overview.md)
* Uma [fonte de eventos ligada a Time Series Insights](./concepts-streaming-ingestion-event-sources.md)
* [Eventos que fluem para a fonte do evento](./time-series-insights-send-events.md) que são ambos mapeados para o modelo e estão em formato JSON válido

## <a name="review-azure-time-series-insights-gen2-limits"></a>Rever os limites da Azure Time Series Insights Gen2

[!INCLUDE [Review Azure Time Series Insights Gen2 limits](../../includes/time-series-insights-preview-limits.md)]

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Configurar iDs de séries de tempo e propriedades de timetamp

Para criar um novo ambiente Azure Time Series Insights, selecione um ID da Série De Tempo. Ao fazê-lo, funciona como uma divisória lógica para os seus dados. Como notado, certifique-se de ter os seus IDs de séries temporizadas prontos.

> [!IMPORTANT]
> Os IDs da Série De Tempo *não podem ser alterados mais tarde*. Verifique cada um antes da seleção final e primeira utilização.

Pode selecionar até três chaves para diferenciar exclusivamente os seus recursos. Para mais informações, leia [as melhores práticas para escolher as regras de ID](./how-to-select-tsid.md) e [Ingestão](concepts-json-flattening-escaping-rules.md)da Série Tempo.

A propriedade **Timestamp** também é importante. Você pode designar esta propriedade quando adicionar fontes de evento. Cada fonte de evento tem uma propriedade opcional do Timestamp que é usada para rastrear fontes de eventos ao longo do tempo. Os valores do tempotamp são sensíveis à caixa e devem ser formatados à especificação individual de cada fonte de evento.

Quando deixado em branco, o momento em que o evento foi encostado no IoT Hub ou No Event Hub é usado como o timetamp do evento. Em geral, os utilizadores devem optar por personalizar a propriedade Timestamp e utilizar o tempo em que o sensor ou tag gerou a leitura, em vez do tempo do hub. Para mais informações e para ler sobre as compensações do fuso horário leia [o tempo de tempo da fonte do evento](./concepts-streaming-ingestion-event-sources.md#event-source-timestamp).

## <a name="understand-the-time-series-model"></a>Compreender o Modelo de Séries Tempores

Agora pode configurar o modelo da Série De Tempo do Azure. O novo modelo facilita a procura e análise de dados IoT. Permite a cura, manutenção e enriquecimento de dados de séries de tempo e ajuda a preparar conjuntos de dados prontos para o consumidor. O modelo usa IDs de Séries De Tempo, que mapeiam para um caso que associa o recurso único com variáveis, conhecidas como tipos, e hierarquias. Leia sobre a visão geral do [Modelo série](./concepts-model-overview.md) de tempo para saber mais.

O modelo é dinâmico, por isso pode ser construído a qualquer momento. Para começar rapidamente, construa-o e carrete-o antes de empurrar os dados para a Azure Time Series Insights. Para construir o seu modelo, leia [Use o Modelo série de tempo](./concepts-model-overview.md).

Para muitos clientes, o Modelo série de tempo mapeia para um modelo de ativo existente ou sistema ERP já em vigor. Se não tiver um modelo existente, é [fornecida](https://github.com/Microsoft/tsiclient) uma experiência de utilizador pré-construída para se levantar e funcionar rapidamente. Para imaginar como um modelo pode ajudá-lo, veja o [ambiente de demonstração de amostras](https://insights.timeseries.azure.com/preview/demo).

## <a name="shape-your-events"></a>Moldar os seus eventos

Pode verificar a forma como envia eventos para a Azure Time Series Insights. Idealmente, os seus eventos são desnormalizados bem e eficientemente.

Uma boa regra do polegar:

* Guarde metadados no seu modelo série de tempo.
* Certifique-se de que o Modo série de tempo, os campos de instância e os eventos incluem apenas informações necessárias, como uma propriedade time series iD ou timestamp.

Para obter mais informações e para compreender como os eventos serão achatados e armazenados, leia as regras de [achatamento e fuga do JSON.](./concepts-json-flattening-escaping-rules.md)

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Passos seguintes

* Reveja [o Azure Advisor](../advisor/advisor-overview.md) para planear as suas opções de configuração de recuperação de negócios.
* Reveja [o Azure Advisor](../advisor/advisor-overview.md) para planear as suas opções de configuração de recuperação de negócios.
* Leia mais sobre [a ingestão de dados](./concepts-ingestion-overview.md) na Azure Time Series Insights Gen2.
* Reveja o artigo sobre [armazenamento de dados](./concepts-storage.md) na Azure Time Series Insights Gen2.
* Saiba mais sobre [a modelação de dados](./concepts-model-overview.md) na Azure Time Series Insights Gen2.