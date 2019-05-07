---
title: Adicionar uma origem de eventos de Hubs de eventos para o Azure Time Series Insights | Documentos da Microsoft
description: Este artigo descreve como adicionar uma origem de evento que está ligada ao Event Hubs do Azure para o seu ambiente do Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/01/2019
ms.custom: seodec18
ms.openlocfilehash: 41d3e72d978a210c2d68365ade5d8cb42c24aad5
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65147606"
---
# <a name="add-an-event-hub-event-source-to-your-time-series-insights-environment"></a>Adicionar uma origem de evento do hub de eventos ao seu ambiente do Time Series Insights

Este artigo descreve como utilizar o portal do Azure para adicionar uma origem de evento que lê dados a partir dos Hubs de eventos do Azure para o seu ambiente do Azure Time Series Insights.

> [!NOTE]
> Os passos descritos neste artigo aplicam-se tanto a disponibilidade geral de informações de série de tempo e ambientes de pré-visualização do Time Series Insights.

## <a name="prerequisites"></a>Pré-requisitos

- Criar um ambiente do Time Series Insights, conforme descrito em [criar um ambiente do Azure Time Series Insights](./time-series-insights-update-create-environment.md).
- Crie um hub de eventos. Ver [para criar um espaço de nomes de Hubs de eventos e um hub de eventos com o portal do Azure](../event-hubs/event-hubs-create.md).
- O hub de eventos tem de ter eventos de mensagem ativa, enviados para o mesmo. Saiba como [enviar eventos para Hubs de eventos do Azure com o .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Crie um grupo de consumidores dedicado no hub de eventos que o ambiente do Time Series Insights pode consumir de. Cada origem de eventos do Time Series Insights tem de ter seu próprio grupo de consumidores dedicado que não seja partilhado com qualquer outro tipo de consumidor. Se vários leitores consumirem eventos a partir do mesmo grupo de consumidores, todos os leitores provável ver falhas. Existe um limite de 20 grupos de consumidores do hub de eventos. Para obter detalhes, consulte a [guia de programação dos Hubs de eventos](../event-hubs/event-hubs-programming-guide.md).

### <a name="add-a-consumer-group-to-your-event-hub"></a>Adicionar um grupo de consumidores ao seu hub de eventos

Aplicações utilizam grupos de consumidores para extrair os dados dos Hubs de eventos do Azure. Forneça um grupo de consumidores dedicado para utilização apenas por este ambiente do Time Series Insights, fiável ler dados a partir do seu hub de eventos.

Para adicionar um novo grupo de consumidores do seu hub de eventos:

1. No portal do Azure, localize e abra o hub de eventos.

1. Sob **entidades**, selecione **grupos de consumidores**e, em seguida, selecione **grupo de consumidores**.

   [![Hub de eventos - adicionar um grupo de consumidores](media/time-series-insights-how-to-add-an-event-source-eventhub/5-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/5-event-hub-consumer-group.png#lightbox)

1. Sobre o **grupos de consumidores** página, introduza um novo valor exclusivo para **nome**.  Utilize este nome de mesmo quando cria uma nova origem de evento no ambiente do Time Series Insights.

1. Selecione **Criar**.

## <a name="add-a-new-event-source"></a>Adicionar uma nova origem de evento

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Localize o seu ambiente do Time Series Insights existente. No menu da esquerda, selecione **todos os recursos**e, em seguida, selecione o seu ambiente do Time Series Insights.

1. Sob **topologia do ambiente**, selecione **origens de eventos**e, em seguida, selecione **Add**.

   [![Em origens de eventos, selecione o botão Adicionar](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-sources.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-sources.png#lightbox)

1. Introduza um valor para **nome da origem de evento** que é exclusivo para este ambiente do Time Series Insights, tal como **fluxo de eventos**.

1. Para **origem**, selecione **Hub de eventos**.

1. Selecione os valores adequados para **importar opção**:
   - Se tiver um hub de eventos existente em uma das suas subscrições, selecione **Hub de eventos de utilização de subscrições disponíveis**. Esta opção é a abordagem mais fácil.
   - Se o hub de eventos é externo para as suas subscrições ou se pretender selecionar opções avançadas, selecione **definições do Hub de eventos de fornecer manualmente**.

   [![No novo painel de origem do evento, introduza os valores para os primeiros três parâmetros](media/time-series-insights-how-to-add-an-event-source-eventhub/2-import-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/2-import-option.png#lightbox)

1. A tabela seguinte descreve as propriedades necessárias para o **Hub de eventos de utilização de subscrições disponíveis** opção:

   [![Detalhes de hub de eventos e de subscrição](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png#lightbox)

   | Propriedade | Descrição |
   | --- | --- |
   | ID da subscrição | Selecione a subscrição na qual este hub de eventos foi criado.
   | Espaço de nomes do Service Bus | Selecione o espaço de nomes do Service bus do Azure que contém o hub de eventos.
   | Nome do hub de eventos | Selecione o nome do hub de eventos.
   | Nome de política do hub de eventos | Selecione a política de acesso partilhado. Pode criar a política de acesso partilhado no hub de eventos **configurar** separador. Cada política de acesso partilhado tem um nome, as permissões que definir e chaves de acesso. A política de acesso partilhado para a origem do evento *tem* ter **ler** permissões.
   | Chave de política do hub de eventos | O valor da chave pode ser pré-preenchidas.
   | Grupo de consumidores do hub de eventos | O grupo de consumidores lê eventos do hub de eventos. Recomendamos vivamente que utilize um grupo de consumidores dedicado para a origem do evento. |
   | Formato de serialização de eventos | Atualmente, o JSON é o formato de serialização só estão disponíveis. Mensagens de evento têm de estar no seguinte formato ou dados não podem ser lidos. |
   | Nome da propriedade Timestamp | Para determinar este valor, precisa entender o formato de mensagem dos dados da mensagem que são enviados para o hub de eventos. Este valor é o **nome** da propriedade de evento específico dos dados de mensagem que pretende utilizar como o carimbo de hora do evento. O valor diferencia maiúsculas de minúsculas. Se deixado em branco, o **tempo de colocar em fila de eventos** de eventos de origem é utilizada como o carimbo de hora do evento. |

1. A tabela seguinte descreve as propriedades necessárias para o **definições do Hub de eventos de fornecer manualmente** opção:

   | Propriedade | Descrição |
   | --- | --- |
   | ID da subscrição | A subscrição na qual este hub de eventos foi criado.
   | Grupo de recursos | O grupo de recursos no qual este hub de eventos foi criado.
   | Espaço de nomes do Service Bus | Um espaço de nomes do Service Bus é um contentor para um conjunto de entidades de mensagens. Quando tiver criado um novo hub de eventos, também criou um espaço de nomes do Service Bus.
   | Nome do hub de eventos | O nome do seu hub de eventos. Quando criou o seu hub de eventos, também lhe atribuiu um nome específico.
   | Nome de política do hub de eventos | A política de acesso partilhado. Pode criar uma política de acesso partilhado no hub de eventos **configurar** separador. Cada política de acesso partilhado tem um nome, as permissões que definir e chaves de acesso. A política de acesso partilhado para a origem do evento *tem* ter **ler** permissões.
   | Chave de política do hub de eventos | A chave de acesso partilhado que é utilizada para autenticar o acesso ao espaço de nomes do Service Bus. Introduza a chave primária ou secundária aqui.
   | Grupo de consumidores do hub de eventos | O grupo de consumidores lê eventos do hub de eventos. Recomendamos vivamente que utilize um grupo de consumidores dedicado para a origem do evento.
   | Formato de serialização de eventos | Atualmente, o JSON é o formato de serialização só estão disponíveis. Mensagens de evento têm de estar no seguinte formato ou dados não podem ser lidos. |
   | Nome da propriedade Timestamp | Para determinar este valor, precisa entender o formato de mensagem dos dados da mensagem que são enviados para o hub de eventos. Este valor é o **nome** da propriedade de evento específico dos dados de mensagem que pretende utilizar como o carimbo de hora do evento. O valor diferencia maiúsculas de minúsculas. Se deixado em branco, o **tempo de colocar em fila de eventos** de eventos de origem é utilizada como o carimbo de hora do evento. |

1. Adicione o nome de grupo consumidor dedicado Time Series Insights que adicionou ao seu hub de eventos.

1. Selecione **Criar**.

   [![Selecione criar](media/time-series-insights-how-to-add-an-event-source-eventhub/4-create-button.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/4-create-button.png#lightbox)

   Depois de criar a origem do evento, o Time Series Insights inicia automaticamente a transmissão em fluxo de dados ao seu ambiente.

## <a name="next-steps"></a>Passos Seguintes

* [Definir políticas de acesso de dados](time-series-insights-data-access.md) para proteger os dados.

* [Enviar eventos](time-series-insights-send-events.md) para a origem do evento.

* Aceder ao seu ambiente no [Explorador do Time Series Insights](https://insights.timeseries.azure.com).
