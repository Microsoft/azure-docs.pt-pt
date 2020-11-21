---
title: Diagnosticar, resolver problemas e resolver problemas - Azure Time Series Insights
description: Este artigo descreve como diagnosticar, resolver problemas e resolver problemas comuns no seu ambiente Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 15f2ff5aaa1d731c13125d0a3ab4ac32acb9276c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95023280"
---
# <a name="diagnose-and-solve-issues-in-your-azure-time-series-insights-gen1-environment"></a>Diagnosticar e resolver problemas no seu ambiente Azure Time Series Insights Gen1

> [!CAUTION]
> Este é um artigo da Gen1.

Este artigo descreve problemas que poderá encontrar no seu ambiente Azure Time Series Insights. O artigo oferece potenciais causas e soluções para a resolução.

## <a name="video"></a>Vídeo

### <a name="learn-about-common-azure-time-series-insights-challenges-and-mitigationsbr"></a>Conheça os desafios e mitigações comuns da Série De Tempo do Azure</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-no-data-is-shown"></a>Problema: Não são mostrados dados

Se não houver dados no [explorador Azure Time Series Insights,](https://insights.timeseries.azure.com)considere estas causas comuns.

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>Causa A: Os dados de origem do evento não estão no formato JSON

A azure Time Series Insights suporta apenas dados JSON. Para as amostras JSON, consulte [as formas JSON suportadas.](./concepts-json-flattening-escaping-rules.md)

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>Causa B: Falta uma permissão necessária para a chave de origem do evento

* Para um hub IoT em Azure IoT Hub, você precisa fornecer a chave que tem permissões de ligação de serviço. Selecione a política **de iothubowner** ou **de serviço.** Ambos têm permissões de ligação de serviço.

   [![Permissões de ligação do serviço IoT Hub](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png#lightbox)

* Para um centro de eventos em Azure Event Hubs, você precisa fornecer a chave que tem permissões de escuta. As políticas **de leitura** e **gestão** funcionarão ambas porque ambas têm permissões de escuta.

   [![Permissões de escuta do centro de eventos](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)](media/diagnose-and-solve-problems/eventhub-listen-permissions.png#lightbox)

### <a name="cause-c-the-provided-consumer-group-isnt-exclusive-to-azure-time-series-insights"></a>Causa C: O grupo de consumidores fornecido não é exclusivo da Azure Time Series Insights

Quando regista um hub IoT ou um centro de eventos, é importante definir o grupo de consumidores que pretende usar para ler os dados. Este grupo de *consumidores não pode ser partilhado.* Se o grupo de consumidores for partilhado, o hub ioT subjacente ou o centro de eventos desliga automaticamente e aleatoriamente um dos leitores. Forneça um grupo de consumidores único para a Azure Time Series Insights para ler.

### <a name="cause-d-the-environment-has-just-been-provisioned"></a>Causa D: O ambiente acaba de ser aprovisionado

Os dados aparecerão no seu explorador Azure Time Series Insights dentro de poucos minutos após o ambiente e os seus dados serem criados pela primeira vez.

## <a name="problem-some-data-is-shown-but-data-is-missing"></a>Problema: Alguns dados são mostrados, mas faltam dados

Quando os dados aparecem apenas parcialmente e os dados parecem estar atrasados, considere estas possíveis questões.

### <a name="cause-a-your-environment-is-being-throttled"></a>Causa A: O seu ambiente está a ser acelerado

[O estrangulamento](time-series-insights-environment-mitigate-latency.md) é uma questão comum quando os ambientes são aprovisionados depois de criar uma fonte de evento que tenha dados. Azure IoT Hub e Azure Events Hubs armazenam dados por um total de sete dias. Azure Time Series Insights começa sempre com o evento mais antigo na fonte do evento (primeiro-a-entrar, primeiro ou *FIFO).*

Por exemplo, se tiver 5 milhões de eventos numa fonte de evento quando se conecta a um ambiente S1, single-unit Azure Time Series Insights, a Azure Time Series Insights lê aproximadamente 1 milhão de eventos por dia. Pode parecer que a Azure Time Series Insights está a passar por cinco dias de latência. Mas o que está a acontecer é que o ambiente está a ser acelerado.

Se tiver eventos antigos na sua fonte de evento, pode aproximar-se do estrangulamento de duas maneiras:

* Altere os limites de retenção da fonte do evento para ajudar a remover eventos antigos que não quer aparecer no Azure Time Series Insights.
* Provisão de um tamanho de ambiente maior (número de unidades) para aumentar a produção de eventos antigos. No exemplo anterior, se aumentar o mesmo ambiente S1 para cinco unidades por um dia, o ambiente deve ser apanhado dentro de um dia. Se a sua produção de eventos em estado constante for de 1 milhão ou menos eventos por dia, pode reduzir a capacidade do evento para uma unidade depois de a Azure Time Series Insights se encontrar.

O limite de estrangulamento forçado baseia-se no tipo e capacidade SKU do ambiente. Todas as fontes de eventos do ambiente partilham esta capacidade. Se a fonte de evento para o seu hub IoT ou centro de eventos empurrar os dados para além dos limites impostos, você vai experimentar estrangulamento e um atraso.

A seguinte figura mostra um ambiente Azure Time Series Insights que tem um SKU de S1 e uma capacidade de 3. Pode ingressá-lo 3 milhões de eventos por dia.

[![Capacidade ambiental](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)](media/diagnose-and-solve-problems/environment-sku-current-capacity.png#lightbox)

Imagine um ambiente que ingere mensagens de um centro de eventos. Tem uma taxa diária de entrada de cerca de 67.000 mensagens. Esta taxa traduz-se em aproximadamente 46 mensagens a cada minuto.

* Se cada mensagem do centro de eventos for achatada para um único evento Azure Time Series Insights, o estrangulamento não ocorre.
* Se cada mensagem do centro de eventos for achatada para 100 eventos Azure Time Series Insights, 4.600 eventos devem ser ingeridos a cada minuto.

Um ambiente S1 SKU com capacidade para 3 pode entrar apenas 2.100 eventos por minuto (1 milhão de eventos por dia = 700 eventos por minuto em três unidades = 2.100 eventos por minuto).

Para obter uma compreensão de alto nível de como a lógica de achatamento funciona, consulte [as formas JSON suportadas.](./concepts-json-flattening-escaping-rules.md)

#### <a name="recommended-resolutions-for-excessive-throttling"></a>Resoluções recomendadas para estrangulamento excessivo

Para corrigir o lag, aumente a capacidade SKU do seu ambiente. Para obter mais informações, leia [Escalar o ambiente Azure Time Series Insights](time-series-insights-how-to-scale-your-environment.md).

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>Causa B: Ingestão inicial de dados históricos atrasa a entrada

Se ligar uma fonte de evento existente, é provável que o seu hub IoT ou centro de eventos já contenha dados. O ambiente começa a retirar dados desde o início do período de retenção de mensagens da fonte do evento. Este processamento predefinido não pode ser ultrapassado. Podes acelerar. A aceleração pode demorar algum tempo a recuperar, pois ingere dados históricos.

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>Resoluções recomendadas para grande ingestão inicial

Para corrigir o atraso:

1. Aumente a capacidade SKU para o valor máximo permitido (10, neste caso). Depois de aumentar a capacidade, o processo de entrada começa a recuperar muito mais rapidamente. És cobrado pelo aumento da capacidade. Para visualizar a rapidez com que está a recuperar, pode ver o gráfico de disponibilidade no [explorador Azure Time Series Insights](https://insights.timeseries.azure.com).

2. Quando o lag for travado, diminua a capacidade SKU para a sua taxa normal de entrada.

## <a name="problem-data-was-showing-previously-but-is-no-longer-showing"></a>Problema: Os dados foram mostrados anteriormente, mas já não mostram

Se o Azure Time Series Insights já não estiver a ingerir dados, mas os eventos ainda estão a fluir para o Iot Hub ou para o Event Hub, considere esta causa potencial.

### <a name="cause-a-your-hub-access-key-was-regenerated-and-your-environment-needs-to-be-updated"></a>Causa A: A chave de acesso ao hub foi regenerada e o seu ambiente precisa de ser atualizado

Este problema ocorre quando a chave fornecida quando criou a sua fonte de evento já não é válida. Verias a telemetria no teu hub, mas nenhuma Ingress Recebeu Mensagens em Azure Time Series Insights. Se não tiver a certeza se a chave foi regenerada, pode pesquisar o registo de atividades do seu centro de eventos para "Criar ou atualizar regras de autorização do espaço de nome". Para um hub IoT, procure "Criar ou atualizar o Recurso IotHub".

Para atualizar o ambiente Azure Time Series Insights com a nova chave, abra o seu recurso hub no portal Azure e copie a nova chave. Aceda ao seu recurso Azure Time Series Insights e selecione **Fontes de Eventos**:

   [![Selecione Fontes de Eventos](media/diagnose-and-solve-problems/update-hub-key-step-1.png)](media/diagnose-and-solve-problems/update-hub-key-step-1.png#lightbox)

Selecione a fonte do evento ou fontes a partir das quais a ingestão parou, cole na nova tecla e, em seguida, **selecione Guardar**:

   [![Pasta na nova chave](media/diagnose-and-solve-problems/update-hub-key-step-2.png)](media/diagnose-and-solve-problems/update-hub-key-step-2.png#lightbox)

## <a name="problem-the-event-sources-timestamp-property-name-setting-doesnt-work"></a>Problema: A definição do nome da propriedade do timetamp da fonte do evento não funciona

Certifique-se de que o valor da propriedade do timetamp que vem da fonte do seu evento como uma corda JSON está no formato _yyy-MM-ddTHH:mm:mms. FFFFFFFK_. Aqui está um exemplo: **2008-04-12T12:53Z**.

Tenha em mente que o nome da propriedade do timetamp é sensível a maiíssimos.

A maneira mais fácil de garantir que o nome da propriedade do seu relógio de tempo é capturado e funcionando corretamente é usar o explorador Azure Time Series Insights. No explorador Azure Time Series Insights, utilizando o gráfico, selecione um período de tempo após ter introduzido o nome da propriedade do relógio. Clique com o botão direito na seleção e, em seguida, **selecione Explore eventos**.

O primeiro cabeçalho da coluna deve ser o nome da propriedade do seu tempotando. Ao lado da palavra **Timestamp**, **($ts)** será exibida.

Os seguintes valores não serão apresentados:

* *Indica* que a Azure Time Series Insights está a ler os valores de dados como cordas.
* *Ícone do calendário*: Indica que o Azure Time Series Insights está a ler os valores dos dados como valores de data.
* *#*: Indica que a Azure Time Series Insights está a ler os valores de dados como inteiros.

## <a name="next-steps"></a>Próximos passos

* Leia sobre [como mitigar a latência em Azure Time Series Insights](time-series-insights-environment-mitigate-latency.md).

* Saiba [como escalar o ambiente Azure Time Series Insights](time-series-insights-how-to-scale-your-environment.md).