---
title: Adicione uma fonte de evento saque a evento Hubs - Azure Time Series Insights [ Microsoft Docs
description: Saiba como adicionar uma fonte de evento saqueada pelo Azure Event Hubs ao seu ambiente Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/15/2020
ms.custom: seodec18
ms.openlocfilehash: 021ac5fccf4d694895ab9941bd46dd2388f49af9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81407466"
---
# <a name="add-an-event-hub-event-source-to-your-time-series-insights-environment"></a>Adicione uma fonte de evento sita ao seu ambiente Time Series Insights

Este artigo descreve como usar o portal Azure para adicionar uma fonte de evento que lê dados dos Hubs de Eventos Azure para o seu ambiente Azure Time Series Insights.

> [!NOTE]
> Os passos descritos neste artigo aplicam-se tanto aos ambientes de pré-visualização da Time Series Insights GA e Time Series Insights.

## <a name="prerequisites"></a>Pré-requisitos

- Crie um ambiente time series insights como descrito em [Create a Azure Time Series Insights environment](./time-series-insights-update-create-environment.md).
- Crie um hub de eventos. Leia Criar um espaço de [nome sinuoso de eventos e um hub de eventos utilizando o portal Azure](../event-hubs/event-hubs-create.md).
- O centro do evento deve ter eventos de mensagens ativas enviados para ele. Saiba como [enviar eventos para os Hubs de Eventos Azure utilizando a .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Crie um grupo de consumidores dedicado no centro de eventos do que o ambiente Time Series Insights possa consumir. Cada fonte de evento da Time Series Insights deve ter o seu próprio grupo de consumidores dedicado que não é partilhado com qualquer outro consumidor. Se vários leitores consumirem eventos do mesmo grupo de consumidores, todos os leitores são suscetíveis de apresentar falhas. Há um limite de 20 grupos de consumidores por centro de eventos. Para mais detalhes, leia o guia de programação do [Event Hubs](../event-hubs/event-hubs-programming-guide.md).

### <a name="add-a-consumer-group-to-your-event-hub"></a>Adicione um grupo de consumidores ao seu centro de eventos

As aplicações utilizam grupos de consumidores para retirar dados dos Hubs de Eventos Azure. Para ler de forma fiável os dados do seu centro de eventos, forneça um grupo de consumidores dedicado que é usado apenas por este ambiente time series Insights.

Para adicionar um novo grupo de consumidores no seu centro de eventos:

1. No [portal Azure, localize](https://portal.azure.com)e abra a sua instância de centro de eventos a partir do painel de **visão geral** do seu espaço de nome do centro de eventos. **Selecione Entidades > Centros** de Eventos ou encontre a sua instância em **Nome**.

    [![Abra o seu espaço de nome do centro de eventos](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png#lightbox)

1. Na sua instância de hub de eventos, **selecione Entidades > Grupos de Consumidores.** Em seguida, selecione **+ Grupo de consumidores** para adicionar um novo grupo de consumidores. 

   [![Centro de eventos - Adicione um grupo de consumidores](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png#lightbox)

   Caso contrário, selecione um grupo de consumidores existente e salte para a secção seguinte.

1. Na página dos **grupos de consumidores,** introduza um novo valor único para **o Nome.**  Use este mesmo nome quando criar uma nova fonte de evento no ambiente Time Series Insights.

1. Selecione **Criar**.

## <a name="add-a-new-event-source"></a>Adicione uma nova fonte de evento

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Localize o ambiente de Insights da Série Do Tempo existente. No menu esquerdo, selecione **Todos os recursos**e, em seguida, selecione o ambiente Time Series Insights.

1. Selecione **Fontes**de Eventos, e, em seguida, **selecione Adicionar**.

   [![Sob fontes de eventos, selecione o botão Adicionar](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png#lightbox)

1. Introduza um valor para o **nome de origem do** `Contoso-TSI-GA-Event-Hub-ES`Evento que seja exclusivo deste ambiente time series Insights, como .

1. Para **origem,** selecione **Event Hub**.

1. Selecione os valores adequados para **a opção Import:**

   * Se tiver um hub de eventos existente numa das suas subscrições, selecione **Use Event Hub a partir de subscrições disponíveis**. Esta opção é a abordagem mais fácil.

     [![Selecione uma opção de importação de Fonte de Evento](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png#lightbox)

    *  A tabela seguinte descreve as propriedades necessárias para o Hub de Eventos de Utilização a partir da opção de **subscrição disponível:**

       [![Detalhes do centro de subscrição e evento](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png#lightbox)

       | Propriedade | Descrição |
       | --- | --- |
       | Subscrição | A subscrição a que o centro de eventos pretendido e o espaço de nome saem. |
       | Espaço de nomes do hub de eventos | O espaço de nome do centro de eventos a que o centro de eventos pretendido pertence. |
       | O nome do hub de eventos | O nome da instância do centro de eventos pretendido. |
       | Valor da política do Hub de Eventos | Selecione a política de acesso partilhado desejada. Pode criar a política de acesso partilhado no separador **Configure** do centro de eventos. Cada política de acesso partilhado tem um nome, permissões que definiu e chaves de acesso. A política de acesso partilhado para a sua fonte de evento *deve* ter **lido** permissões. |
       | Chave política do Hub de Eventos | Pré-povoado do valor da política selecionada do Event Hub. |

    * Se o centro de eventos for externo às suas subscrições ou se pretender selecionar opções avançadas, selecione fornecer as definições do Hub de **Eventos manualmente**.

       A tabela seguinte descreve as propriedades necessárias para as **definições do 'Provide Event Hub' manualmente:**
 
       | Propriedade | Descrição |
       | --- | --- |
       | ID da subscrição | A subscrição a que o centro de eventos pretendido e o espaço de nome saem. |
       | Grupo de recursos | O grupo de recursos a instância de centro de eventos desejada e espaço de nome pertence. |
       | Espaço de nomes do hub de eventos | O espaço de nome do centro de eventos a que o centro de eventos pretendido pertence. |
       | O nome do hub de eventos | O nome da instância do centro de eventos pretendido. |
       | Valor da política do Hub de Eventos | Selecione a política de acesso partilhado desejada. Pode criar a política de acesso partilhado no separador **Configure** do centro de eventos. Cada política de acesso partilhado tem um nome, permissões que definiu e chaves de acesso. A política de acesso partilhado para a sua fonte de evento *deve* ter **lido** permissões. |
       | Chave política do Hub de Eventos | A chave de acesso partilhada que é usada para autenticar o acesso ao espaço de nome do Ônibus de serviço. Insira a chave primária ou secundária aqui. |

    * Ambas as opções partilham as seguintes opções de configuração:

       | Propriedade | Descrição |
       | --- | --- |
       | Grupo de consumidor do Hub de Eventos | O grupo de consumidores que lê os eventos do centro do evento. Recomendamos vivamente que utilize um grupo de consumidores dedicado para a sua fonte de evento. |
       | Formato de serialização de eventos | Atualmente, a JSON é o único formato de serialização disponível. As mensagens de evento devem estar neste formato ou os dados não podem ser lidos. |
       | Nome da propriedade Carimbo de data/hora | Para determinar este valor, é necessário compreender o formato de mensagem dos dados de mensagem que são enviados para o centro do evento. Este valor é o **nome** da propriedade específica do evento nos dados da mensagem que pretende utilizar como carimbo temporal do evento. O valor é sensível ao caso. Se ficar em branco, o **evento enfila** o tempo na fonte do evento é usado como carimbo de tempo do evento. |

1. Adicione o nome de grupo de consumidores dedicado time series Insights que adicionou ao seu centro de eventos.

1. Selecione **Criar**.

   Após a criação da fonte do evento, a Time Series Insights começa automaticamente a transmitir dados para o seu ambiente.

## <a name="next-steps"></a>Passos seguintes

* [Defina políticas de acesso](time-series-insights-data-access.md) a dados para proteger os dados.

* [Envie eventos](time-series-insights-send-events.md) para a fonte do evento.

* Aceda ao seu ambiente no explorador time [series Insights](https://insights.timeseries.azure.com).
