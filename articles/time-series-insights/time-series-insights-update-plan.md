---
title: Planeie o seu ambiente de pré-visualização - Azure Time Series Insights / Microsoft Docs
description: As melhores práticas para configurar, gerir, planear e implementar o seu ambiente de visualização de insights de séries de tempo Azure.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/27/2020
ms.custom: seodec18
ms.openlocfilehash: b57a7c04db0e601b90bc19059df70e63795784bf
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86036955"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Planeie o seu ambiente de visualização de insights da série de tempo Azure

Este artigo descreve as melhores práticas para planear e começar rapidamente usando a Azure Time Series Insights Preview.

> [!NOTE]
> Para as melhores práticas para planear uma instância geral de insights de séries de tempo, leia [Plan your Azure Time Series Insights ambiente de disponibilidade geral](time-series-insights-environment-planning.md).

## <a name="best-practices-for-planning-and-preparation"></a>Melhores práticas de planeamento e preparação

As melhores práticas em torno do planeamento e preparação do seu ambiente são descritas ainda mais nos seguintes artigos:

* O que obtém quando [disponibiliza um ambiente de pré-visualização de insights de séries de tempo](#the-preview-environment).
* Quais são as suas [propriedades de Séries Tempotamos e Timestamp](#configure-time-series-ids-and-timestamp-properties).
* Qual é o novo [Modelo da Série Do Tempo](#understand-the-time-series-model)e como construir o seu próprio.
* Como [enviar eventos de forma eficiente em JSON.](#shape-your-events)
* Time Series Insights [opções de recuperação de desastres de negócios](#business-disaster-recovery).

A Azure Time Series Insights emprega um modelo de negócio pay-as-you-go. Para obter mais informações sobre encargos e capacidade, leia [os preços da Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-preview-environment"></a>O ambiente de pré-visualização

Ao fornecer um ambiente de pré-visualização de insights de séries de tempo, cria dois recursos Azure:

* Um ambiente de visualização de insights de séries de tempo Azure
* Uma conta de armazenamento Azure

Como parte do processo de provisionamento, especifica se deseja ativar uma loja quente. A loja warm proporciona-lhe uma experiência de consulta hierárquico. Quando ativado, deve especificar um período de retenção entre 7 e 30 dias. As consultas executadas dentro do período de retenção da loja quente geralmente proporcionam tempos de resposta mais rápidos. Quando uma consulta se estende durante o período de retenção quente da loja, é servida a partir de uma loja de frio.

As consultas na loja quente são gratuitas, enquanto as consultas sobre a loja de frio incorrem em custos. É importante compreender os seus padrões de consulta e planear a configuração da sua loja quente em conformidade. Recomendamos que a análise interativa dos dados mais recentes resida na sua loja quente e análise de padrões e tendências de longo prazo residem no frio.

> [!NOTE]
> Para ler mais sobre como consultar os seus dados quentes, leia a [Referência API.](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters)

Para começar, precisa de três itens adicionais:

* Um [modelo de série sonora](./concepts-model-overview.md)
* Uma [fonte de eventos ligada a Time Series Insights](./time-series-insights-how-to-add-an-event-source-iothub.md)
* [Eventos que fluem para a fonte do evento](./time-series-insights-send-events.md) que são ambos mapeados para o modelo e estão em formato JSON válido

## <a name="review-preview-limits"></a>Limites de pré-visualização de revisão

[!INCLUDE [Review Time Series Insights Preview limits](../../includes/time-series-insights-preview-limits.md)]

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Configurar iDs de séries de tempo e propriedades de timetamp

Para criar um novo ambiente de Insights de Séries Tempo, selecione um ID da Série De Tempo. Ao fazê-lo, funciona como uma divisória lógica para os seus dados. Como notado, certifique-se de ter os seus IDs de séries temporizadas prontos.

> [!IMPORTANT]
> Os IDs da Série De Tempo *não podem ser alterados mais tarde*. Verifique cada um antes da seleção final e primeira utilização.

Pode selecionar até três chaves para diferenciar exclusivamente os seus recursos. Para mais informações, leia [as melhores práticas para escolher as regras de ID](./time-series-insights-update-how-to-id.md) e [Ingestão](concepts-json-flattening-escaping-rules.md)da Série Tempo.

A propriedade **Timestamp** também é importante. Você pode designar esta propriedade quando adicionar fontes de evento. Cada fonte de evento tem uma propriedade opcional do Timestamp que é usada para rastrear fontes de eventos ao longo do tempo. Os valores do tempotamp são sensíveis à caixa e devem ser formatados à especificação individual de cada fonte de evento.

> [!TIP]
> Verifique os requisitos de formatação e análise das suas fontes de evento.

Quando deixada em branco, o Tempo de Enqueue do Evento de uma fonte de evento é usado como o timetamp do evento. Se enviar dados históricos ou eventos em lote, personalizar a propriedade Timestamp é mais útil do que o tempo de enquese do evento padrão. Para mais informações, leia sobre como [adicionar fontes de eventos no Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="understand-the-time-series-model"></a>Compreender o Modelo de Séries Tempores

Agora pode configurar o modelo da Série De Tempo do Ambiente da Série De Tempo do Time Series. O novo modelo facilita a procura e análise de dados IoT. Permite a cura, manutenção e enriquecimento de dados de séries de tempo e ajuda a preparar conjuntos de dados prontos para o consumidor. O modelo usa IDs de Séries De Tempo, que mapeiam para um caso que associa o recurso único com variáveis, conhecidas como tipos, e hierarquias. Leia sobre o novo [Modelo da Série Do Tempo](./concepts-model-overview.md).

O modelo é dinâmico, por isso pode ser construído a qualquer momento. Para começar rapidamente, construa-o e carreca-o antes de empurrar os dados para o Time Series Insights. Para construir o seu modelo, leia [Use o Modelo série de tempo](./time-series-insights-update-how-to-tsm.md).

Para muitos clientes, o Modelo série de tempo mapeia para um modelo de ativo existente ou sistema ERP já em vigor. Se não tiver um modelo existente, é [fornecida](https://github.com/Microsoft/tsiclient) uma experiência de utilizador pré-construída para se levantar e funcionar rapidamente. Para imaginar como um modelo pode ajudá-lo, veja o [ambiente de demonstração de amostras](https://insights.timeseries.azure.com/preview/demo).

## <a name="shape-your-events"></a>Moldar os seus eventos

Pode verificar a forma como envia eventos para a Time Series Insights. Idealmente, os seus eventos são desnormalizados bem e eficientemente.

Uma boa regra do polegar:

* Guarde metadados no seu modelo série de tempo.
* Certifique-se de que o Modo série de tempo, os campos de instância e os eventos incluem apenas informações necessárias, como uma propriedade time series iD ou timestamp.

Para mais informações, leia [eventos de forma](./time-series-insights-send-events.md#supported-json-shapes).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Próximos passos

- Reveja [o Azure Advisor](../advisor/advisor-overview.md) para planear as suas opções de configuração de recuperação de negócios.
- Leia mais sobre [a ingestão de dados.](./concepts-ingestion-overview.md)
- Rever o artigo sobre [armazenamento de dados](./concepts-storage.md)
- Saiba mais sobre [modelação de dados](./concepts-model-overview.md) na pré-visualização de Insights da Série Temporal.
