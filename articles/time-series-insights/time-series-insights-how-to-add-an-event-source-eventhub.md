---
title: Adicionar uma origem de evento dos hubs de eventos a Azure Time Series Insights | Microsoft Docs
description: Este artigo descreve como adicionar uma origem de evento que está conectada aos hubs de eventos do Azure ao seu ambiente de Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/09/2019
ms.custom: seodec18
ms.openlocfilehash: 3adf8b71d264b01f13f5aac7002b7ec455a31d60
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990069"
---
# <a name="add-an-event-hub-event-source-to-your-time-series-insights-environment"></a>Adicionar uma origem de evento do hub de eventos ao seu ambiente de Time Series Insights

Este artigo descreve como usar o portal do Azure para adicionar uma origem de evento que lê dados de hubs de eventos do Azure para seu ambiente de Azure Time Series Insights.

> [!NOTE]
> As etapas descritas neste artigo se aplicam aos ambientes Time Series Insights GA e Time Series Insights Preview.

## <a name="prerequisites"></a>Pré-requisitos

- Crie um ambiente de Time Series Insights conforme descrito em [criar um ambiente de Azure Time Series insights](./time-series-insights-update-create-environment.md).
- Crie um hub de eventos. Consulte [criar um namespace de hubs de eventos e um hub de eventos usando o portal do Azure](../event-hubs/event-hubs-create.md).
- O Hub de eventos deve ter eventos de mensagem ativos enviados a ele. Saiba como [enviar eventos para os hubs de eventos do Azure usando o .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Crie um grupo de consumidores dedicado no Hub de eventos do qual o ambiente de Time Series Insights pode consumir. Cada fonte de evento de Time Series Insights deve ter seu próprio grupo de consumidores dedicado que não é compartilhado com nenhum outro consumidor. Se vários leitores consomem eventos do mesmo grupo de consumidores, é provável que todos os leitores vejam falhas. Há um limite de 20 grupos de consumidores por Hub de eventos. Para obter detalhes, consulte o [Guia de programação dos hubs de eventos](../event-hubs/event-hubs-programming-guide.md).

### <a name="add-a-consumer-group-to-your-event-hub"></a>Adicionar um grupo de consumidores ao seu hub de eventos

Os aplicativos usam grupos de consumidores para efetuar pull de dados dos hubs de eventos do Azure. Para ler dados de forma confiável do seu hub de eventos, forneça um grupo de consumidores dedicado que é usado somente por esse ambiente de Time Series Insights.

Para adicionar um novo grupo de consumidores em seu hub de eventos:

1. Na [portal do Azure](https://portal.azure.com), localize e abra o Hub de eventos do namespace do hub de eventos.

    [![abrir o namespace do hub de eventos](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-hub-namespace.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-hub-namespace.png#lightbox)

1. Em **entidades**, selecione **grupos de consumidores**e, em seguida, selecione **grupo de consumidores**.

   [![Hub de eventos-adicionar um grupo de consumidores](media/time-series-insights-how-to-add-an-event-source-eventhub/2-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/2-event-hub-consumer-group.png#lightbox)

1. Na página **grupos de consumidores** , insira um novo valor exclusivo para **nome**.  Use esse mesmo nome ao criar uma nova origem de evento no ambiente de Time Series Insights.

1. Selecione **Criar**.

## <a name="add-a-new-event-source"></a>Adicionar uma nova origem do evento

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Localize seu ambiente de Time Series Insights existente. No menu à esquerda, selecione **todos os recursos**e, em seguida, selecione seu ambiente de time Series insights.

1. Em **topologia do ambiente**, selecione **origens do evento**e, em seguida, selecione **Adicionar**.

   [![em origens do evento, selecione o botão Adicionar](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png#lightbox)

1. Insira um valor para o **nome de origem do evento** que seja exclusivo para esse time Series insights ambiente, como **Event-Stream**.

1. Para **origem**, selecione **Hub de eventos**.

1. Selecione os valores apropriados para a **opção de importação**:

   * Se você tiver um hub de eventos existente em uma de suas assinaturas, selecione **usar o Hub de eventos das assinaturas disponíveis**. Essa opção é a abordagem mais fácil.

     [![selecionar uma opção de importação de origem de evento](media/time-series-insights-how-to-add-an-event-source-eventhub/4-select-an-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/4-select-an-option.png#lightbox)

    *  A tabela a seguir descreve as propriedades necessárias para a opção **usar o Hub de eventos das assinaturas disponíveis** :

       [detalhes da assinatura![e do hub de eventos](media/time-series-insights-how-to-add-an-event-source-eventhub/5-create-button.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/5-create-button.png#lightbox)

       | Propriedade | Descrição |
       | --- | --- |
       | Subscrição | A assinatura à qual a instância do hub de eventos desejada e o namespace pertencem. |
       | Espaço de nomes do hub de eventos | O namespace do hub de eventos ao qual a instância do hub de eventos desejada pertence. |
       | O nome do hub de eventos | O nome da instância do hub de eventos desejada. |
       | Valor da política do hub de eventos | Selecione a política de acesso compartilhado desejada. Você pode criar a política de acesso compartilhado na guia **Configurar** o Hub de eventos. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. A política de acesso compartilhado para a origem do evento *deve* ter permissões de **leitura** . |
       | Chave de política do hub de eventos | Preenchido previamente com base no valor da política do hub de eventos selecionado. |

    * Se o Hub de eventos for externo às suas assinaturas ou se você quiser selecionar opções avançadas, selecione **fornecer configurações do hub de eventos manualmente**.

       A tabela a seguir descreve as propriedades necessárias para a opção **fornecer configurações do hub de eventos manualmente** :
 
       | Propriedade | Descrição |
       | --- | --- |
       | ID de Subscrição | A assinatura à qual a instância do hub de eventos desejada e o namespace pertencem. |
       | Grupo de recursos | O grupo de recursos ao qual a instância do hub de eventos desejada e o namespace pertencem. |
       | Espaço de nomes do hub de eventos | O namespace do hub de eventos ao qual a instância do hub de eventos desejada pertence. |
       | O nome do hub de eventos | O nome da instância do hub de eventos desejada. |
       | Valor da política do hub de eventos | Selecione a política de acesso compartilhado desejada. Você pode criar a política de acesso compartilhado na guia **Configurar** o Hub de eventos. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. A política de acesso compartilhado para a origem do evento *deve* ter permissões de **leitura** . |
       | Chave de política do hub de eventos | A chave de acesso compartilhado usada para autenticar o acesso ao namespace do barramento de serviço. Insira a chave primária ou secundária aqui. |

    * Ambas as opções compartilham as seguintes opções de configuração:

       | Propriedade | Descrição |
       | --- | --- |
       | Grupo de consumidores do hub de eventos | O grupo de consumidores que lê eventos do hub de eventos. É altamente recomendável que você use um grupo de consumidores dedicado para a origem do evento. |
       | Formato de serialização de evento | Atualmente, o JSON é o único formato de serialização disponível. As mensagens de evento devem estar nesse formato ou os dados não podem ser lidos. |
       | Nome da propriedade Timestamp | Para determinar esse valor, você precisa entender o formato da mensagem dos dados da mensagem que são enviados para o Hub de eventos. Esse valor é o **nome** da propriedade de evento específica nos dados da mensagem que você deseja usar como o carimbo de data/hora do evento. O valor diferencia maiúsculas de minúsculas. Se deixado em branco, o **tempo de enfileiramento de eventos** na origem do evento será usado como o carimbo de data/hora do evento. |

1. Adicione o nome do grupo de consumidores de Time Series Insights dedicado que você adicionou ao seu hub de eventos.

1. Selecione **Criar**.

   Depois que a origem do evento é criada, Time Series Insights automaticamente começa a transmitir dados para o seu ambiente.

## <a name="next-steps"></a>Passos seguintes

* [Defina políticas de acesso de dados](time-series-insights-data-access.md) para proteger os dados.

* [Enviar eventos](time-series-insights-send-events.md) para a origem do evento.

* Acesse seu ambiente no [Time Series insights Explorer](https://insights.timeseries.azure.com).
