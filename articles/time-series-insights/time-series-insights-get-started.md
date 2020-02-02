---
title: Criar um ambiente - Azure Time Series Insights  Microsoft Docs
description: Aprenda a usar o portal Azure para criar um novo ambiente Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/31/2020
ms.custom: seodec18
ms.openlocfilehash: 2c946c49884ef0de6843028976d4ec00ccfbcdfe
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934852"
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Criar um novo ambiente do Time Series Insights no portal do Azure

Este artigo descreve como criar um novo ambiente Time Series Insights usando o portal Azure.

Time Series Insights permite-lhe começar a visualizar e consultar dados que fluem para Hubs E Hubs de Eventos Azure IoT em minutos, permitindo-lhe consultar grandes volumes de dados da série de tempo em segundos.  Foi projetado para a escala de internet-of-things (IoT) e pode lidar com terabytes de dados.

## <a name="steps-to-create-the-environment"></a>Passos para criar o ambiente

Siga estes passos para criar um ambiente:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

1. Selecione o + Criar um botão **de recurso.**

1. Selecione a categoria **Internet das Coisas** e selecione Time Series **Insights**.

   [![Criar o ambiente de Insights da Série do Tempo](media/time-series-insights-get-started/tsi-create-new-environment.png)](media/time-series-insights-get-started/tsi-create-new-environment.png#lightbox)

1. Na página **Time Series Insights,** selecione **Criar**.

1. Preencha os parâmetros necessários. A tabela a seguir explica cada parâmetro:
   
   [![Criar o grupo de recursos Time Series Insights](media/time-series-insights-get-started/tsi-configure-and-create.png)](media/time-series-insights-get-started/tsi-configure-and-create.png#lightbox)
   
   Definição|Valor sugerido|Descrição
   ---|---|---
   Nome do ambiente | Um nome exclusivo | Este nome representa o ambiente no explorador da [série temporal](https://insights.timeseries.azure.com)
   Subscrição | A sua subscrição | Se tiver várias subscrições, escolha a subscrição que contém a fonte do seu evento de preferência. As Time Series Insights podem detetar automaticamente os recursos do Hub Azure IoT e do Event Hub existentes na mesma subscrição.
   Grupo de recursos | Criar um novo ou usar existente | Um grupo de recursos é uma coleção de recursos do Azure utilizados em conjunto. Pode escolher um grupo de recursos existente, por exemplo aquele que contém o seu Hub de Eventos ou Hub IoT. Ou pode fazer um novo se este recurso não estiver relacionado com os outros recursos.
   Localização | Mais próximo da sua fonte de evento | De preferência, escolha a mesma localização do centro de dados que contém os dados de origem do evento, no esforço para evitar custos adicionais de largura de banda transversal e transversal e latência adicionada ao retirar dados da região.
   Escalão de preço | S1 | Escolha o débito necessário. Para os custos mais baixos e capacidade de arranque, selecione S1.
   Capacidade | 1 | A capacidade é que o multiplicador se aplica à taxa de entrada, capacidade de armazenamento e custo associado ao SKU selecionado.  Pode alterar a capacidade de um ambiente após a criação. Para os custos mais baixos, selecione uma capacidade de 1. 
  
1. Selecione **Criar** para iniciar o processo de provisionamento. Pode levar alguns minutos.

1. Para monitorizar o processo de implantação, selecione o símbolo **notificações** (ícone do sino).

   [![Ver as notificações](media/time-series-insights-get-started/tsi-deploy-notifications.png)](media/time-series-insights-get-started/tsi-deploy-notifications.png#lightbox)

1. Na **visão geral**do recurso, verifique as definições de configuração de implementação.

   [![Criar o pino de Insights da Série de Tempo para o painel de instrumentos](media/time-series-insights-get-started/tsi-verify-deployment.png)](media/time-series-insights-get-started/tsi-verify-deployment.png#lightbox)

1. **(Opcional)** Selecione o ícone do **pino** no canto superior direito para aceder facilmente ao ambiente time series Insights no futuro.

## <a name="next-steps"></a>Passos seguintes

* [Defina políticas de acesso](time-series-insights-data-access.md) a dados para proteger o seu ambiente.

* [Adicione uma fonte](time-series-insights-how-to-add-an-event-source-eventhub.md) de evento do Event Hub ao seu ambiente Azure Time Series Insights.

* [Enviar eventos](time-series-insights-send-events.md) para a origem do evento.

* Veja o seu ambiente no explorador time [series Insights](https://insights.timeseries.azure.com).
