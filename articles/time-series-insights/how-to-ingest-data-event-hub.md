---
title: Adicione uma fonte de eventos hubs de evento - Azure Time Series Insights | Microsoft Docs
description: Saiba como adicionar uma fonte de eventos Azure Event Hubs ao seu ambiente Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/21/2021
ms.custom: seodec18
ms.openlocfilehash: ee66e68216933c410092865a1cdb781476a944c6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103461139"
---
# <a name="add-an-event-hub-event-source-to-your-azure-time-series-insights-environment"></a>Adicione uma fonte de eventos hub de evento ao seu ambiente Azure Time Series Insights

Este artigo descreve como usar o portal Azure para adicionar uma fonte de evento que lê dados dos Azure Event Hubs para o seu ambiente Azure Time Series Insights.

> [!NOTE]
> Os passos descritos neste artigo aplicam-se tanto aos ambientes Azure Time Series Insights Gen 1 e Azure Time Series Insights Gen 2.

## <a name="prerequisites"></a>Pré-requisitos

- Crie um ambiente Azure Time Series Insights, conforme descrito no [Criar um ambiente de Insights de Séries de Tempo Azure](./tutorial-set-up-environment.md).
- Crie um hub de eventos. Leia [Criar um espaço de nomes de Centros de Eventos e um centro de eventos utilizando o portal Azure](../event-hubs/event-hubs-create.md).
- O centro de eventos deve ter eventos de mensagens ativas enviados para ele. Saiba como [enviar eventos para Azure Event Hubs utilizando o quadro .NET](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Crie um grupo de consumidores dedicado no centro de eventos que o ambiente Azure Time Series Insights pode consumir. Cada fonte de eventos Azure Time Series Insights deve ter o seu próprio grupo de consumidores dedicado que não é partilhado com nenhum outro consumidor. Se vários leitores consumirem eventos do mesmo grupo de consumidores, todos os leitores são suscetíveis de apresentar falhas. Há um limite de 20 grupos de consumidores por centro de eventos. Para mais detalhes, leia o guia de programação do [Event Hubs.](../event-hubs/event-hubs-programming-guide.md)

### <a name="add-a-consumer-group-to-your-event-hub"></a>Adicione um grupo de consumidores ao seu centro de eventos

As aplicações utilizam grupos de consumidores para extrair dados dos Hubs de Eventos Azure. Para ler de forma fiável os dados do seu centro de eventos, forneça um grupo de consumidores dedicado que é usado apenas por este ambiente Azure Time Series Insights.

Para adicionar um novo grupo de consumidores no seu centro de eventos:

1. No [portal Azure,](https://portal.azure.com)localize e abra o seu centro de eventos a partir do painel de **nomes** do seu centro de eventos. Selecione **Entidades > Centros de Eventos** ou encontre a sua instância em **Nome**.

    [![Abra o seu espaço de nome de centro de eventos](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png#lightbox)

1. Na sua instância de hub de evento, selecione **Entidades > Grupos de Consumidores.** Em seguida, selecione **+ Grupo de consumidores** para adicionar um novo grupo de consumidores.

   [![Centro de eventos - Adicione um grupo de consumidores](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png#lightbox)

   Caso contrário, selecione um grupo de consumidores existente e salte para a secção seguinte.

1. Na página **de grupos de consumidores,** insira um novo valor único para **o Nome.**  Use este mesmo nome quando criar uma nova fonte de evento no ambiente Azure Time Series Insights.

1. Selecione **Criar**.

## <a name="add-a-new-event-source"></a>Adicione uma nova fonte de evento

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Localize o ambiente existente da Série de TempoS Azure Insights. No menu esquerdo, selecione **Todos os recursos** e, em seguida, selecione o ambiente Azure Time Series Insights.

1. Selecione **Fontes de Eventos** e, em seguida, selecione **Adicionar**.

   [![Em Fontes de Evento, selecione o botão Adicionar](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png#lightbox)

1. Introduza um valor para **o nome de origem do Evento** que seja exclusivo deste ambiente Azure Time Series Insights, como `Contoso-TSI-Gen 1-Event-Hub-ES` .

1. Para **obter**, selecione **Event Hub**.

1. Selecione os valores adequados para **a opção Importação**:

   - Se tiver um centro de eventos existente numa das suas subscrições, selecione **Use Event Hub a partir de subscrições disponíveis.** Esta opção é a abordagem mais fácil.

     [![Selecione uma opção de importação de Fonte de Evento](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png#lightbox)

   - A tabela a seguir descreve as propriedades necessárias para o Centro de Eventos de Utilização a partir da opção **de subscrições disponíveis:**

       [![Detalhes do centro de subscrição e eventos](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png#lightbox)

       | Propriedade | Descrição |
       | --- | --- |
       | Subscrição | A subscrição da instância do centro de eventos desejada e o espaço de nome pertence. |
       | Espaço de nomes do hub de eventos | O espaço de nome do centro de eventos pretendido pertence ao centro de eventos. |
       | Nome do Hub de Eventos | O nome da instância do centro de eventos desejado. |
       | Valor da política do Centro de Eventos | Selecione a política de acesso partilhado pretendido. Pode criar a política de acesso partilhado no separador Configurar do centro de **eventos.** Cada política de acesso partilhado tem um nome, permissões que definiu e chaves de acesso. A política de acesso partilhado para a sua fonte de *eventos deve* ter **lido** permissões. |
       | Chave política do Centro de Eventos | Pré-povoado do valor de política do Event Hub selecionado. |

   - Se o centro de eventos for externo às suas subscrições ou se pretender selecionar opções avançadas, selecione **configurações do Centro de Eventos manualmente**.

       A tabela a seguir descreve as propriedades necessárias para a opção **"Fornecer Centros de Eventos" manualmente:**

       | Propriedade | Descrição |
       | --- | --- |
       | ID da subscrição | A subscrição da instância do centro de eventos desejada e o espaço de nome pertence. |
       | Grupo de recursos | O grupo de recursos a instância do centro de eventos desejada e o espaço de nome pertence. |
       | Espaço de nomes do hub de eventos | O espaço de nome do centro de eventos pretendido pertence ao centro de eventos. |
       | Nome do Hub de Eventos | O nome da instância do centro de eventos desejado. |
       | Valor da política do Centro de Eventos | Selecione a política de acesso partilhado pretendido. Pode criar a política de acesso partilhado no separador Configurar do centro de **eventos.** Cada política de acesso partilhado tem um nome, permissões que definiu e chaves de acesso. A política de acesso partilhado para a sua fonte de *eventos deve* ter **lido** permissões. |
       | Chave política do Centro de Eventos | A chave de acesso partilhada que é usada para autenticar o acesso ao espaço de nomes do Service Bus. Introduza a chave primária ou secundária aqui. |

   - Ambas as opções partilham as seguintes opções de configuração:

       | Propriedade | Descrição |
       | --- | --- |
       | Grupo de consumidor do Hub de Eventos | O grupo de consumidores que lê eventos do centro de eventos. Recomendamos vivamente que utilize um grupo de consumidores dedicado para a sua fonte de eventos. |
       | Formato de serialização de eventos | Atualmente, o JSON é o único formato de serialização disponível. As mensagens de evento devem estar neste formato ou os dados não podem ser lidos. |
       | Nome da propriedade Carimbo de data/hora | Para determinar este valor, é necessário compreender o formato de mensagem dos dados de mensagem enviados para o centro de eventos. Este valor é o **nome** da propriedade específica do evento nos dados de mensagem que pretende utilizar como o tempo de marcação do evento. O valor é sensível a casos. Se ficar em branco, o **tempo de encade de evento** na fonte do evento é usado como o tempo de tempo do evento. |

1. Adicione o nome dedicado do grupo de consumidores Azure Time Series Insights que adicionou ao seu centro de eventos.

1. Selecione **Criar**.

   Após a criação da fonte do evento, o Azure Time Series Insights começa automaticamente a transmitir dados para o seu ambiente.

## <a name="next-steps"></a>Passos seguintes

- [Defina políticas de acesso](./concepts-access-policies.md) a dados para proteger os dados.

- [Envie eventos](time-series-insights-send-events.md) para a fonte do evento.

- Aceda ao seu ambiente no [Azure Time Series Insights Explorer](https://insights.timeseries.azure.com).
