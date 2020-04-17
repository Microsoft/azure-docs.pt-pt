---
title: Como adicionar uma fonte de evento de hub IoT - Azure Time Series Insights [ Microsoft Docs
description: Aprenda a adicionar uma fonte de evento de hub IoT ao seu ambiente Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/16/2020
ms.custom: seodec18
ms.openlocfilehash: a0a2f703d9224b8b9dd77c80b2b6a7faee70f5bb
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538108"
---
# <a name="add-an-iot-hub-event-source-to-your-time-series-insights-environment"></a>Adicione uma fonte de evento de hub IoT ao seu ambiente Time Series Insights

Este artigo descreve como usar o portal Azure para adicionar uma fonte de evento que lê dados do Azure IoT Hub para o seu ambiente Azure Time Series Insights.

> [!NOTE]
> As instruções deste artigo aplicam-se tanto aos insights da Série De Tempo Azure GA como aos ambientes de pré-visualização da Time Series Insights.

## <a name="prerequisites"></a>Pré-requisitos

* Crie um ambiente de Insights da [Série De Tempo Azure.](time-series-insights-update-create-environment.md)
* Crie um [hub IoT utilizando o portal Azure.](../iot-hub/iot-hub-create-through-portal.md)
* O centro ioT deve ter eventos de mensagens ativas sendo enviados.
* Crie um grupo de consumidores dedicado no hub IoT para que o ambiente Time Series Insights consuma. Cada fonte de evento da Time Series Insights deve ter o seu próprio grupo de consumidores dedicado que não é partilhado com qualquer outro consumidor. Se vários leitores consumirem eventos do mesmo grupo de consumidores, todos os leitores são suscetíveis de apresentar falhas. Para mais detalhes, leia o guia de desenvolvimento do [Azure IoT Hub](../iot-hub/iot-hub-devguide.md).

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Adicione um grupo de consumidores ao seu hub IoT

As aplicações utilizam grupos de consumidores para retirar dados do Azure IoT Hub. Para ler de forma fiável os dados do seu hub IoT, forneça um grupo de consumidores dedicado que é usado apenas por este ambiente time series Insights.

Para adicionar um novo grupo de consumidores ao seu hub IoT:

1. No [portal Azure,](https://portal.azure.com)encontre e abra o seu hub IoT.

1. Em **Definições**, selecione **Pontos Finais Incorporados**e, em seguida, selecione o ponto final do **Evento.**

   [![Na página Build-in Endpoints, selecione o botão Eventos](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-connect-iot-hub.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-connect-iot-hub.png#lightbox)

1. No âmbito dos **grupos de consumidores,** insira um nome único para o grupo de consumidores. Use este mesmo nome no ambiente time series Insights quando criar uma nova fonte de evento.

1. Selecione **Guardar**.

## <a name="add-a-new-event-source"></a>Adicione uma nova fonte de evento

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No menu do lado esquerdo, selecione **Todos os recursos**. Selecione o seu ambiente do Time Series Insights.

1. Em **Definições,** selecione **Fontes**de Eventos, e, em seguida, **selecione Adicionar**.

   [![Selecione Fontes de Evento e, em seguida, selecione o botão Adicionar](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-add-event-source.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-add-event-source.png#lightbox)

1. No novo painel de origem do **evento,** para **nome de origem do evento,** insira um nome único para este ambiente Time Series Insights. Por exemplo, introduza **o fluxo de eventos**.

1. Para **origem,** selecione **IoT Hub**.

1. Selecione um valor para **a opção Import:**

   * Se já tem um hub IoT numa das suas subscrições, selecione **Use IoT Hub a partir de subscrições disponíveis**. Esta opção é a abordagem mais fácil.
   
     [![Selecione opções no painel de origem do novo evento](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-select-an-import-option.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-select-an-import-option.png#lightbox)

    * O quadro seguinte descreve as propriedades que são necessárias para o **Hub de Utilização IoT a partir da** opção de subscrição disponível:

       [![Novo painel de fonte de eventos - Propriedades para definir no Hub use IoT a partir da opção de subscrição disponível](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-create-configure-confirm.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-create-configure-confirm.png#lightbox)

       | Propriedade | Descrição |
       | --- | --- |
       | Subscrição | A subscrição a que o centro iot desejado pertence. |
       | Nome do hub ioT | O nome do centro de iot selecionado. |
       | Nome da política do hub IoT | Selecione a política de acesso partilhado. Pode encontrar a política de acesso partilhado no separador de definições do hub IoT. Cada política de acesso partilhado tem um nome, permissões que definiu e chaves de acesso. A política de acesso partilhado para a sua fonte de evento *deve* ter permissões de **ligação do serviço.** |
       | Chave política do hub IoT | A chave está pré-povoada. |

    * Se o hub IoT for externo às suas subscrições, ou se pretender escolher opções avançadas, selecione fornecer as definições do **Hub IoT manualmente**.

      A tabela seguinte descreve manualmente as propriedades necessárias para as definições do **Hub Provide IoT:**

       | Propriedade | Descrição |
       | --- | --- |
       | ID da subscrição | A subscrição a que o centro iot desejado pertence. |
       | Grupo de recursos | O nome do grupo de recursos em que o centro IoT foi criado. |
       | Nome do hub ioT | O nome do seu centro de iot. Quando criaste o teu centro de IoT, entraste num nome para o centro ioT. |
       | Nome da política do hub IoT | A política de acesso partilhado. Pode criar a política de acesso partilhado no separador de definições do hub IoT. Cada política de acesso partilhado tem um nome, permissões que definiu e chaves de acesso. A política de acesso partilhado para a sua fonte de evento *deve* ter permissões de **ligação do serviço.** |
       | Chave política do hub IoT | A chave de acesso partilhada que é usada para autenticar o acesso ao espaço de nome do Azure Service Bus. Insira a chave primária ou secundária aqui. |

    * Ambas as opções partilham as seguintes opções de configuração:

       | Propriedade | Descrição |
       | --- | --- |
       | Grupo de consumidores hub IoT | O grupo de consumidores que lê os acontecimentos do centro ioT. Recomendamos vivamente que utilize um grupo de consumidores dedicado para a sua fonte de evento. |
       | Formato de serialização de eventos | Atualmente, a JSON é o único formato de serialização disponível. As mensagens de evento devem estar neste formato ou não se podem ler dados. |
       | Nome da propriedade Carimbo de data/hora | Para determinar este valor, é necessário compreender o formato de mensagem dos dados de mensagem que são enviados para o centro ioT. Este valor é o **nome** da propriedade específica do evento nos dados da mensagem que pretende utilizar como carimbo temporal do evento. O valor é sensível ao caso. Se ficar em branco, o **evento enfila** o tempo na fonte do evento é usado como carimbo de tempo do evento. |


1. Adicione o nome de grupo de consumidores dedicado time series Insights que adicionou ao seu hub IoT.

1. Selecione **Criar**.

1. Depois de criar a fonte do evento, a Time Series Insights inicia automaticamente o streaming de dados para o seu ambiente.

## <a name="next-steps"></a>Passos seguintes

* [Defina políticas de acesso](time-series-insights-data-access.md) a dados para proteger os dados.

* [Envie eventos](time-series-insights-send-events.md) para a fonte do evento.

* Aceda ao seu ambiente no explorador time [series Insights](https://insights.timeseries.azure.com).
