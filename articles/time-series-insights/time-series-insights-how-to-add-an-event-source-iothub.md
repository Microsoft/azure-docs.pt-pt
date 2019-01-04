---
title: Como adicionar uma origem de evento do hub IoT para o Azure Time Series Insights | Documentos da Microsoft
description: Este artigo descreve como adicionar uma origem de evento que está ligada a um hub IoT para o seu ambiente do Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/30/2018
ms.custom: seodec18
ms.openlocfilehash: 933d411f67655b49b4aef7bf413dfe5f87e4ff08
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/18/2018
ms.locfileid: "53556735"
---
# <a name="add-an-iot-hub-event-source-to-your-time-series-insights-environment"></a>Adicionar uma origem de evento do hub IoT ao seu ambiente do Time Series Insights

Este artigo descreve como utilizar o portal do Azure para adicionar uma origem de evento que lê dados a partir do IoT Hub do Azure para o seu ambiente do Azure Time Series Insights.

> [!NOTE]
> As instruções neste artigo aplicam-se para o Azure Time Series Insights GA e para ambientes de pré-visualização do Time Series Insights.

## <a name="prerequisites"></a>Pré-requisitos

* Criar uma [ambiente do Azure Time Series Insights](time-series-insights-update-create-environment.md).
* Criar uma [hub IoT com o portal do Azure](../iot-hub/iot-hub-create-through-portal.md).
* O hub IoT tem de ter os eventos de mensagem ativa a ser enviados na.
* Crie um grupo de consumidores dedicado no hub IoT para o ambiente do Time Series Insights consumir de. Cada origem de eventos do Time Series Insights tem de ter seu próprio grupo de consumidores dedicado que não seja partilhado com qualquer outro tipo de consumidor. Se vários leitores consumirem eventos a partir do mesmo grupo de consumidores, todos os leitores provável ver falhas. Para obter detalhes, consulte a [Guia do programador do IoT Hub do Azure](../iot-hub/iot-hub-devguide.md).

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Adicionar um grupo de consumidores ao seu hub IoT

Aplicações utilizam grupos de consumidores para extrair os dados do IoT Hub do Azure. Forneça um grupo de consumidores dedicado que é utilizado apenas por este ambiente do Time Series Insights com fiabilidade ler dados do seu hub IoT.

Para adicionar um novo grupo de consumidores ao seu hub IoT:

1. No portal do Azure, localize e abra o seu hub IoT.

1. No menu, sob **configurações**, selecione **pontos finais incorporados**e, em seguida, selecione o **eventos** ponto final.

   ![Na página pontos finais de compilação, selecione o botão de eventos][1]

1. Sob **grupos de consumidores**, introduza um nome exclusivo para o grupo de consumidores. Utilize este mesmo nome no seu ambiente do Time Series Insights quando cria uma nova origem de evento.

1. Selecione **Guardar**.

## <a name="add-a-new-event-source"></a>Adicionar uma nova origem de evento

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No menu do lado esquerdo, selecione **Todos os recursos**. Selecione o seu ambiente do Time Series Insights.

1. Sob **topologia do ambiente**, selecione **origens de eventos**e, em seguida, selecione **Add**.

   ![Selecione as origens de eventos e, em seguida, selecione o botão Adicionar][2]

1. Na **nova fonte de eventos** painel, para **nome da origem de evento**, introduza um nome exclusivo para este ambiente do Time Series Insights. Por exemplo, introduza **fluxo de eventos**.

1. Para **origem**, selecione **IoT Hub**.

1. Selecione um valor para **importar opção**:

   * Se já tiver um hub IoT das suas subscrições, selecione **utilize o IoT Hub a partir de subscrições disponíveis**. Esta opção é a abordagem mais fácil.
   * Se o hub IoT é externo para as suas subscrições ou se pretender escolher opções avançadas, selecione **definições de fornecer o IoT Hub manualmente**.

   ![Selecione as opções no novo painel de origem do evento][3]

