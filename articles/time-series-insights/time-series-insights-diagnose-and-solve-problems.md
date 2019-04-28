---
title: Diagnosticar, resolução de problemas e resolver problemas no Azure Time Series Insights | Documentos da Microsoft
description: Este artigo descreve como diagnosticar, solucionar problemas e resolver problemas comuns que poderá encontrar no seu ambiente do Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 04/09/2018
ms.custom: seodec18
ms.openlocfilehash: ad739041ebd20f9940e305efb19807df4c73cb8e
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2019
ms.locfileid: "63759723"
---
# <a name="diagnose-and-solve-issues-in-your-time-series-insights-environment"></a>Diagnosticar e resolver problemas no seu ambiente do Time Series Insights

Este artigo descreve alguns problemas que poderá encontrar no seu ambiente do Azure Time Series Insights. O artigo oferece possíveis causas e soluções para a resolução.

## <a name="video"></a>Vídeo

### <a name="in-this-video-we-cover-common-time-series-insights-customer-challenges-and-mitigationsbr"></a>Neste vídeo, vamos abordar os desafios de cliente do Time Series Insights e atenuações comuns:</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-one-no-data-is-shown"></a>Problema um: não existem dados são apresentados

Sem dados na [Explorador do Azure Time Series Insights](https://insights.timeseries.azure.com) poderão ocorrer por diversos motivos comuns:

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>Dados de origem de evento de r: causa não estão no formato JSON

O Azure Time Series Insights suporta apenas os dados JSON. Para exemplos de JSON, veja [formas JSON suportadas](./how-to-shape-query-json.md).

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>Causa b: a chave de origem do evento está em falta uma permissão necessária

* Para um hub IoT no IoT Hub do Azure, tem de fornecer a chave que tenha **ligação de serviço** permissões. Qualquer um da **iothubowner** ou **service** políticas funcionam porque os dois têm **serviço ligar** permissões.

   ![Permissões de ligação do serviço IoT Hub](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)

* Para um hub de eventos nos Hubs de eventos do Azure, tem de fornecer a chave que tenha **escutar** permissões. Qualquer um da **ler** ou **gerir** políticas funcionam porque os dois têm **escutar** permissões.

   ![Permissões de escuta do hub de eventos](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)

### <a name="cause-c-the-consumer-group-provided-isnt-exclusive-to-time-series-insights"></a>Causa c: o grupo de consumidores fornecido não é exclusivo para o Time Series Insights

Quando se registra um hub IoT ou de um hub de eventos, é importante definir o grupo de consumidores que pretende utilizar para ler os dados. Este grupo de consumidores *não é possível partilhar*. Se o grupo de consumidores for partilhado, o hub de IoT subjacente ou o hub de eventos aleatoriamente e automaticamente desliga um dos leitores. Forneça um grupo de consumidores exclusivo para ler a partir do Time Series Insights.

## <a name="problem-two-some-data-is-shown-but-data-is-missing"></a>Problema dois: alguns dados são apresentados, mas está em falta

Quando dados são apresentados apenas parcialmente e são apresentados os dados ser lagging, deve considerar várias possibilidades.

### <a name="cause-a-your-environment-is-being-throttled"></a>Fazer com que está a ser limitado a seu ambiente de r:

A limitação é um problema comum quando os ambientes são aprovisionados depois de criar uma origem de evento que tem dados. Os Hubs de eventos do Azure e o IoT Hub do Azure armazenam dados durante sete dias. O Time Series Insights sempre começar com o evento mais antigo do evento origem (first in, First Out, ou *FIFO*).

Por exemplo, se tiver 5 milhões de eventos numa origem de evento, quando se liga a uma S1, o ambiente do Time Series Insights única unidade, o Time Series Insights lê aproximadamente 1 milhão de eventos por dia. Esse processo, como o Time Series Insights está com cinco dias de latência. No entanto, o que acontece é que o ambiente está a ser limitado.

Se tiver eventos antigos na origem de evento, pode abordar limitação em uma das seguintes formas:

- Altere limites de retenção da sua origem de evento para o ajudar a remover eventos antigos que não pretende que apareçam no Time Series Insights.
- Aprovisione um tamanho maior de ambiente (número de unidades de) para aumentar o débito de eventos antigos. Usando o exemplo anterior, se aumentar o mesmo ambiente de S1 para cinco unidades durante um dia, o ambiente deve acompanhar dentro de um dia. Se sua produção de eventos de estado estável é 1 milhão ou menos eventos por dia, pode reduzir a capacidade do evento para uma unidade após ela capturará a cópia de segurança.

O limite de limitação é imposto com base no tipo de SKU do ambiente e a capacidade. Todas as origens de eventos no ambiente de partilham esta capacidade. Se a origem do evento para o seu IoT hub ou hub de eventos envia por push dados para além dos limites de imposto, verá a limitação e um atraso.

A figura seguinte mostra um ambiente do Time Series Insights que tenha uma SKU S1 e uma capacidade de 3. É possível 3 milhões de eventos de entrada por dia.

![Capacidade atual de SKU de ambiente](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)

Por exemplo, partem do princípio de que este ambiente ingere mensagens a partir de um hub de eventos. A figura seguinte mostra a taxa de entrada:

![Taxa de entrada de exemplo para um hub de eventos](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)

A taxa de entrada diária é ~ 67,000 mensagens. Essa taxa se traduz em aproximadamente 46 mensagens a cada minuto. Se cada mensagem do hub de eventos foi simplificada para um único evento de Time Series Insights, a limitação não ocorre. Se cada mensagem do hub de eventos foi simplificada para 100 eventos do Time Series Insights, devem ser ingeridos 4,600 eventos a cada minuto. Um ambiente de S1 SKU que tem a capacidade de 3 pode 2.100 apenas eventos de entrada a cada minuto (1 milhão de eventos por dia = 700 eventos por minuto em três unidades = 2.100 eventos por minuto). Para esta configuração, verá um atraso devido à limitação. 

Para uma compreensão geral de como funciona a mesclar lógica, consulte [formas JSON suportadas](./how-to-shape-query-json.md).

#### <a name="recommended-resolutions-for-excessive-throttling"></a>Resoluções recomendadas para a limitação excessiva de

Para corrigir o atraso, aumente a capacidade SKU do seu ambiente. Para obter mais informações, consulte [dimensionar o seu ambiente do Time Series Insights](time-series-insights-how-to-scale-your-environment.md).

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>Causa b: a ingestão inicial de dados históricos diminui a entrada

Se ligar uma origem de evento existente, é provável que o seu IoT hub ou hub de eventos já contém dados. O ambiente começa a extrair dados desde o início do período de retenção de mensagem de origem do evento. Este é o processamento padrão e não pode ser substituída. Pode envolver a limitação. A limitação pode demorar algum tempo para acompanhar à medida que ingere dados históricos.

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>Resoluções recomendadas para grandes ingestão inicial

Para corrigir o atraso:

1. Aumente a capacidade SKU para o valor máximo permitido (10, neste caso). Depois de aumentar a capacidade, o processo de entrada é iniciado acompanhar muito mais rapidamente. É-lhe cobrada a maior capacidade. Para visualizar rapidamente como estamos obtendo até, pode ver o gráfico de disponibilidade na [Explorador do Time Series Insights](https://insights.timeseries.azure.com). 

2. Quando o atraso é atualizado com, diminua a capacidade SKU para a taxa de entrada normal.

## <a name="problem-three-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Problema três: definição de nome de propriedade de timestamp minha origem de evento não funciona

Certifique-se de que o nome da propriedade timestamp e o valor está em conformidade com as seguintes regras:

* O nome da propriedade timestamp diferencia maiúsculas de minúsculas.
* O valor da propriedade timestamp que vem da origem de evento como uma cadeia de caracteres do JSON deve ter o formato _aaaa-MM-ddTHH. FFFFFFFK_. Um exemplo é **2008-04-12T12:53Z**.

A maneira mais fácil para garantir que seu nome de propriedade timestamp é capturado e a funcionar corretamente é utilizar o Explorador do Time Series Insights. No Explorador do Time Series Insights, com o gráfico, selecione um período de tempo após introduzir o nome da propriedade timestamp. Com o botão direito a seleção e, em seguida, selecione o **explorar eventos** opção. 

No cabeçalho da coluna primeiro deve ser o nome da propriedade timestamp. Junto a palavra **Timestamp**, deverá ver **($ts)**.

Não verá os seguintes valores:

- *(abc)*: Indica que o Time Series Insights é ler os valores de dados como cadeias de caracteres.
- *Ícone de calendário*: Indica que o Time Series Insights é ler o valor de dados como *datetime*.
- *#*: Indica que o Time Series Insights está a ler os valores de dados como um número inteiro.

## <a name="next-steps"></a>Passos Seguintes

- Para obter assistência, iniciar uma conversação [fórum MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights) ou [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights).

- Para opções de suporte assistido, utilize [suporte do Azure](https://azure.microsoft.com/support/options/).
