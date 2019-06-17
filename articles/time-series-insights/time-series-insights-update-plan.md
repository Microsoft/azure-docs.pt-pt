---
title: Planejar seu ambiente de pré-visualização do Azure Time Series Insights | Documentos da Microsoft
description: Planeje seu ambiente de pré-visualização do Azure Time Series Insights.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: 435e5f2163270672ac5f1f5695ca2fe9be22ee6b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66388747"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Planejar seu ambiente de pré-visualização do Azure Time Series Insights

Este artigo descreve as melhores práticas para planear e comece a trabalhar rapidamente utilizando a pré-visualização do Azure Time Series Insights.

> [!NOTE]
> Para melhores práticas planear uma instância do Time Series Insights de disponibilidade geral, consulte [planejar seu ambiente de disponibilidade geral do Azure Time Series Insights](time-series-insights-environment-planning.md).

## <a name="best-practices-for-planning-and-preparation"></a>Melhores práticas para o planejamento e preparação

Para começar a utilizar com o Time Series Insights, é melhor se compreender:

* O que fazer quando [aprovisionar um ambiente de pré-visualização do Time Series Insights](#the-preview-environment).
* O que sua [são propriedades de IDs de série de tempo e Timestamp](#configure-time-series-ids-and-timestamp-properties).
* Nova [modelo de série de tempo é](#understand-the-time-series-model)e como criar seus próprios.
* Como [enviar eventos com eficiência em JSON](#shape-your-events).
* Time Series Insights [opções de recuperação após desastre de negócio](#business-disaster-recovery).

O Azure Time Series Insights emprega um modelo de negócio pay as you go. Para obter mais informações sobre custos e a capacidade, consulte [preços de Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-preview-environment"></a>O ambiente de pré-visualização

Quando aprovisiona um ambiente de pré-visualização do Time Series Insights, cria dois recursos do Azure:

* Um ambiente de pré-visualização do Azure Time Series Insights
* Uma conta do armazenamento do Azure para fins gerais V1

Para começar, terá de três itens adicionais:

* A [o modelo de série de tempo](./time-series-insights-update-tsm.md)
* Um [origem de eventos ligado ao Time Series Insights](./time-series-insights-how-to-add-an-event-source-iothub.md)
* [Eventos a ser encaminhados para a origem do evento](./time-series-insights-send-events.md) que estão ambos mapeados para o modelo e estão no formato JSON válido

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Configurar propriedades de IDs de série de tempo e Timestamp

Para criar um novo ambiente do Time Series Insights, selecione uma série de tempo de ID. Ao fazê-lo então, atua como uma partição lógica para os seus dados. Como observado, lembre-se de que tem os seus IDs de série de tempo pronto.

> [!IMPORTANT]
> Os IDs de série de tempo são *imutável* e *não é possível alterar mais tarde*. Verifique se cada um deles antes de seleção final e utilizar pela primeira vez.

Pode selecionar até três chaves para distinguir exclusivamente os seus recursos. Para obter mais informações, leia [melhores práticas para a escolha de um ID de série de tempo](./time-series-insights-update-how-to-id.md) e [armazenamento e de entrada](./time-series-insights-update-storage-ingress.md).

A propriedade Timestamp também é importante. Pode designar esta propriedade quando adicionar origens de eventos. Cada origem de evento tem uma propriedade Timestamp opcional que utilizou para origens de eventos de controle ao longo do tempo. Timestamp valores são sensíveis a maiúsculas e minúsculas e têm de ser formatados a especificação individuais de cada origem de evento.

> [!TIP]
> Verifique se os requisitos de formatação e análise para origens de evento.

Quando deixado em branco, será utilizada a hora de colocar em fila de eventos de uma origem de evento que o evento Timestamp. Se tiver de enviar dados históricos ou eventos em lote, personalizando a propriedade Timestamp é mais útil à predefinição de tempo de colocar em fila de eventos. Para obter mais informações, leia sobre como [adicionar origens de eventos no IoT Hub do Azure](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="understand-the-time-series-model"></a>Compreender a série de tempo modelo

Agora pode configurar o modelo de série de tempo de seu ambiente de Time Series Insights. O novo modelo torna mais fácil localizar e analisar dados de IoT. Ele permite que a organização, manutenção e enriquecimento de dados de séries de tempo e ajuda a preparar conjuntos de dados de preparado para o consumidor. O modelo utiliza IDs de série de tempo, que mapeiam para uma instância que associa o recurso exclusivo de variáveis, conhecidas como tipos e hierarquias. Saiba mais sobre a nova [modelo de série de tempo](./time-series-insights-update-tsm.md).

O modelo é dinâmico, pelo que podem ser criado em qualquer altura. Para começar rapidamente a utilizar, crie e carregue-o antes de enviar por push dados para o Time Series Insights. Para criar o seu modelo, veja [utilizam o modelo de série de tempo](./time-series-insights-update-how-to-tsm.md).

Para muitos clientes, o modelo de série de tempo é mapeado para um modelo de recurso existente ou o sistema ERP já em vigor. Se não tiver um modelo existente, uma experiência de utilizador criados previamente é [fornecida](https://github.com/Microsoft/tsiclient) para começar a trabalhar rapidamente. Para prever como um modelo pode ajudá-lo, veja a [ambiente de demonstração de exemplo](https://insights.timeseries.azure.com/preview/demo).

## <a name="shape-your-events"></a>Moldar seus eventos

Pode verificar a maneira que eventos são enviados para o Time Series Insights. O ideal é que os eventos são desnormalizados bem e eficiente.

Uma boa regra prática:

* Store metadados no seu modelo de série de tempo.
* Modo de série de tempo, campos de instância e eventos incluem informações apenas necessárias, como um ID de série de tempo ou Timestamp.

Para obter mais informações, consulte [moldar eventos](./time-series-insights-send-events.md#json).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Passos Seguintes

- Leia mais sobre [armazenamento e de entrada](./time-series-insights-update-storage-ingress.md) na pré-visualização do Time Series Insights.
- Saiba mais sobre [modelação de dados](./time-series-insights-update-tsm.md) na pré-visualização do Time Series Insights.