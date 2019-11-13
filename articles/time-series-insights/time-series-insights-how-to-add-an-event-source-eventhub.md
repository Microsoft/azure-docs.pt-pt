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
ms.date: 10/09/2019
ms.custom: seodec18
ms.openlocfilehash: 421a4635a80c5a7a45fb14bf900c205a06789279
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012624"
---
# <a name="add-an-event-hub-event-source-to-your-time-series-insights-environment"></a>Adicionar uma origem de evento do hub de eventos ao seu ambiente do Time Series Insights

Este artigo descreve como utilizar o portal do Azure para adicionar uma origem de evento que lê dados a partir dos Hubs de eventos do Azure para o seu ambiente do Azure Time Series Insights.

> [!NOTE]
> As etapas descritas neste artigo se aplicam aos ambientes Time Series Insights GA e Time Series Insights Preview.

## <a name="prerequisites"></a>Pré-requisitos

- Crie um ambiente de Time Series Insights conforme descrito em [criar um ambiente de Azure Time Series insights](./time-series-insights-update-create-environment.md).
- Crie um hub de eventos. Consulte [criar um namespace de hubs de eventos e um hub de eventos usando o portal do Azure](../event-hubs/event-hubs-create.md).
- O hub de eventos tem de ter eventos de mensagem ativa, enviados para o mesmo. Saiba como [enviar eventos para os hubs de eventos do Azure usando o .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Crie um grupo de consumidores dedicado no hub de eventos que o ambiente do Time Series Insights pode consumir de. Cada origem de eventos do Time Series Insights tem de ter seu próprio grupo de consumidores dedicado que não seja partilhado com qualquer outro tipo de consumidor. Se vários leitores consumirem eventos a partir do mesmo grupo de consumidores, todos os leitores provável ver falhas. Existe um limite de 20 grupos de consumidores do hub de eventos. Para obter detalhes, consulte a [guia de programação dos Hubs de eventos](../event-hubs/event-hubs-programming-guide.md).

### <a name="add-a-consumer-group-to-your-event-hub"></a>Adicionar um grupo de consumidores ao seu hub de eventos

Aplicações utilizam grupos de consumidores para extrair os dados dos Hubs de eventos do Azure. Para ler dados de forma confiável do seu hub de eventos, forneça um grupo de consumidores dedicado que é usado somente por esse ambiente de Time Series Insights.

Para adicionar um novo grupo de consumidores do seu hub de eventos:

1. Na [portal do Azure](https://portal.azure.com), localize e abra o Hub de eventos do namespace do hub de eventos.

    [![abrir o namespace do hub de eventos](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-hub-namespace.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-hub-namespace.png#lightbox)

1. Sob **entidades**, selecione **grupos de consumidores**e, em seguida, selecione **grupo de consumidores**.

   [![Hub de eventos-adicionar um grupo de consumidores](media/time-series-insights-how-to-add-an-event-source-eventhub/2-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/2-event-hub-consumer-group.png#lightbox)

1. Sobre o **grupos de consumidores** página, introduza um novo valor exclusivo para **nome**.  Utilize este nome de mesmo quando cria uma nova origem de evento no ambiente do Time Series Insights.

1. Selecione **Criar**.

## <a name="add-a-new-event-source"></a>Adicionar uma nova origem de evento

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).

1. Localize o seu ambiente do Time Series Insights existente. No menu da esquerda, selecione **todos os recursos**e, em seguida, selecione o seu ambiente do Time Series Insights.

1. Sob **topologia do ambiente**, selecione **origens de eventos**e, em seguida, selecione **Add**.

   [![em origens do evento, selecione o botão Adicionar](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png#lightbox)

1. Introduza um valor para **nome da origem de evento** que é exclusivo para este ambiente do Time Series Insights, tal como **fluxo de eventos**.

1. Para **origem**, selecione **Hub de eventos**.

1. Selecione os valores adequados para **importar opção**:

   * Se tiver um hub de eventos existente em uma das suas subscrições, selecione **Hub de eventos de utilização de subscrições disponíveis**. Esta opção é a abordagem mais fácil.

     [![selecionar uma opção de importação de origem de evento](media/time-series-insights-how-to-add-an-event-source-eventhub/4-select-an-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/4-select-an-option.png#lightbox)

    *  A tabela seguinte descreve as propriedades necessárias para o **Hub de eventos de utilização de subscrições disponíveis** opção:

       [detalhes da assinatura ![e do hub de eventos](media/time-series-insights-how-to-add-an-event-source-eventhub/5-create-button.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/5-create-button.png#lightbox)

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
       | ID da subscrição | A assinatura à qual a instância do hub de eventos desejada e o namespace pertencem. |
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
