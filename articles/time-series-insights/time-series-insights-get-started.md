---
title: Criar um ambiente do Azure Time Series Insights | Microsoft Docs
description: Este artigo descreve como utilizar o portal do Azure para criar um novo ambiente do Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.custom: seodec18
ms.openlocfilehash: b18ae44a140d77c24d8b5741bc09740767d1344f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58075957"
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Criar um novo ambiente do Time Series Insights no portal do Azure
Este artigo descreve como criar um novo ambiente do Time Series Insights com o portal do Azure.

O Time Series Insights permite-lhe começar a visualizar e consultar dados a ser encaminhados para os Hubs IoT do Azure e Hubs de eventos em minutos, permitindo que para consultar grandes volumes de dados de séries de tempo em segundos.  Ele foi projetado para a escala do internet das coisas (IoT) e pode processar terabytes de dados.

## <a name="steps-to-create-the-environment"></a>Passos para criar o ambiente
Siga estes passos para criar um ambiente:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Selecione o **+ novo** botão.

3. Selecione o **Internet das coisas** categoria e selecione **Time Series Insights**.

   ![Crie o ambiente do Time Series Insights](media/time-series-insights-get-started/1-new-tsi.png)

4. Sobre o **Time Series Insights** página, selecione **criar**.

5. Preencha os parâmetros necessários. A tabela seguinte explica cada parâmetro:
   
   ![Crie o grupo de recursos do Time Series Insights](media/time-series-insights-get-started/2-create-tsi.png)
   
   Definição|Valor sugerido|Descrição
   ---|---|---
   Nome do ambiente | Um nome exclusivo | Este nome representa o ambiente no [Explorador de séries de tempo](https://insights.timeseries.azure.com)
   Subscrição | A sua subscrição | Se tiver várias subscrições, escolha a subscrição que contém a origem do evento, de preferência. O Time Series Insights pode detetar automaticamente recursos do Hub IoT do Azure e do Hub de Eventos existentes na mesma subscrição.
   Grupo de recursos | Criar um novo ou utilizar existente | Um grupo de recursos é uma coleção de recursos do Azure utilizados em conjunto. Pode escolher um grupo de recursos existente, por exemplo de uma que contenha o seu Hub de eventos ou o IoT Hub. Ou pode fazer um novo, se este recurso não está relacionada com os outros recursos.
   Localização | Mais próxima da origem de evento | De preferência, escolha a mesma localização do Datacenter que contém os dados de origem de evento, em esforço para evitar adicionado os custos de largura de banda entre regiões e entre zonas e adicionados a latência ao mover dados fora da região.
   Escalão de preço | S1 | Escolha o débito necessário. Custos mais baixos e capacidade de arranque, selecione S1.
   Capacidade | 1 | A capacidade é que o multiplicador aplica-se para a taxa de entrada, a capacidade de armazenamento e o custo associado o SKU selecionado.  Pode alterar a capacidade de um ambiente após a criação. Custos mais baixos, selecione uma capacidade de 1. 
  
6. Verifique **afixar ao dashboard** para melhor aceder facilmente ao seu ambiente de Time Series no futuro.

   ![Crie o marcador do Time Series Insights para o dashboard](media/time-series-insights-get-started/3-pin-create.png)

7. Selecione **criar** para iniciar o processo de aprovisionamento. Pode demorar alguns minutos.

8. Para monitorizar o processo de implementação, selecione o **notificações** símbolo (ícone de sino).

   ![Ver as notificações](media/time-series-insights-get-started/4-notifications.png)

Quando a implementação com êxito, pode selecionar **Ir para recurso** para configurar outras propriedades, definir a segurança com políticas de acesso de dados, adicionar origens de eventos e outras ações.

## <a name="next-steps"></a>Passos Seguintes
* [Definir políticas de acesso de dados](time-series-insights-data-access.md) para proteger o seu ambiente.
* [Adicionar uma origem de evento do Hub de eventos](time-series-insights-how-to-add-an-event-source-eventhub.md) ao seu ambiente do Azure Time Series Insights. 
* [Enviar eventos](time-series-insights-send-events.md) para a origem do evento.
* Ver o seu ambiente no [Explorador do Time Series Insights](https://insights.timeseries.azure.com).
