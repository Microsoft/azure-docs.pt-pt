---
title: Diagnosticar, resolver problemas e resolver problemas - Azure Time Series Insights
description: Este artigo descreve como diagnosticar, resolver problemas e resolver problemas comuns no seu ambiente Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 02/04/2020
ms.custom: seodec18
ms.openlocfilehash: 4d9efa1ebf1a3e3b146c4f45b0e84047562141cd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192719"
---
# <a name="diagnose-and-solve-issues-in-your-time-series-insights-environment"></a>Diagnosticar e resolver problemas no seu ambiente time series Insights

Este artigo descreve questões que pode encontrar no seu ambiente Azure Time Series Insights. O artigo oferece potenciais causas e soluções para a resolução.

## <a name="video"></a>Vídeo

### <a name="learn-about-common-time-series-insights-challenges-and-mitigationsbr"></a>Conheça os desafios e mitigações comuns da Time Series Insights</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-no-data-is-shown"></a>Problema: Não são apresentados dados

Se não houver dados no explorador de Insights da [Série De Tempo Azure,](https://insights.timeseries.azure.com)considere estas causas comuns.

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>Causa A: Os dados de origem do evento não estão no formato JSON

A Azure Time Series Insights suporta apenas dados DaJSON. Para amostras JSON, consulte [as formas JSON suportadas](./how-to-shape-query-json.md).

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>Causa B: A chave de origem do evento está faltando uma permissão necessária

* Para um hub IoT no Azure IoT Hub, você precisa fornecer a chave que tem permissões de ligação de serviço. Selecione o **iothubowner** ou a política de **serviço.** Ambos têm permissões de ligação de serviço.

   [![IoT Hub serviço de ligação permissões](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png#lightbox)

* Para um hub de eventos em Azure Event Hubs, você precisa fornecer a chave que tem permissões de escuta. As políticas **de leitura** e **gestão** funcionarão, porque ambas têm permissões de escuta.

   [![Permissões de escuta do centro do evento](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)](media/diagnose-and-solve-problems/eventhub-listen-permissions.png#lightbox)

### <a name="cause-c-the-provided-consumer-group-isnt-exclusive-to-time-series-insights"></a>Causa C: O grupo de consumidores fornecido não é exclusivo da Time Series Insights

Quando regista um hub IoT ou um hub de eventos, é importante definir o grupo de consumidores que pretende usar para ler os dados. Este grupo de consumidores *não pode ser partilhado.* Se o grupo de consumidores for partilhado, o hub ou centro de eventos ioT subjacente desliga automaticamente e aleatoriamente um dos leitores. Forneça um grupo de consumidores único para que a Time Series Insights leia.

### <a name="cause-d-the-environment-has-just-been-provisioned"></a>Causa D: O ambiente acaba de ser provisionado

Os dados aparecerão no seu explorador de Insights da Série Time dentro de poucos minutos após o ambiente e os seus dados forem criados pela primeira vez.

## <a name="problem-some-data-is-shown-but-data-is-missing"></a>Problema: Alguns dados são mostrados, mas faltam dados

Quando os dados aparecem apenas parcialmente e os dados parecem estar atrasados, considere estas possíveis questões.

### <a name="cause-a-your-environment-is-being-throttled"></a>Causa A: O seu ambiente está a ser estrangulado

[O estrangulamento](time-series-insights-environment-mitigate-latency.md) é uma questão comum quando os ambientes são provisionados depois de criar uma fonte de evento que tenha dados. O Azure IoT Hub e o Azure Events Hubs armazenam dados por um n.º 7 dias. Time Series Insights começa sempre com o evento mais antigo na fonte do evento (primeiro-in, primeiro-out ou *FIFO).*

Por exemplo, se tiver 5 milhões de eventos numa fonte de evento quando se conecta a um ambiente S1, single-unit Time Series Insights, time series Insights lê aproximadamente 1 milhão de eventos por dia. Pode parecer que a Time Series Insights está a passar por cinco dias de latência. Mas o que está a acontecer é que o ambiente está a ser acelerado.

Se tiver eventos antigos na sua fonte de evento, pode aproximar-se de uma de duas maneiras:

- Altere os limites de retenção da fonte do seu evento para ajudar a remover eventos antigos que não quer aparecer na Time Series Insights.
- Providenciar uma dimensão ambiental maior (número de unidades) para aumentar a entrada de eventos antigos. No exemplo anterior, se aumentar o mesmo ambiente S1 para cinco unidades por um dia, o ambiente deve recuperar dentro de um dia. Se a sua produção de eventos em estado constante for de 1 milhão ou menos eventos por dia, pode reduzir a capacidade do evento para uma unidade após a Time Series Insights chegar.

O limite de estrangulamento imposto baseia-se no tipo e capacidade sku do ambiente. Todas as fontes de eventos no ambiente partilham desta capacidade. Se a fonte do evento para o seu hub ioT ou centro de eventos empurrar dados para além dos limites forçados, você sentirá estrangulamento e um atraso.

A figura que se segue mostra um ambiente time series insights que tem um SKU de S1 e uma capacidade de 3. Pode inseri-se em 3 milhões de eventos por dia.

[![Capacidade ambiental](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)](media/diagnose-and-solve-problems/environment-sku-current-capacity.png#lightbox)

Imagine um ambiente que ingere mensagens de um centro de eventos. Tem uma taxa diária de entrada de cerca de 67.000 mensagens. Esta taxa traduz-se em aproximadamente 46 mensagens a cada minuto.

* Se cada mensagem do centro de eventos for achatada para um único evento time series Insights, o estrangulamento não ocorre.
* Se cada mensagem do centro de eventos for achatada para 100 eventos time series Insights, 4.600 eventos devem ser ingeridos a cada minuto.

Um ambiente S1 SKU que tem uma capacidade de 3 pode ingressar apenas 2.100 eventos por minuto (1 milhão de eventos por dia = 700 eventos por minuto em três unidades = 2.100 eventos por minuto).

Para obter uma compreensão de alto nível de como funciona a lógica de achatamento, consulte [formas JSON apoiadas](./how-to-shape-query-json.md).

#### <a name="recommended-resolutions-for-excessive-throttling"></a>Resoluções recomendadas para estrangulamento excessivo

Para corrigir o atraso, aumente a capacidade SKU do seu ambiente. Para mais informações, leia [Scale your Time Series Insights environment](time-series-insights-how-to-scale-your-environment.md).

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>Causa B: Ingestão inicial de dados históricos atrasa ingresso

Se ligar uma fonte de evento existente, é provável que o seu hub IoT ou centro de eventos já contenha dados. O ambiente começa a retirar dados desde o início do período de retenção de mensagens da fonte do evento. Este processamento padrão não pode ser ultrapassado. Podes engatar a estrangulação. O estrangulamento pode demorar algum tempo a recuperar, pois ingeque dados históricos.

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>Resoluções recomendadas para grande ingestão inicial

Para corrigir o lag:

1. Aumentar a capacidade sku para o valor máximo permitido (10, neste caso). Depois de aumentar a capacidade, o processo de ingresso começa a recuperar muito mais rapidamente. É cobrado pelo aumento da capacidade. Para visualizar a rapidez com que está a recuperar, pode ver o gráfico de disponibilidade no [explorador time series Insights](https://insights.timeseries.azure.com).

2. Quando o atraso for apanhado, diminua a capacidade sku para a sua taxa normal de ingresso.

## <a name="problem-data-was-showing-previously-but-is-no-longer-showing"></a>Problema: Os dados mostravam anteriormente, mas já não aparece

Se a Time Series Insights já não estiver a ingerir dados, mas os eventos ainda estão a fluir para o Hub Iot ou para o Event Hub, considere esta causa potencial.

### <a name="cause-a-your-hub-access-key-was-regenerated-and-your-environment-needs-to-be-updated"></a>Causa A: A chave de acesso ao hub foi regenerada e o seu ambiente precisa de ser atualizado

Este problema ocorre quando a chave fornecida quando criou a fonte do seu evento já não é válida. Veria telemetria no seu centro, mas sem Mensagens Recebidas em Insights da Série Time. Se não tiver a certeza se a chave foi regenerada, pode pesquisar o registo de atividade do seu centro de eventos para "Criar ou Atualizar regras de autorização de espaço de nome". Para um hub IoT, procure "Criar ou atualizar recurso IotHub".

Para atualizar o seu ambiente Time Series Insights com a nova chave, abra o seu recurso hub no portal Azure e copie a nova chave. Vá ao seu recurso Time Series Insights e selecione **Fontes**de Eventos:

   [![Selecione Fontes de Eventos](media/diagnose-and-solve-problems/update-hub-key-step-1.png)](media/diagnose-and-solve-problems/update-hub-key-step-1.png#lightbox)

Selecione a fonte do evento ou fontes a partir das quais a ingestão parou, cola na nova tecla e, em seguida, selecione **Guardar:**

   [![Pasta na nova chave](media/diagnose-and-solve-problems/update-hub-key-step-2.png)](media/diagnose-and-solve-problems/update-hub-key-step-2.png#lightbox)

## <a name="problem-the-event-sources-timestamp-property-name-setting-doesnt-work"></a>Problema: A definição de nome de propriedade da fonte do evento não funciona

Certifique-se de que o valor da propriedade da marca de tempo que provém da sua fonte de evento como uma corda JSON está no formato _yyyy-MM-ddTHH:mms. FFFFFFFK._ Eis um exemplo: **2008-04-12T12:53Z**.

Tenha em mente que o nome da propriedade do timestamp é sensível a casos.

A maneira mais fácil de garantir que o nome da propriedade do seu timestamp é capturado e funcionando corretamente é usar o explorador time series Insights. No explorador Time Series Insights, utilizando o gráfico, selecione um período de tempo após ter introduzido o nome da propriedade da timestamp. Clique na seleção e, em seguida, **selecione Explore eventos**.

O cabeçalho da primeira coluna deve ser o seu nome de propriedade de timestamp. Ao lado da palavra **Timestamp**, **($ts)** será exibida.

Os seguintes valores não serão apresentados:

- *(abc)*: Indica que a Time Series Insights está a ler os valores dos dados como cordas.
- *Ícone do calendário*: Indica que a Time Series Insights está a ler os valores dos dados como valores de data.
- *#*: Indica que a Time Series Insights está a ler os valores dos dados como inteiros.

## <a name="next-steps"></a>Passos seguintes

- Leia sobre [como mitigar a latência em Azure Time Series Insights](time-series-insights-environment-mitigate-latency.md).

- Aprenda [a escalar o seu ambiente Time Series Insights](time-series-insights-how-to-scale-your-environment.md).
