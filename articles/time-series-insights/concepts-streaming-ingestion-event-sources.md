---
title: Fontes de eventos de streaming - Azure Time Series Insights Gen2 | Microsoft Docs
description: Saiba mais sobre o streaming de dados para a Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 4e22d93d3037c190193f53b7cfdbc87cff2da6ed
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504401"
---
# <a name="azure-time-series-insights-gen2-event-sources"></a>Fontes de eventos Azure Time Series Insights Gen2

O seu ambiente Azure Time Series Insights Gen2 pode ter até duas fontes de eventos de streaming. Dois tipos de recursos Azure são suportados como entradas:

- [Hub IoT do Azure](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Os eventos devem ser enviados como UTF-8 codificado JSON.

## <a name="create-or-edit-event-sources"></a>Criar ou editar fontes de eventos

A fonte do evento é a ligação entre o seu hub e o ambiente Azure Time Series Insights Gen2, e um recurso de tipo separado é criado no seu grupo de `Time Series Insights event source` recursos. O IoT Hub ou os recursos do Event Hub podem viver na mesma subscrição Azure que o ambiente Azure Time Series Insights Gen2 ou uma subscrição diferente. No entanto, é uma boa prática alojar o seu ambiente Azure Time Series Insights e o IoT Hub ou Event Hub dentro da mesma região do Azure.

Pode utilizar o [portal Azure](./tutorials-set-up-tsi-environment.md#create-an-azure-time-series-insights-gen2-environment), [Azure CLI,](https://docs.microsoft.com/cli/azure/ext/timeseriesinsights/tsi/event-source) [modelos Azure Resource Manager](time-series-insights-manage-resources-using-azure-resource-manager-template.md)e a [API REST](/rest/api/time-series-insights/management(gen1/gen2)/eventsources) para criar, editar ou remover as fontes de eventos do seu ambiente.

## <a name="start-options"></a>Opções de início

Ao criar uma fonte de evento, tem a opção de especificar quais os dados pré-existentes que devem ser recolhidos. Esta definição é opcional. Estão disponíveis as seguintes opções:

| Nome   |  Descrição  |  Exemplo do modelo do gestor de recursos Azure |
|----------|-------------|------|
| O mais cedo disponível | Ingerir todos os dados pré-existentes armazenados no IoT ou No Centro de Eventos | `"ingressStartAt": {"type": "EarliestAvailable"}` |
| EventSourceCreationTime |  Comece a ingerir dados que cheguem após a criação da fonte do evento. Todos os dados pré-existentes que foram transmitidos antes da criação da fonte do evento serão ignorados. Esta é a definição padrão no portal Azure   |   `"ingressStartAt": {"type": "EventSourceCreationTime"}` |
| Tempo Personalizado | O seu ambiente irá ingerir dados a partir do seu tempo personalizado (UTC) para a frente. Todos os eventos que foram encadeados no seu IoT ou Event Hub em ou após o seu tempo personalizado será ingerido e armazenado. Todos os eventos que chegaram antes do seu tempo personalizado serão ignorados. Note que "tempo enqueso" refere-se à hora (na UTC) que o evento chegou ao seu IoT ou Event Hub. Isto difere de uma propriedade de [timetamp](./concepts-streaming-ingestion-event-sources.md#event-source-timestamp) personalizada que está dentro do corpo do seu evento. |     `"ingressStartAt": {"type": "CustomEnqueuedTime", "time": "2021-03-01T17:00:00.20Z"}` |

> [!IMPORTANT]
>
> - Se selecionar o EarliestAvailable e tiver muitos dados pré-existentes, poderá experimentar uma elevada latência inicial, uma vez que o ambiente Azure Time Series Insights Gen2 processa todos os seus dados.
> - Esta elevada latência deve eventualmente diminuir à medida que os dados são indexados. Envie um bilhete de apoio através do portal Azure se sentir uma elevada latência.

* O mais cedo disponível

![Diagrama disponível mais cedo](media/concepts-streaming-event-sources/event-source-earliest-available.png)

* EventSourceCreationTime

![Diagrama de Tempo de EventoSourceCreation](media/concepts-streaming-event-sources/event-source-creation-time.png)

* Tempo Personalizado

![Diagrama customEnqueuedTime](media/concepts-streaming-event-sources/event-source-custom-enqueued-time.png)


## <a name="streaming-ingestion-best-practices"></a>Boas práticas de ingestão de streaming

- Crie sempre um grupo de consumidores único para o seu ambiente Azure Time Series Insights Gen2 para consumir dados da sua fonte de evento. A reutilização dos grupos de consumidores pode causar desconexões aleatórias e pode resultar na perda de dados.

- Configure o seu ambiente Azure Time Series Insights Gen2 e o seu IoT Hub e/ou Centros de Eventos na mesma região do Azure. Embora seja possível configurar uma fonte de eventos numa região separada, este cenário não é apoiado e não podemos garantir uma elevada disponibilidade.

- Não ultrapasse o limite de [taxa de produção](./concepts-streaming-ingress-throughput-limits.md) do seu ambiente ou por limite de partição.

- Configure um [alerta](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts) de lag para ser notificado se o seu ambiente estiver a passar por problemas de processamento de dados. Consulte as [cargas de trabalho de produção](./concepts-streaming-ingestion-event-sources.md#production-workloads) abaixo para obter condições de alerta sugeridas.

- Utilize a ingestão de streaming apenas em tempo real e dados recentes, o streaming de dados históricos não é suportado.

- Compreenda como as propriedades serão escapadas e os dados da JSON [achatados e armazenados.](./concepts-json-flattening-escaping-rules.md)

- Siga o princípio do menor privilégio ao fornecer cordas de ligação de fonte de evento. Para os Centros de Eventos, configurar uma política de acesso partilhado apenas com a reclamação *de envio,* e para o IoT Hub utilizar apenas a permissão *de ligação* de serviço.

> [!CAUTION]
> Se eliminar o seu Hub IoT ou Centro de Eventos e recriar um novo recurso com o mesmo nome, tem de criar uma nova fonte de eventos e anexar o novo Hub IoT ou Centro de Eventos. Os dados não serão ingeridos até completar este passo.

## <a name="production-workloads"></a>Cargas de trabalho de produção

Além das melhores práticas acima, recomendamos que implemente o seguinte para cargas de trabalho críticas de negócios.

- Aumente o seu tempo de retenção de dados IoT Hub ou Event Hub para o máximo de sete dias.

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

Ao configurar uma fonte de evento, será solicitado que forneça uma propriedade de identificação de hora. A propriedade de relógios é usada para rastrear eventos ao longo do tempo, este é o tempo que será usado como o relógio de tempo `$ts` nas [APIs de Consulta](/rest/api/time-series-insights/dataaccessgen2/query/execute) e para traçar séries no Azure Time Series Insights Explorer. Se nenhuma propriedade for fornecida no momento da criação, ou se a propriedade do relógio faltar de um evento, então o tempo de IoT Hub ou Eventos do evento será usado como padrão. Os valores de propriedade do timetamp são armazenados na UTC.

Em geral, os utilizadores optarão por personalizar a propriedade do timetamp e utilizar o tempo em que o sensor ou tag gerou a leitura em vez de usar o tempo padrão do hub. Isto é particularmente necessário quando os dispositivos têm perda de conectividade intermitente e um lote de mensagens atrasadas são reencaminhadas para Azure Time Series Insights Gen2.

Se a sua marca de tempo personalizada estiver dentro de um objeto JSON aninhado ou de uma matriz, você precisará fornecer o nome de propriedade correto seguindo nossas [convenções de achatamento e fuga](concepts-json-flattening-escaping-rules.md)de nomeação . Por exemplo, o tempo de origem do evento para a carga útil JSON [mostrada aqui](concepts-json-flattening-escaping-rules.md#example-a) deve ser introduzido como `"values.time"` .

### <a name="time-zone-offsets"></a>Compensações de fuso horário

Os cartões de tempo devem ser enviados em formato ISO 8601 e serão armazenados na UTC. Se for fornecida uma compensação do fuso horário, a offset será aplicada e, em seguida, o tempo armazenado e devolvido em formato UTC. Se o offset for indevidamente formatado, será ignorado. Em situações em que a sua solução pode não ter contexto da compensação original, pode enviar os dados de compensação numa propriedade adicional separada do evento para garantir que está preservada e que a sua aplicação pode referenciar numa resposta de consulta.

A compensação do fuso horário deve ser formatada como uma das seguintes:

±HHMMZ</br>
±HH:MM</br>
±HH:MMZ</br>

## <a name="next-steps"></a>Passos seguintes

- Leia as [Regras de Achatamento e Fuga](./concepts-json-flattening-escaping-rules.md) do JSON para entender como os eventos serão armazenados.

- Compreenda as [limitações](./concepts-streaming-ingress-throughput-limits.md) de produção do seu ambiente
