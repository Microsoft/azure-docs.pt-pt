---
title: Diagnosticar, solucionar problemas e solucionar problemas-Azure Time Series Insights | Microsoft Docs
description: Este artigo descreve como diagnosticar, solucionar problemas e resolver problemas comuns em seu ambiente de Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 12/06/2019
ms.custom: seodec18
ms.openlocfilehash: ff723f490a3f6d34f652e0b21e5f6e0b16f0a841
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74900277"
---
# <a name="diagnose-and-solve-issues-in-your-time-series-insights-environment"></a>Diagnosticar e resolver problemas em seu ambiente de Time Series Insights

Este artigo descreve alguns problemas que você pode encontrar em seu ambiente de Azure Time Series Insights. O artigo oferece possíveis causas e soluções para resolução.

## <a name="video"></a>Vídeo

### <a name="learn-about-common-time-series-insights-customer-challenges-and-mitigationsbr"></a>Saiba mais sobre os desafios e as mitigações comuns do Time Series Insights cliente.</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-no-data-is-shown"></a>Problema: nenhum dado é mostrado

Nenhum dado no [Azure Time Series insights Explorer](https://insights.timeseries.azure.com) pode ocorrer por vários motivos comuns:

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>Causa: os dados de origem do evento não estão no formato JSON

Azure Time Series Insights dá suporte apenas a dados JSON. Para exemplos de JSON, veja [formas JSON suportadas](./how-to-shape-query-json.md).

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>Causa B: a chave de origem do evento não tem uma permissão necessária

* Para um hub IoT no Hub IoT do Azure, você deve fornecer a chave que tem permissões de **conexão de serviço** . Selecione as políticas de **iothubowner** ou de **serviço** , pois ambas têm permissões de **conexão de serviço** .

   [![permissões de conexão do serviço do Hub IoT](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png#lightbox)

* Para um hub de eventos nos hubs de eventos do Azure, você deve fornecer a chave que tem permissões de **escuta** . Qualquer uma das políticas de **leitura** ou de **Gerenciamento** funcionará porque ambas têm permissões de **escuta** .

   [![permissões de escuta do hub de eventos](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)](media/diagnose-and-solve-problems/eventhub-listen-permissions.png#lightbox)

### <a name="cause-c-the-consumer-group-provided-isnt-exclusive-to-time-series-insights"></a>Causa C: o grupo de consumidores fornecido não é exclusivo para Time Series Insights

Ao registrar um hub IoT ou um hub de eventos, é importante definir o grupo de consumidores que você deseja usar para ler os dados. Este grupo de consumidores *não pode ser compartilhado*. Se o grupo de consumidores for compartilhado, o Hub IoT subjacente ou o Hub de eventos se desconectará de forma automática e aleatoriamente um dos leitores. Forneça um grupo de consumidores exclusivo para Time Series Insights ler.

### <a name="cause-d-the-environment-has-just-been-provisioned"></a>Causa D: o ambiente acabou de ser provisionado

Os dados aparecerão em seu Time Series Insights Explorer dentro de alguns minutos depois que o ambiente e seus dados forem criados primeiro.

## <a name="problem-some-data-is-shown-but-data-is-missing"></a>Problema: alguns dados são mostrados, mas os dados estão ausentes

Quando os dados aparecem apenas parcialmente e os dados parecem ficar atrasados, você deve considerar várias possibilidades.

### <a name="cause-a-your-environment-is-being-throttled"></a>Causa um: seu ambiente está sendo limitado

A [limitação](time-series-insights-environment-mitigate-latency.md) é um problema comum quando os ambientes são provisionados depois que você cria uma origem de evento com dados. O Hub IoT do Azure e os hubs de eventos do Azure armazenam dados por até sete dias. Time Series Insights sempre comece com o evento mais antigo na origem do evento (primeiro a entrar, primeiro a sair ou *FIFO*).

Por exemplo, se você tiver 5 milhões eventos em uma origem de evento quando você se conectar a um ambiente de Time Series Insights de unidade única S1, Time Series Insights lerá aproximadamente 1 milhão eventos por dia. Pode parecer que Time Series Insights está experimentando cinco dias de latência. No entanto, o que acontece é que o ambiente está sendo limitado.

Se você tiver eventos antigos na origem do evento, poderá abordar a limitação de uma das duas maneiras:

- Altere os limites de retenção da origem do evento para ajudar a remover eventos antigos que você não deseja exibir no Time Series Insights.
- Provisione um tamanho de ambiente maior (número de unidades) para aumentar a taxa de transferência de eventos antigos. Usando o exemplo anterior, se você aumentar o mesmo ambiente S1 para cinco unidades por um dia, o ambiente deverá ser acumulado em um dia. Se a produção de eventos de estado estacionário for 1 milhão ou menos eventos por dia, você poderá reduzir a capacidade do evento para uma unidade depois que ele for atualizado.

O limite de limitação é imposto com base na capacidade e no tipo de SKU do ambiente. Todas as origens de eventos no ambiente compartilham essa capacidade. Se a origem do evento para o Hub IoT ou o Hub de eventos enviar dados para além dos limites impostos, você verá a limitação e um retardo.

A figura a seguir mostra um ambiente Time Series Insights que tem uma SKU de S1 e uma capacidade de 3. Ele pode ingressar 3 milhões eventos por dia.

[capacidade atual de SKU do ambiente de ![](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)](media/diagnose-and-solve-problems/environment-sku-current-capacity.png#lightbox)

Como exemplo, suponha que um ambiente ingere mensagens de um hub de eventos. A taxa de entrada diária é de ~ 67.000 mensagens. Essa taxa se traduz em aproximadamente 46 mensagens a cada minuto. 

* Se cada mensagem do hub de eventos for achatada para um único evento de Time Series Insights, a limitação não ocorrerá. 
* Se cada mensagem do hub de eventos for achatada para 100 Time Series Insights eventos, 4.600 os eventos devem ser ingeridos a cada minuto. 

Um ambiente de SKU S1 que tem uma capacidade de 3 pode entrar apenas 2.100 eventos a cada minuto (1 milhão eventos por dia = 700 eventos por minuto em três unidades = 2.100 eventos por minuto). 

Para obter uma compreensão de alto nível de como funciona a lógica de mesclagem, consulte [formas de JSON com suporte](./how-to-shape-query-json.md).

#### <a name="recommended-resolutions-for-excessive-throttling"></a>Resoluções recomendadas para limitação excessiva

Para corrigir o retardo, aumente a capacidade de SKU do seu ambiente. Para obter mais informações, consulte [dimensionar seu ambiente de time Series insights](time-series-insights-how-to-scale-your-environment.md).

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>Causa B: a ingestão inicial de dados históricos torna a entrada mais lenta

Se você conectar uma origem de evento existente, é provável que o Hub IoT ou Hub de eventos já contenha dados. O ambiente começa a extrair dados do início do período de retenção da mensagem da origem do evento. Esse processamento padrão não pode ser substituído. Você pode envolver a limitação. A limitação pode levar algum tempo para ser atualizada à medida que ele ingere dados históricos.

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>Resoluções recomendadas para ingestão inicial grande

Para corrigir o retardo:

1. Aumente a capacidade do SKU para o valor máximo permitido (10, neste caso). Depois de aumentar a capacidade, o processo de entrada começa a ser atualizado muito mais rapidamente. Você é cobrado pela maior capacidade. Para visualizar a rapidez com que você está se capturando, você pode exibir o gráfico de disponibilidade no [Time Series insights Explorer](https://insights.timeseries.azure.com).

2. Quando o retardo for feito, diminua a capacidade do SKU para sua taxa de entrada normal.

## <a name="problem-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Problema: a configuração do nome da propriedade Timestamp da origem do evento não funciona

Verifique se o nome e o valor da propriedade Timestamp estão de acordo com as seguintes regras:

* O nome da propriedade Timestamp diferencia maiúsculas de minúsculas.
* O valor da propriedade Timestamp que vem da origem do evento como uma cadeia de caracteres JSON deve ter o formato _aaaa-mm-ddThh: mm: SS. FFFFFFFK_. Um exemplo é **2008-04-12T12:53Z**.

A maneira mais fácil de garantir que o nome da propriedade de carimbo de data/hora seja capturado e funcionando corretamente é usar o Time Series Insights Explorer. No Time Series Insights Explorer, usando o gráfico, selecione um período de tempo depois de inserir o nome da propriedade de carimbo de data/hora. Clique com o botão direito do mouse na seleção e selecione a opção **explorar eventos** .

O primeiro cabeçalho de coluna deve ser seu nome de propriedade de carimbo de data/hora. Ao lado do **carimbo de data/hora**do Word, você deve ver **($TS)** .

Você não deve ver os seguintes valores:

- *(ABC)* : indica que Time Series insights está lendo os valores de dados como cadeias de caracteres.
- *Ícone de calendário*: indica que Time Series insights está lendo o valor de dados como *DateTime*.
- *#* : indica que Time Series insights está lendo os valores de dados como um número inteiro.

## <a name="next-steps"></a>Passos seguintes

- Leia sobre [como mitigar a latência no Azure Time Series insights](time-series-insights-environment-mitigate-latency.md).

- Saiba [como dimensionar seu ambiente de time Series insights](time-series-insights-how-to-scale-your-environment.md).