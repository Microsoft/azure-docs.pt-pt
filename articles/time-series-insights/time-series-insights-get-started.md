---
title: Criar um ambiente - Azure Time Series Insights / Microsoft Docs
description: Aprenda a usar o portal Azure para criar um novo ambiente de Insights de Séries Tempo.
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
ms.openlocfilehash: 7459c6afc775aa0df43d6f9285191c4c7e1b8cb8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81602371"
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Criar um novo ambiente do Time Series Insights no portal do Azure

Este artigo descreve como criar um novo ambiente de Insights de Séries Temporizadas utilizando o portal Azure.

O Time Series Insights permite-lhe começar a visualizar e consultar dados que fluem para Azure IoT Hubs e Event Hubs em minutos, permitindo-lhe consultar grandes volumes de dados de séries de tempo em segundos.  Foi projetado para a escala internet-of-things (IoT) e pode lidar com terabytes de dados.

## <a name="steps-to-create-the-environment"></a>Passos para criar o ambiente

Siga estes passos para criar um ambiente:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione o + Criar um botão **de recurso.**

1. Selecione a categoria **Internet of Things** e selecione Time Series **Insights**.

   [![Crie o ambiente do Time Series Insights](media/time-series-insights-get-started/tsi-create-new-environment.png)](media/time-series-insights-get-started/tsi-create-new-environment.png#lightbox)

1. Na página **Time Series Insights,** selecione **Criar**.

1. Preencha os parâmetros necessários. A tabela a seguir explica cada parâmetro:

   [![Crie o grupo de recursos do Time Series Insights](media/time-series-insights-get-started/tsi-configure-and-create.png)](media/time-series-insights-get-started/tsi-configure-and-create.png#lightbox)

   Definição|Valor sugerido|Descrição
   ---|---|---
   Nome do ambiente | Um nome exclusivo | Este nome representa o ambiente no [explorador de séries temporôns](https://insights.timeseries.azure.com)
   Subscrição | A sua subscrição | Se tiver várias subscrições, escolha a subscrição que contém a fonte do seu evento de preferência. O Time Series Insights pode detetar automaticamente os recursos do Azure IoT Hub e do Event Hub existentes na mesma subscrição.
   Grupo de recursos | Criar um novo ou usar o existente | Um grupo de recursos é uma coleção de recursos do Azure utilizados em conjunto. Pode escolher um grupo de recursos existente, por exemplo, aquele que contém o seu Centro de Eventos ou IoT Hub. Ou pode fazer um novo se este recurso não estiver relacionado com os outros recursos.
   Localização | A fonte mais próxima do seu evento | De preferência, escolha a mesma localização do centro de dados que contém os dados de origem do seu evento, em esforço para evitar custos adicionais de largura de banda entre regiões e zonas cruzadas e acrescentou latência ao retirar dados da região.
   Escalão de preço | S1 | Escolha o débito necessário. Para obter os custos mais baixos e a capacidade de arranque, selecione S1.
   Capacidade | 1 | A capacidade é que o multiplicador se aplica à taxa de entrada, capacidade de armazenamento e custo associado ao SKU selecionado.  Pode alterar a capacidade de um ambiente após a criação. Para os custos mais baixos, selecione uma capacidade de 1.
  
1. Selecione **Criar** para iniciar o processo de provisionamento. Poderá demorar alguns minutos.

1. Para monitorizar o processo de implementação, selecione o símbolo **'Notificações'** (ícone da campainha).

   [![Assista às notificações](media/time-series-insights-get-started/tsi-deploy-notifications.png)](media/time-series-insights-get-started/tsi-deploy-notifications.png#lightbox)

1. No **resumo**do recurso, verifique as definições de configuração da sua implementação.

   [![Crie o marcador do Time Series Insights para o dashboard](media/time-series-insights-get-started/tsi-verify-deployment.png)](media/time-series-insights-get-started/tsi-verify-deployment.png#lightbox)

1. **(Opcional)** Selecione o **ícone pino** no canto superior direito para aceder facilmente ao ambiente de Insights de Séries temporais no futuro.

## <a name="next-steps"></a>Próximos passos

* [Defina políticas de acesso](time-series-insights-data-access.md) a dados para proteger o seu ambiente.

* [Adicione uma fonte de eventos do Event Hub](time-series-insights-how-to-add-an-event-source-eventhub.md) ao ambiente Azure Time Series Insights.

* [Envie eventos](time-series-insights-send-events.md) para a fonte do evento.

* Veja o seu ambiente no [explorador de Insights de Séries tempor usual .](https://insights.timeseries.azure.com)
