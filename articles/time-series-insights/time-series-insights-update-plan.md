---
title: Planeie o seu ambiente de pré-visualização - Azure Time Series Insights [ Microsoft Docs
description: As melhores práticas para configurar, gerir, planear e implementar o seu ambiente de pré-visualização da Série De Tempo Azure Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/27/2020
ms.custom: seodec18
ms.openlocfilehash: 4b61df52df45cb2ee01407390ce3e34d86350ef7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82189254"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Planeie o seu ambiente de pré-visualização da Série de Tempo Azure Insights

Este artigo descreve as melhores práticas para planear e começar rapidamente usando a Pré-visualização de Insights da Série De Tempo Azure.

> [!NOTE]
> Para as melhores práticas para planear uma disponibilidade geral Time Series Insights, leia [Plan your Azure Time Series Insights ambiente de disponibilidade geral](time-series-insights-environment-planning.md).

## <a name="best-practices-for-planning-and-preparation"></a>Boas práticas para planeamento e preparação

As melhores práticas em torno do planeamento e preparação do seu ambiente são descritas nos seguintes artigos:

* O que obtém quando oferece um ambiente de [pré-visualização](#the-preview-environment)de Insights da Série Tempo.
* Quais são as suas propriedades time [series e timestamp.](#configure-time-series-ids-and-timestamp-properties)
* O que é o novo Modelo da [Série Tempo](#understand-the-time-series-model)e como construir o seu próprio.
* Como [enviar eventos de forma eficiente na JSON.](#shape-your-events)
* Opções de recuperação de [desastres de negócio](#business-disaster-recovery)da Time Series Insights.

A Azure Time Series Insights emprega um modelo de negócio pay-as-you-go. Para obter mais informações sobre taxas e capacidade, leia os preços da [Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-preview-environment"></a>O ambiente de pré-visualização

Ao fornecer um ambiente de pré-visualização de Insights da Série De Tempo, cria dois recursos Azure:

* Um ambiente de pré-visualização da série de tempo azure Insights
* Uma conta V1 de uso geral de armazenamento azure

Como parte do processo de provisionamento, especifica se pretende ativar uma loja quente. A loja warm proporciona-lhe uma experiência de consulta hierárquica. Quando ativado, deve especificar um período de retenção entre 7 e 30 dias. As consultas executadas dentro do período de retenção de lojas quentes geralmente fornecem tempos de resposta mais rápidos. Quando uma consulta se estende durante o período de retenção de lojas quentes, é servida a partir de uma loja fria.

As consultas na loja quente são gratuitas, enquanto as consultas na loja de frio incorrem em custos. É importante compreender os seus padrões de consulta e planear a configuração da loja quente em conformidade. Recomendamos que a análise interativa dos dados mais recentes resida na sua loja quente e a análise de padrões e tendências a longo prazo residem no frio.

> [!NOTE]
> Para ler mais sobre como consultar os seus dados quentes, leia a [Referência API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters).

Para começar, precisa de três itens adicionais:

* Um [modelo de série de tempo](./time-series-insights-update-tsm.md)
* Uma [fonte de evento ligada a Time Series Insights](./time-series-insights-how-to-add-an-event-source-iothub.md)
* [Eventos que fluem para a fonte do evento](./time-series-insights-send-events.md) que estão ambos mapeados para o modelo e estão em formato JSON válido

## <a name="review-preview-limits"></a>Limites de pré-visualização de revisão

[!INCLUDE [Review Time Series Insights Preview limits](../../includes/time-series-insights-preview-limits.md)]

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Configure propriedades de IDs e Timestamp da Série de Tempo

Para criar um novo ambiente time series Insights, selecione um ID da Série De Tempo. Fazê-lo funciona como uma partição lógica para os seus dados. Como notado, certifique-se de ter as suas identificações da Série De Tempo prontas.

> [!IMPORTANT]
> Os IDs da Série De Tempo *não podem ser alterados mais tarde.* Verifique cada um antes da seleção final e primeira utilização.

Pode selecionar até três chaves para diferenciar os seus recursos de forma única. Para mais informações, leia [as melhores práticas para escolher um ID](./time-series-insights-update-how-to-id.md) e Armazenamento da Série De Tempo e [ingresso](./time-series-insights-update-storage-ingress.md).

A propriedade **Timestamp** também é importante. Você pode designar esta propriedade quando adicionar fontes de evento. Cada fonte de evento tem uma propriedade opcional timestamp que é usada para rastrear fontes de eventos ao longo do tempo. Os valores da marca tempora são sensíveis ao caso e devem ser formatados à especificação individual de cada fonte do evento.

> [!TIP]
> Verifique os requisitos de formatação e análise para as suas fontes de evento.

Quando deixada em branco, o Tempo de Enfila do Evento de uma fonte de evento é usado como carimbo de tempo do evento. Se enviar dados históricos ou eventos em lotes, personalizar a propriedade timestamp é mais útil do que o tempo padrão do Evento Enqueue. Para mais informações, leia sobre como adicionar fontes de [eventos no Hub Azure IoT](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="understand-the-time-series-model"></a>Compreender o Modelo da Série de Tempo

Agora pode configurar o modelo da série time insights do ambiente. O novo modelo facilita a descoberta e análise de dados ioT. Permite a curadoria, manutenção e enriquecimento de dados da série temporal e ajuda a preparar conjuntos de dados prontos para o consumidor. O modelo utiliza IDs da Série do Tempo, que mapeiam para uma instância que associa o recurso único a variáveis, conhecidas como tipos, e hierarquias. Leia sobre o novo Modelo da [Série Tempo.](./time-series-insights-update-tsm.md)

O modelo é dinâmico, por isso pode ser construído a qualquer momento. Para começar rapidamente, construa-os e carregue-os antes de empurrar os dados para a Time Series Insights. Para construir o seu modelo, leia [Use o Modelo](./time-series-insights-update-how-to-tsm.md)série de tempo .

Para muitos clientes, o Modelo série de tempo mapeia um modelo de ativos existente ou sistema ERP já em vigor. Se não tiver um modelo existente, é [fornecida](https://github.com/Microsoft/tsiclient) uma experiência de utilizador pré-construída para se levantar e funcionar rapidamente. Para visualizar como um modelo pode ajudá-lo, veja o ambiente de [demonstração](https://insights.timeseries.azure.com/preview/demo)de amostras.

## <a name="shape-your-events"></a>Moldar os seus eventos

Pode verificar a forma como envia eventos para time series Insights. Idealmente, os seus eventos são desnormalizados de forma bem e eficiente.

Uma boa regra do polegar:

* Guarde metadados no seu Modelo de Série Seletiva.
* Certifique-se de que o Modo de Série de Tempo, campos de exemplo e eventos incluem apenas informações necessárias, tais como uma propriedade id da Série de Tempo ou timestamp.

Para mais informações, leia [os eventos shape](./time-series-insights-send-events.md#supported-json-shapes).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Passos seguintes

- Reveja o [Azure Advisor](../advisor/advisor-overview.md) para planear as suas opções de configuração de recuperação de negócios.
- Leia mais sobre [armazenamento e ingresso](./time-series-insights-update-storage-ingress.md) na Pré-visualização da Série De Tempo Insights.
- Saiba mais sobre [modelação](./time-series-insights-update-tsm.md) de dados na Pré-visualização de Insights da Série De Tempo.
