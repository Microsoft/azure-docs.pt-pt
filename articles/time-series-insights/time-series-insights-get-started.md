---
title: Criar um ambiente - Azure Time Series Insights / Microsoft Docs
description: Aprenda a usar o portal Azure para criar um novo ambiente Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 61af7922318514a7b86a349d1970c59d4d168d85
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95023331"
---
# <a name="create-a-new-azure-time-series-insights-gen1-environment-in-the-azure-portal"></a>Criar um novo ambiente Azure Time Series Insights Gen1 no portal Azure

> [!CAUTION]
> Este é um artigo da Gen1.

Este artigo descreve como criar um novo ambiente Azure Time Series Insights utilizando o portal Azure.

O Azure Time Series Insights permite-lhe começar a visualizar e consultar dados que fluem para Azure IoT Hubs e Event Hubs em minutos, permitindo-lhe consultar grandes volumes de dados de séries de tempo em segundos.  Foi projetado para a escala internet-of-things (IoT) e pode lidar com terabytes de dados.

## <a name="steps-to-create-the-environment"></a>Passos para criar o ambiente

Siga estes passos para criar um ambiente:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione o + Criar um botão **de recurso.**

1. Selecione a categoria **Internet of Things** e selecione Time Series **Insights**.

   [![Criar o ambiente Azure Time Series Insights](media/time-series-insights-get-started/tsi-create-new-environment.png)](media/time-series-insights-get-started/tsi-create-new-environment.png#lightbox)

1. Na página **Time Series Insights,** selecione **Criar**.

1. Preencha os parâmetros necessários. A tabela a seguir explica cada parâmetro:

   [![Criar o grupo de recursos Azure Time Series Insights](media/time-series-insights-get-started/tsi-configure-and-create.png)](media/time-series-insights-get-started/tsi-configure-and-create.png#lightbox)

   Definição|Valor sugerido|Descrição
   ---|---|---
   Nome do ambiente | Um nome exclusivo | Este nome representa o ambiente na [série de tempo Explorer](https://insights.timeseries.azure.com)
   Subscrição | A sua subscrição | Se tiver várias subscrições, escolha a subscrição que contém a fonte do seu evento de preferência. Azure Time Series Insights pode detetar automaticamente os recursos do Azure IoT Hub e do Event Hub existentes na mesma subscrição.
   Grupo de recursos | Criar um novo ou usar o existente | Um grupo de recursos é uma coleção de recursos do Azure utilizados em conjunto. Pode escolher um grupo de recursos existente, por exemplo, aquele que contém o seu Centro de Eventos ou IoT Hub. Ou pode fazer um novo se este recurso não estiver relacionado com os outros recursos.
   Localização | A fonte mais próxima do seu evento | De preferência, escolha a mesma localização do centro de dados que contém os dados de origem do seu evento, em esforço para evitar custos adicionais de largura de banda entre regiões e zonas cruzadas e acrescentou latência ao retirar dados da região.
   Escalão de preço | S1 | Escolha o débito necessário. Para obter os custos mais baixos e a capacidade de arranque, selecione S1.
   Capacidade | 1 | A capacidade é que o multiplicador se aplica à taxa de entrada, capacidade de armazenamento e custo associado ao SKU selecionado.  Pode alterar a capacidade de um ambiente após a criação. Para os custos mais baixos, selecione uma capacidade de 1.
  
1. Selecione **Criar** para iniciar o processo de provisionamento. Poderá demorar alguns minutos.

1. Para monitorizar o processo de implementação, selecione o símbolo **'Notificações'** (ícone da campainha).

   [![Assista às notificações](media/time-series-insights-get-started/tsi-deploy-notifications.png)](media/time-series-insights-get-started/tsi-deploy-notifications.png#lightbox)

1. No **resumo** do recurso, verifique as definições de configuração da sua implementação.

   [![Crie o pin da Série de Tempo Azure para painel de instrumentos](media/time-series-insights-get-started/tsi-verify-deployment.png)](media/time-series-insights-get-started/tsi-verify-deployment.png#lightbox)

1. **(Opcional)** Selecione o **ícone pino** no canto superior direito para aceder facilmente ao ambiente Azure Time Series Insights no futuro.

## <a name="next-steps"></a>Próximos passos

* [Defina políticas de acesso](./concepts-access-policies.md) a dados para proteger o seu ambiente.

* [Adicione uma fonte de eventos do Event Hub](./how-to-ingest-data-event-hub.md) ao ambiente Azure Time Series Insights.

* [Envie eventos](time-series-insights-send-events.md) para a fonte do evento.

* Veja o seu ambiente no [Azure Time Series Insights Explorer](https://insights.timeseries.azure.com).