1. A tabela seguinte descreve as propriedades que são necessárias para o **utilize o IoT Hub a partir de subscrições disponíveis** opção:

   ![Novo painel de origem de evento - propriedades a definir no IoT Hub do uso da opção subscrições disponíveis][4]

   | Propriedade | Descrição |
   | --- | --- |
   | ID da subscrição | Selecione a subscrição na qual o hub IoT foi criado.
   | Nome do hub IoT | Selecione o nome do IoT hub.
   | Nome de política do hub IoT | Selecione a política de acesso partilhado. Pode encontrar a política de acesso partilhado no separador de definições do hub IoT. Cada política de acesso partilhado tem um nome, as permissões que definir e chaves de acesso. A política de acesso partilhado para a origem do evento *tem* ter **serviço ligar** permissões.
   | Chave de política do hub IoT | A chave é pré-preenchida.
   | Grupo de consumidores do hub IoT | O grupo de consumidores lê eventos a partir do IoT hub. Recomendamos vivamente que utilize um grupo de consumidores dedicado para a origem do evento.
   | Formato de serialização de eventos | Atualmente, o JSON é o formato de serialização só estão disponíveis. As mensagens de eventos tem de estar no seguinte formato ou dados não podem ser lidos. |
   | Nome da propriedade Timestamp | Para determinar este valor, precisa entender o formato de mensagem dos dados da mensagem que são enviados para o hub IoT. Este valor é o **nome** da propriedade de evento específico dos dados de mensagem que pretende utilizar como o carimbo de hora do evento. O valor diferencia maiúsculas de minúsculas. Se deixado em branco, o **tempo de colocar em fila de eventos** de eventos de origem é utilizada como o carimbo de hora do evento. |

1. A tabela seguinte descreve as propriedades necessárias para o **definições de fornecer o IoT Hub manualmente**:

   | Propriedade | Descrição |
   | --- | --- |
   | ID da subscrição | A subscrição na qual o hub IoT foi criado.
   | Grupo de recursos | O nome do grupo de recursos no qual o hub IoT foi criado.
   | Nome do hub IoT | o nome do IoT hub. Quando criou o seu hub IoT, que introduziu um nome para o hub IoT.
   | Nome de política do hub IoT | A política de acesso partilhado. Pode criar a política de acesso partilhado no separador de definições do hub IoT. Cada política de acesso partilhado tem um nome, as permissões que definir e chaves de acesso. A política de acesso partilhado para a origem do evento *tem* ter **serviço ligar** permissões.
   | Chave de política do hub IoT | A chave de acesso partilhado que é utilizada para autenticar o acesso ao espaço de nomes do Service bus do Azure. Introduza a chave primária ou secundária aqui.
   | Grupo de consumidores do hub IoT | O grupo de consumidores lê eventos a partir do IoT hub. Recomendamos vivamente que utilize um grupo de consumidores dedicado para a origem do evento.
   | Formato de serialização de eventos | Atualmente, o JSON é o formato de serialização só estão disponíveis. As mensagens de eventos tem de estar no seguinte formato ou dados não podem ser lidos. |
   | Nome da propriedade Timestamp | Para determinar este valor, precisa entender o formato de mensagem dos dados da mensagem que são enviados para o hub IoT. Este valor é o **nome** da propriedade de evento específico dos dados de mensagem que pretende utilizar como o carimbo de hora do evento. O valor diferencia maiúsculas de minúsculas. Se deixado em branco, o **tempo de colocar em fila de eventos** de eventos de origem é utilizada como o carimbo de hora do evento. |

1. Adicione o nome de grupo consumidor dedicado Time Series Insights que adicionou ao seu hub IoT.

1. Selecione **Criar**.

   ![O botão Criar][5]

1. Depois de criar a origem do evento, o Time Series Insights inicia automaticamente a transmissão em fluxo de dados ao seu ambiente.

## <a name="next-steps"></a>Passos Seguintes

* [Definir políticas de acesso de dados](time-series-insights-data-access.md) para proteger os dados.
* [Enviar eventos](time-series-insights-send-events.md) para a origem do evento.
* Aceder ao seu ambiente no [Explorador do Time Series Insights](https://insights.timeseries.azure.com).

<!-- Images -->
[1]: media/time-series-insights-how-to-add-an-event-source-iothub/iothub_one.png
[2]: media/time-series-insights-how-to-add-an-event-source-iothub/iothub_two.png
[3]: media/time-series-insights-how-to-add-an-event-source-iothub/iothub_three.png
[4]: media/time-series-insights-how-to-add-an-event-source-iothub/iothub_four.png
[5]: media/time-series-insights-how-to-add-an-event-source-iothub/iothub_five.png