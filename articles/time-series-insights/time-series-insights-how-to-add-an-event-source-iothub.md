---
title: Como adicionar uma fonte de evento do hub IoT - Azure Time Series Insights Microsoft Docs
description: Saiba como adicionar uma fonte de evento de hub IoT ao seu ambiente time series Insights.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81538108"
---
# <a name="add-an-iot-hub-event-source-to-your-time-series-insights-environment"></a>Adicione uma fonte de evento de hub IoT ao seu ambiente de Insights de Séries Tempotamos

Este artigo descreve como usar o portal Azure para adicionar uma fonte de evento que lê dados do Azure IoT Hub para o seu ambiente Azure Time Series Insights.

> [!NOTE]
> As instruções deste artigo aplicam-se tanto aos ambientes de visualização de Insights GA da Série Temporal Azure como aos ambientes de visualização de insights de séries temporizadas.

## <a name="prerequisites"></a>Pré-requisitos

* Criar um [ambiente Azure Time Series Insights](time-series-insights-update-create-environment.md).
* Crie um [hub IoT utilizando o portal Azure](../iot-hub/iot-hub-create-through-portal.md).
* O hub IoT deve ter eventos de mensagens ativos sendo enviados.
* Crie um grupo de consumidores dedicado no centro IoT para o ambiente Time Series Insights para consumir. Cada fonte de eventos da Time Series Insights deve ter o seu próprio grupo de consumidores dedicado que não é partilhado com nenhum outro consumidor. Se vários leitores consumirem eventos do mesmo grupo de consumidores, todos os leitores são suscetíveis de apresentar falhas. Para mais detalhes, leia o guia de desenvolvimento do [Azure IoT Hub](../iot-hub/iot-hub-devguide.md).

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Adicione um grupo de consumidores ao seu hub IoT

As aplicações utilizam grupos de consumidores para extrair dados do Azure IoT Hub. Para ler de forma fiável os dados do seu hub IoT, forneça um grupo de consumidores dedicado que é usado apenas por este ambiente Time Series Insights.

Para adicionar um novo grupo de consumidores ao seu hub IoT:

1. No [portal Azure,](https://portal.azure.com)encontre e abra o seu hub IoT.

1. Em **Definições**, selecione **Pontos finais incorporados**e, em seguida, selecione o ponto final **de Eventos.**

   [![Na página 'Pontos finais', selecione o botão Eventos](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-connect-iot-hub.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-connect-iot-hub.png#lightbox)

1. Nos **grupos de consumidores,** insira um nome único para o grupo de consumidores. Use este mesmo nome no seu ambiente Time Series Insights quando criar uma nova fonte de evento.

1. Selecione **Guardar**.

## <a name="add-a-new-event-source"></a>Adicione uma nova fonte de evento

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No menu do lado esquerdo, selecione **Todos os recursos**. Selecione o seu ambiente do Time Series Insights.

1. Em **Definições**, selecione **Fontes de Eventos**e, em seguida, selecione **Adicionar**.

   [![Selecione Fontes de Evento e, em seguida, selecione o botão Adicionar](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-add-event-source.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-add-event-source.png#lightbox)

1. No **painel de origem** do novo evento, para **o nome de origem do Evento,** insira um nome exclusivo deste ambiente Time Series Insights. Por exemplo, insira **o fluxo de eventos**.

1. Para **fonte**, selecione **IoT Hub**.

1. Selecione um valor para **a opção Importação**:

   * Se já tem um hub IoT numa das suas subscrições, selecione **Use IoT Hub a partir de subscrições disponíveis.** Esta opção é a abordagem mais fácil.
   
     [![Selecione opções no painel de origem do novo evento](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-select-an-import-option.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-select-an-import-option.png#lightbox)

    * A tabela a seguir descreve as propriedades necessárias para o Use IoT Hub a partir da opção **de subscrições disponíveis:**

       [![Novo painel de fonte de eventos - Propriedades a definir no Use IoT Hub a partir da opção de subscrições disponíveis](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-create-configure-confirm.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-create-configure-confirm.png#lightbox)

       | Propriedade | Descrição |
       | --- | --- |
       | Subscrição | A subscrição do iot hub desejado pertence. |
       | Nome do hub IoT | O nome do iot hub selecionado. |
       | Nome da política do hub IoT | Selecione a política de acesso partilhado. Pode encontrar a política de acesso partilhado no separador de definições do hub IoT. Cada política de acesso partilhado tem um nome, permissões que definiu e chaves de acesso. A política de acesso partilhado para a sua fonte de *eventos deve* ter permissões **de ligação de serviço.** |
       | Chave de política do hub IoT | A chave está pré-poupulada. |

    * Se o hub IoT for externo às suas subscrições ou se quiser escolher opções avançadas, selecione **manualmente as definições do Hub IoT**.

      A tabela seguinte descreve manualmente as propriedades necessárias para as **definições do Hub Provide IoT**:

       | Propriedade | Descrição |
       | --- | --- |
       | ID da subscrição | A subscrição do iot hub desejado pertence. |
       | Grupo de recursos | O nome do grupo de recursos no qual o hub IoT foi criado. |
       | Nome do hub IoT | O nome do seu centro de IoT. Quando criaste o teu hub IoT, inseriste um nome para o hub IoT. |
       | Nome da política do hub IoT | A política de acesso partilhado. Pode criar a política de acesso partilhado no separador de definições do hub IoT. Cada política de acesso partilhado tem um nome, permissões que definiu e chaves de acesso. A política de acesso partilhado para a sua fonte de *eventos deve* ter permissões **de ligação de serviço.** |
       | Chave de política do hub IoT | A chave de acesso partilhada que é usada para autenticar o acesso ao espaço de nomes do Azure Service Bus. Introduza a chave primária ou secundária aqui. |

    * Ambas as opções partilham as seguintes opções de configuração:

       | Propriedade | Descrição |
       | --- | --- |
       | Grupo de consumidores ioT hub | O grupo de consumidores que lê eventos do centro de IoT. Recomendamos vivamente que utilize um grupo de consumidores dedicado para a sua fonte de eventos. |
       | Formato de serialização de eventos | Atualmente, o JSON é o único formato de serialização disponível. As mensagens do evento devem estar neste formato ou não podem ser lidos dados. |
       | Nome da propriedade Carimbo de data/hora | Para determinar este valor, é necessário compreender o formato de mensagem dos dados de mensagem enviados para o hub IoT. Este valor é o **nome** da propriedade específica do evento nos dados de mensagem que pretende utilizar como o tempo de marcação do evento. O valor é sensível a casos. Se ficar em branco, o **tempo de encade de evento** na fonte do evento é usado como o tempo de tempo do evento. |


1. Adicione o nome dedicado do grupo de consumidores Time Series Insights que adicionou ao seu hub IoT.

1. Selecione **Criar**.

1. Depois de criar a fonte de evento, o Time Series Insights inicia automaticamente o streaming de dados para o seu ambiente.

## <a name="next-steps"></a>Próximos passos

* [Defina políticas de acesso](time-series-insights-data-access.md) a dados para proteger os dados.

* [Envie eventos](time-series-insights-send-events.md) para a fonte do evento.

* Aceda ao seu ambiente no [explorador de Insights de Séries Tempo.](https://insights.timeseries.azure.com)
