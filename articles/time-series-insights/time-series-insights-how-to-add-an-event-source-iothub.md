---
title: Como adicionar um evento de Hub IoT fonte-Azure Time Series Insights | Microsoft Docs
description: Saiba como adicionar uma origem de evento do Hub IoT ao seu ambiente de Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/02/2019
ms.custom: seodec18
ms.openlocfilehash: fa3bcb0ba16c976706c70bbc76daeb8b418a74ea
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74764067"
---
# <a name="add-an-iot-hub-event-source-to-your-time-series-insights-environment"></a>Adicionar uma origem de evento do Hub IoT ao seu ambiente de Time Series Insights

Este artigo descreve como usar o portal do Azure para adicionar uma origem de evento que lê dados do Hub IoT do Azure para seu ambiente de Azure Time Series Insights.

> [!NOTE]
> As instruções neste artigo se aplicam a Azure Time Series Insights GA e Time Series Insights ambientes de visualização.

## <a name="prerequisites"></a>Pré-requisitos

* Crie um [ambiente de Azure Time Series insights](time-series-insights-update-create-environment.md).
* Crie um [Hub IOT usando o portal do Azure](../iot-hub/iot-hub-create-through-portal.md).
* O Hub IoT deve ter eventos de mensagem ativos sendo enviados no.
* Crie um grupo de consumidores dedicado no Hub IoT no qual o ambiente de Time Series Insights será consumido. Cada fonte de evento de Time Series Insights deve ter seu próprio grupo de consumidores dedicado que não é compartilhado com nenhum outro consumidor. Se vários leitores consomem eventos do mesmo grupo de consumidores, é provável que todos os leitores vejam falhas. Para obter detalhes, consulte o [Guia do desenvolvedor do Hub IOT do Azure](../iot-hub/iot-hub-devguide.md).

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Adicionar um grupo de consumidores ao Hub IoT

Os aplicativos usam grupos de consumidores para efetuar pull de dados do Hub IoT do Azure. Para ler de forma confiável dados de seu hub IoT, forneça um grupo de consumidores dedicado que é usado somente por esse ambiente de Time Series Insights.

Para adicionar um novo grupo de consumidores ao seu hub IoT:

1. Na [portal do Azure](https://portal.azure.com), localize e abra o Hub IOT.

1. Em **configurações**, selecione **pontos de extremidade internos**e, em seguida, selecione o ponto final de **eventos** .

   [![na página pontos de extremidade de compilação, selecione o botão eventos](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-connect-iot-hub.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-connect-iot-hub.png#lightbox)

1. Em **grupos de consumidores**, insira um nome exclusivo para o grupo de consumidores. Use esse mesmo nome em seu ambiente de Time Series Insights ao criar uma nova origem de evento.

1. Selecione **Guardar**.

## <a name="add-a-new-event-source"></a>Adicionar uma nova origem do evento

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No menu do lado esquerdo, selecione **Todos os recursos**. Selecione o seu ambiente do Time Series Insights.

1. Em **topologia do ambiente**, selecione **origens do evento**e, em seguida, selecione **Adicionar**.

   [![selecionar origens do evento e, em seguida, selecione o botão Adicionar](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-add-event-source.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-add-event-source.png#lightbox)

1. No **novo painel origem do evento** , para **nome da origem do evento**, insira um nome que seja exclusivo para esse time Series insights ambiente. Por exemplo, insira **Event-Stream**.

1. Para **origem**, selecione **Hub IOT**.

1. Selecione um valor para a **opção de importação**:

   * Se você já tiver um hub IoT em uma de suas assinaturas, selecione usar o **Hub IOT de assinaturas disponíveis**. Essa opção é a abordagem mais fácil.
   
     [![selecionar opções no novo painel de origem do evento](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-select-an-import-option.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-select-an-import-option.png#lightbox)

    * A tabela a seguir descreve as propriedades que são necessárias para a opção **usar o Hub IOT de assinaturas disponíveis** :

       [![novo painel de origem do evento-Propriedades a serem definidas na opção usar o Hub IoT de assinaturas disponíveis](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-create-configure-confirm.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-create-configure-confirm.png#lightbox)

       | Propriedade | Descrição |
       | --- | --- |
       | Subscrição | A assinatura à qual o Hub IOT desejado pertence. |
       | Nome do Hub IoT | O nome do Hub IOT selecionado. |
       | Nome da política do Hub IoT | Selecione a política de acesso compartilhado. Você pode encontrar a política de acesso compartilhado na guia Configurações do Hub IoT. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. A política de acesso compartilhado para a origem do evento *deve* ter permissões de **conexão de serviço** . |
       | Chave de política do Hub IoT | A chave está preenchida previamente. |

    * Se o Hub IoT for externo às suas assinaturas ou se você quiser escolher opções avançadas, selecione **fornecer configurações do Hub IOT manualmente**.

      A tabela a seguir descreve as propriedades necessárias para **fornecer as configurações do Hub IOT manualmente**:

       | Propriedade | Descrição |
       | --- | --- |
       | ID de Subscrição | A assinatura à qual o Hub IOT desejado pertence. |
       | Grupo de recursos | O nome do grupo de recursos no qual o Hub IoT foi criado. |
       | Nome do Hub IoT | O nome do Hub IoT. Quando você criou o Hub IoT, você inseriu um nome para o Hub IoT. |
       | Nome da política do Hub IoT | A política de acesso compartilhado. Você pode criar a política de acesso compartilhado na guia Configurações do Hub IoT. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. A política de acesso compartilhado para a origem do evento *deve* ter permissões de **conexão de serviço** . |
       | Chave de política do Hub IoT | A chave de acesso compartilhado que é usada para autenticar o acesso ao namespace do barramento de serviço do Azure. Insira a chave primária ou secundária aqui. |

    * Ambas as opções compartilham as seguintes opções de configuração:

       | Propriedade | Descrição |
       | --- | --- |
       | Grupo de consumidores do Hub IoT | O grupo de consumidores que lê eventos do Hub IoT. É altamente recomendável que você use um grupo de consumidores dedicado para a origem do evento. |
       | Formato de serialização de evento | Atualmente, o JSON é o único formato de serialização disponível. As mensagens de evento devem estar nesse formato ou nenhum dado pode ser lido. |
       | Nome da propriedade Timestamp | Para determinar esse valor, você precisa entender o formato da mensagem dos dados da mensagem que são enviados para o Hub IoT. Esse valor é o **nome** da propriedade de evento específica nos dados da mensagem que você deseja usar como o carimbo de data/hora do evento. O valor diferencia maiúsculas de minúsculas. Se deixado em branco, o **tempo de enfileiramento de eventos** na origem do evento será usado como o carimbo de data/hora do evento. |


1. Adicione o nome do grupo de consumidores de Time Series Insights dedicado que você adicionou ao seu hub IoT.

1. Selecione **Criar**.

1. Depois de criar a origem do evento, Time Series Insights iniciar automaticamente os dados de streaming para o seu ambiente.

## <a name="next-steps"></a>Passos seguintes

* [Defina políticas de acesso de dados](time-series-insights-data-access.md) para proteger os dados.

* [Enviar eventos](time-series-insights-send-events.md) para a origem do evento.

* Acesse seu ambiente no [Time Series insights Explorer](https://insights.timeseries.azure.com).
