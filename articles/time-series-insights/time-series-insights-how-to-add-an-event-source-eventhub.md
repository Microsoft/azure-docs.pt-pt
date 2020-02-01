---
title: Adicionar um evento de hubs de eventos fonte-Azure Time Series Insights | Microsoft Docs
description: Saiba como adicionar uma fonte de eventos de hubs de eventos do Azure ao seu ambiente de Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/30/2020
ms.custom: seodec18
ms.openlocfilehash: c3b06289ba6ce98d4307a8255981ecdba069fdfa
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905403"
---
# <a name="add-an-event-hub-event-source-to-your-time-series-insights-environment"></a>Adicionar uma origem de evento do hub de eventos ao seu ambiente do Time Series Insights

Este artigo descreve como utilizar o portal do Azure para adicionar uma origem de evento que lê dados a partir dos Hubs de eventos do Azure para o seu ambiente do Azure Time Series Insights.

> [!NOTE]
> As etapas descritas neste artigo se aplicam aos ambientes Time Series Insights GA e Time Series Insights Preview.

## <a name="prerequisites"></a>Pré-requisitos

- Crie um ambiente de Time Series Insights conforme descrito em [criar um ambiente de Azure Time Series insights](./time-series-insights-update-create-environment.md).
- Crie um hub de eventos. Leia [criar um namespace de hubs de eventos e um hub de eventos usando o portal do Azure](../event-hubs/event-hubs-create.md).
- O hub de eventos tem de ter eventos de mensagem ativa, enviados para o mesmo. Saiba como [enviar eventos para os hubs de eventos do Azure usando o .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Crie um grupo de consumidores dedicado no hub de eventos que o ambiente do Time Series Insights pode consumir de. Cada origem de eventos do Time Series Insights tem de ter seu próprio grupo de consumidores dedicado que não seja partilhado com qualquer outro tipo de consumidor. Se vários leitores consomem eventos do mesmo grupo de consumidores, é provável que todos os leitores apresentem falhas. Existe um limite de 20 grupos de consumidores do hub de eventos. Para obter detalhes, leia o [Guia de programação dos hubs de eventos](../event-hubs/event-hubs-programming-guide.md).

### <a name="add-a-consumer-group-to-your-event-hub"></a>Adicionar um grupo de consumidores ao seu hub de eventos

Aplicações utilizam grupos de consumidores para extrair os dados dos Hubs de eventos do Azure. Para ler dados de forma confiável do seu hub de eventos, forneça um grupo de consumidores dedicado que é usado somente por esse ambiente de Time Series Insights.

Para adicionar um novo grupo de consumidores do seu hub de eventos:

1. Na [portal do Azure](https://portal.azure.com), localize e abra sua instância do hub de eventos no painel **visão geral** do seu namespace do hub de eventos. Selecione **entidades > hubs de eventos** ou localize sua instância em **nome**.

    [![Abra o seu espaço de nome sem nome do evento](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png#lightbox)

1. Na sua instância do hub de eventos, selecione **entidades > grupos de consumidores**. Em seguida, selecione **+ grupo de consumidores** para adicionar um novo grupo de consumidores. 

   [![Event hub - Adicione um grupo de consumidores](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png#lightbox)

   Caso contrário, selecione um grupo de consumidores existente e pule para a próxima seção.

1. Sobre o **grupos de consumidores** página, introduza um novo valor exclusivo para **nome**.  Utilize este nome de mesmo quando cria uma nova origem de evento no ambiente do Time Series Insights.

1. Selecione **Criar**.

## <a name="add-a-new-event-source"></a>Adicionar uma nova origem de evento

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

1. Localize o seu ambiente do Time Series Insights existente. No menu da esquerda, selecione **todos os recursos**e, em seguida, selecione o seu ambiente do Time Series Insights.

1. Selecione **origens do evento**e, em seguida, selecione **Adicionar**.

   [![Sob Fontes de Eventos, selecione o botão Adicionar](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png#lightbox)

1. Insira um valor para o **nome de origem do evento** que seja exclusivo para esse time Series insights ambiente, como `Contoso-TSI-GA-Event-Hub-ES`.

1. Para **origem**, selecione **Hub de eventos**.

1. Selecione os valores adequados para **importar opção**:

   * Se tiver um hub de eventos existente em uma das suas subscrições, selecione **Hub de eventos de utilização de subscrições disponíveis**. Esta opção é a abordagem mais fácil.

     [![Selecione uma opção de importação de Fonte de Evento](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png#lightbox)

    *  A tabela seguinte descreve as propriedades necessárias para o **Hub de eventos de utilização de subscrições disponíveis** opção:

       [detalhes de ![de subscrição e centro de eventos](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png#lightbox)

       | Propriedade | Descrição |
       | --- | --- |
       | Subscrição | A assinatura à qual a instância do hub de eventos desejada e o namespace pertencem. |
       | Espaço de nomes do hub de eventos | O namespace do hub de eventos ao qual a instância do hub de eventos desejada pertence. |
       | O nome do hub de eventos | O nome da instância do hub de eventos desejada. |
       | Valor da política do hub de eventos | Selecione a política de acesso compartilhado desejada. Você pode criar a política de acesso compartilhado na guia **Configurar** o Hub de eventos. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. A política de acesso partilhado para a origem do evento *tem* ter **ler** permissões. |
       | Chave da política do Hub de Eventos | Preenchido previamente com base no valor da política do hub de eventos selecionado. |

    * Se o hub de eventos é externo para as suas subscrições ou se pretender selecionar opções avançadas, selecione **definições do Hub de eventos de fornecer manualmente**.

       A tabela seguinte descreve as propriedades necessárias para o **definições do Hub de eventos de fornecer manualmente** opção:
 
       | Propriedade | Descrição |
       | --- | --- |
       | ID de Subscrição | A assinatura à qual a instância do hub de eventos desejada e o namespace pertencem. |
       | Grupo de recursos | O grupo de recursos ao qual a instância do hub de eventos desejada e o namespace pertencem. |
       | Espaço de nomes do hub de eventos | O namespace do hub de eventos ao qual a instância do hub de eventos desejada pertence. |
       | O nome do hub de eventos | O nome da instância do hub de eventos desejada. |
       | Valor da política do hub de eventos | Selecione a política de acesso compartilhado desejada. Você pode criar a política de acesso compartilhado na guia **Configurar** o Hub de eventos. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. A política de acesso partilhado para a origem do evento *tem* ter **ler** permissões. |
       | Chave da política do Hub de Eventos | A chave de acesso partilhado que é utilizada para autenticar o acesso ao espaço de nomes do Service Bus. Introduza a chave primária ou secundária aqui. |

    * Ambas as opções compartilham as seguintes opções de configuração:

       | Propriedade | Descrição |
       | --- | --- |
       | Grupo de consumidores do hub de eventos | O grupo de consumidores lê eventos do hub de eventos. Recomendamos vivamente que utilize um grupo de consumidores dedicado para a origem do evento. |
       | Formato de serialização de eventos | Atualmente, o JSON é o formato de serialização só estão disponíveis. As mensagens de evento devem estar nesse formato ou os dados não podem ser lidos. |
       | Nome da propriedade Timestamp | Para determinar este valor, precisa entender o formato de mensagem dos dados da mensagem que são enviados para o hub de eventos. Este valor é o **nome** da propriedade de evento específico dos dados de mensagem que pretende utilizar como o carimbo de hora do evento. O valor diferencia maiúsculas de minúsculas. Se deixado em branco, o **tempo de colocar em fila de eventos** de eventos de origem é utilizada como o carimbo de hora do evento. |

1. Adicione o nome de grupo consumidor dedicado Time Series Insights que adicionou ao seu hub de eventos.

1. Selecione **Criar**.

   Depois que a origem do evento é criada, Time Series Insights automaticamente começa a transmitir dados para o seu ambiente.

## <a name="next-steps"></a>Passos seguintes

* [Definir políticas de acesso de dados](time-series-insights-data-access.md) para proteger os dados.

* [Enviar eventos](time-series-insights-send-events.md) para a origem do evento.

* Aceder ao seu ambiente no [Explorador do Time Series Insights](https://insights.timeseries.azure.com).
