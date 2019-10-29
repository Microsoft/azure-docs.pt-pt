---
title: Criar um ambiente do Azure Time Series Insights | Microsoft Docs
description: Este artigo descreve como usar o portal do Azure para criar um novo ambiente de Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 1b51931385eb7e2e0bf2a1baf5cc4c3379eab2df
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991236"
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Criar um novo ambiente do Time Series Insights no portal do Azure

Este artigo descreve como criar um novo ambiente de Time Series Insights usando o portal do Azure.

Time Series Insights permite que você comece a visualizar e consultar dados que fluem para hubs IoT do Azure e hubs de eventos em minutos, permitindo que você consulte grandes volumes de dados de série temporal em segundos.  Ele foi projetado para a escala de IoT (Internet das coisas) e pode lidar com terabytes de dados.

## <a name="steps-to-create-the-environment"></a>Passos para criar o ambiente

Siga estas etapas para criar um ambiente:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione o botão **+ criar um recurso** .

1. Selecione a categoria **Internet das coisas** e selecione **Time Series insights**.

   [![criar o ambiente de time Series insights](media/time-series-insights-get-started/1-new-tsi.png)](media/time-series-insights-get-started/1-new-tsi.png#lightbox))

1. Na página **Time Series insights** , selecione **criar**.

1. Preencha os parâmetros necessários. A tabela a seguir explica cada parâmetro:
   
   [![criar o grupo de recursos Time Series Insights](media/time-series-insights-get-started/2-create-tsi.png)](media/time-series-insights-get-started/2-create-tsi.png#lightbox)
   
   Definição|Valor sugerido|Descrição
   ---|---|---
   Nome do ambiente | Um nome exclusivo | Esse nome representa o ambiente no [Explorador do time Series](https://insights.timeseries.azure.com)
   Subscrição | A sua subscrição | Se você tiver várias assinaturas, escolha a assinatura que contém a origem do evento de preferência. O Time Series Insights pode detetar automaticamente recursos do Hub IoT do Azure e do Hub de Eventos existentes na mesma subscrição.
   Grupo de recursos | Criar um novo ou usar um existente | Um grupo de recursos é uma coleção de recursos do Azure utilizados em conjunto. Você pode escolher um grupo de recursos existente, por exemplo, aquele que contém seu hub de eventos ou Hub IoT. Ou você pode criar um novo se esse recurso não estiver relacionado aos outros recursos.
   Localização | Mais próximo da origem do evento | Preferencialmente, escolha o mesmo local de data center que contém os dados de origem do evento, em esforço para evitar o acréscimo de custos de largura de banda entre regiões e de zona cruzada e adição de latência ao mover dados para fora da região.
   Escalão de preço | S1 | Escolha o débito necessário. Para obter os custos mais baixos e a capacidade inicial, selecione S1.
   Capacidade | 1 | A capacidade é o multiplicador se aplica à taxa de entrada, à capacidade de armazenamento e ao custo associado à SKU selecionada.  Pode alterar a capacidade de um ambiente após a criação. Para custos mais baixos, selecione uma capacidade de 1. 
  
1. Selecione **criar** para iniciar o processo de provisionamento. Pode levar alguns minutos.

1. Para monitorar o processo de implantação, selecione o símbolo de **notificações** (ícone de sino).

   [![observar as notificações](media/time-series-insights-get-started/3-notifications.png)](media/time-series-insights-get-started/3-notifications.png#lightbox)

    Quando a implantação for realizada com sucesso, você poderá selecionar **ir para o recurso** para configurar outras propriedades, definir a segurança com políticas de acesso a dados, adicionar fontes de evento e outras ações.

1. Na **visão geral**do recurso, selecione o **ícone de pino** no canto superior direito para acessar facilmente seu ambiente de time Series insights no futuro.

   [![criar o PIN de Time Series Insights ao painel](media/time-series-insights-get-started/4-pin-create.png)](media/time-series-insights-get-started/4-pin-create.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

* [Defina políticas de acesso a dados](time-series-insights-data-access.md) para proteger seu ambiente.

* [Adicione uma origem de evento do hub de eventos](time-series-insights-how-to-add-an-event-source-eventhub.md) ao seu ambiente de Azure Time Series insights.

* [Enviar eventos](time-series-insights-send-events.md) para a origem do evento.

* Exiba seu ambiente no [Time Series insights Explorer](https://insights.timeseries.azure.com).
