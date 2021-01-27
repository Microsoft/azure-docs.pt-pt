---
title: Fontes de eventos de streaming - Azure Time Series Insights Gen2 | Microsoft Docs
description: Saiba mais sobre o streaming de dados para a Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: ae07f51a91745acdaf2601d3a50bf282129dac71
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881810"
---
# <a name="azure-time-series-insights-gen2-event-sources"></a>Azure Time Series Insights Gen2 Event Sources

 O seu ambiente Azure Time Series Insights Gen2 pode ter até duas fontes de eventos de streaming. Dois tipos de recursos Azure são suportados como entradas:

- [Hub IoT do Azure](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Os eventos devem ser enviados como UTF-8 codificado JSON.

## <a name="create-or-edit-event-sources"></a>Criar ou editar fontes de eventos

Os recursos de origem do evento podem viver na mesma subscrição Azure que o ambiente Azure Time Series Insights Gen2 ou uma subscrição diferente. Pode utilizar o [portal Azure](./tutorials-set-up-tsi-environment.md#create-an-azure-time-series-insights-gen2-environment), [Azure CLI,](https://github.com/Azure/azure-cli-extensions/tree/master/src/timeseriesinsights) [ARM Templates](time-series-insights-manage-resources-using-azure-resource-manager-template.md)e a [API REST](/rest/api/time-series-insights/management(gen1/gen2)/eventsources) para criar, editar ou remover as fontes de eventos do seu ambiente.

Quando ligar uma fonte de evento, o seu ambiente Azure Time Series Insights Gen2 irá ler todos os eventos atualmente armazenados no seu Iot ou Event Hub, a começar pelo evento mais antigo.

> [!IMPORTANT]
>
> - Pode experimentar uma elevada latência inicial ao anexar uma fonte de evento ao seu ambiente Azure Time Series Insights Gen2.
> - A latência da fonte do evento depende do número de eventos atualmente no seu Hub IoT ou Centro de Eventos.
> - A alta latência diminuirá após a primeira ingestão de dados de origem de eventos. Envie um bilhete de apoio através do portal Azure se sentir uma elevada latência.

## <a name="streaming-ingestion-best-practices"></a>Boas práticas de ingestão de streaming

- Crie sempre um grupo de consumidores único para o seu ambiente Azure Time Series Insights Gen2 para consumir dados da sua fonte de evento. A reutilização de grupos de consumidores pode causar desconexões aleatórias e pode resultar na perda de dados.

- Configure o seu ambiente Azure Time Series Insights Gen2 e o seu IoT Hub e/ou Centros de Eventos na mesma região do Azure. Embora seja possível configurar uma fonte de eventos numa região separada, este cenário não é apoiado e não podemos garantir uma elevada disponibilidade.

- Não ultrapasse o limite de [taxa de produção](./concepts-streaming-ingress-throughput-limits.md) do seu ambiente ou por limite de partição.

- Configure um [alerta](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts) de lag para ser notificado se o seu ambiente estiver a passar por problemas de processamento de dados. Consulte as [cargas de trabalho de produção](./concepts-streaming-ingestion-event-sources.md#production-workloads) abaixo para obter condições de alerta sugeridas.

- Utilize a ingestão de streaming apenas em tempo real e dados recentes, o streaming de dados históricos não é suportado.

- Compreenda como as propriedades serão escapadas e os dados da JSON [achatados e armazenados.](./concepts-json-flattening-escaping-rules.md)

- Siga o princípio do menor privilégio ao fornecer cordas de ligação de fonte de evento. Para os Centros de Eventos, configurar uma política de acesso partilhado apenas com a reclamação *de envio,* e para o IoT Hub utilizar apenas a permissão *de ligação* de serviço.

## <a name="production-workloads"></a>Cargas de trabalho de produção

Além das melhores práticas acima, recomendamos que implemente o seguinte para cargas de trabalho críticas de negócios.

- Aumente o seu tempo de retenção de dados IoT Hub ou Event Hub para o máximo de 7 dias.

- Crie alertas ambientais no portal Azure. Os alertas [baseados nas métricas](./how-to-monitor-tsi-reference.md#metrics) da plataforma permitem validar o comportamento do gasoduto de ponta a ponta. As instruções para criar e gerir alertas estão [aqui.](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts) Condições de alerta sugeridas:

  - IngressReceivedMesstimeLag é maior que 5 minutos
  - IngressReceivedBytes é 0
- Mantenha a sua carga de ingestão equilibrada entre as divisórias IoT Hub ou Event Hub.

### <a name="historical-data-ingestion"></a>Ingestão de dados históricos

A utilização do gasoduto de streaming para importar dados históricos não é atualmente suportada no Azure Time Series Insights Gen2. Se precisar de importar dados passados para o seu ambiente, siga as orientações abaixo:

- Não transmita dados ao vivo e históricos em paralelo. Ingerir dados fora da ordem resultará num desempenho de consulta degradado.
- Ingerir dados históricos na moda ordenada pelo tempo para melhor desempenho.
- Mantenha-se dentro dos limites da taxa de produção abaixo.
- Desative a Warm Store se os dados forem mais antigos do que o período de retenção da Warm Store.

## <a name="event-source-timestamp"></a>Data de tempo de fonte de evento

Ao configurar uma fonte de evento, será solicitado que forneça uma propriedade de identificação de hora. A propriedade de relógios é usada para rastrear eventos ao longo do tempo, este é o tempo que será usado como o $event.$ts nas [APIs de Consulta](/rest/api/time-series-insights/dataaccessgen2/query/execute) e para traçar séries no Azure Time Series Insights Explorer. Se nenhuma propriedade for fornecida no momento da criação, ou se a propriedade do relógio faltar de um evento, então o tempo de IoT Hub ou Eventos do evento será usado como padrão. Os valores de propriedade do timetamp são armazenados na UTC.

Em geral, os utilizadores optarão por personalizar a propriedade do timetamp e utilizar o tempo em que o sensor ou tag gerou a leitura em vez de usar o tempo padrão do hub. Isto é particularmente necessário quando os dispositivos têm perda de conectividade intermitente e um lote de mensagens atrasadas são reencaminhadas para Azure Time Series Insights Gen2.

Se a sua marca de tempo personalizada estiver dentro de um objeto JSON aninhado ou de uma matriz, você precisará fornecer o nome de propriedade correto seguindo nossas [convenções de achatamento e fuga](concepts-json-flattening-escaping-rules.md)de nomeação . Por exemplo, o tempo de origem do evento para a carga útil JSON [mostrada aqui](concepts-json-flattening-escaping-rules.md#example-a) deve ser introduzido como `"values.time"` .

### <a name="time-zone-offsets"></a>Compensações de fuso horário

Os cartões de tempo devem ser enviados em formato ISO 8601 e serão armazenados na UTC. Se for fornecida uma compensação do fuso horário, a offset será aplicada e, em seguida, o tempo armazenado e devolvido em formato UTC. Se o offset for indevidamente formatado, será ignorado. Em situações em que a sua solução pode não ter contexto da compensação original, pode enviar os dados de compensação numa propriedade adicional separada do evento para garantir que está preservada e que a sua aplicação pode referenciar numa resposta de consulta.

A compensação do fuso horário deve ser formatada como uma das seguintes:

±HHMMZ</br>
±HH:MM</br>
±HH:MMZ</br>

## <a name="next-steps"></a>Próximos passos

- Leia as [Regras de Achatamento e Fuga](./concepts-json-flattening-escaping-rules.md) do JSON para entender como os eventos serão armazenados.

- Compreenda as [limitações](./concepts-streaming-ingress-throughput-limits.md) de produção do seu ambiente
