---
title: Crie um ambiente Gen2 utilizando o portal Azure - Azure Time Series Insights Gen2 | Microsoft Docs
description: Aprenda a configurar um ambiente em Azure Time Series Insights Gen2 usando o portal Azure.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18
ms.openlocfilehash: 56f7ac6f4403867e5e4e53b55b241912cdc57454
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103464453"
---
# <a name="create-an-azure-time-series-insights-gen2-environment-using-the-azure-portal"></a>Criar um ambiente Azure Time Series Insights Gen2 utilizando o portal Azure

Este artigo descreve como criar um ambiente Azure Time Series Insights Gen2 utilizando o [portal Azure](https://portal.azure.com/).

## <a name="overview"></a>Descrição geral

Ao fornecer um ambiente Azure Time Series Insights Gen2, cria estes recursos Azure:

* Um ambiente Azure Time Series Insights Gen2 que segue o modelo de preços pay-as-you-go
* Uma conta de armazenamento Azure
* Uma loja de aquecimento opcional para consultas mais rápidas e ilimitadas

> [!TIP]
>
> * Saiba [como planear o seu ambiente.](./how-to-plan-your-environment.md)
> * Leia sobre como [adicionar uma fonte de hub](./how-to-ingest-data-event-hub.md) de evento ou como adicionar uma fonte de hub [IoT](./how-to-ingest-data-iot-hub.md).

Vai aprender a:

1. Associar cada ambiente Azure Time Series Insights Gen 2 com uma fonte de evento. Você também fornecerá uma propriedade de ID Timestamp e um grupo de consumidores único para garantir que o ambiente tem acesso aos eventos apropriados.

1. Após o fornecimento estar completo, pode modificar as suas políticas de acesso e outros atributos ambientais de acordo com as necessidades do seu negócio.

   > [!NOTE]
   > O primeiro passo é facultativo quando se disponibiliza um ambiente. Se saltar este passo, deve anexar uma fonte de evento ao ambiente mais tarde para que os dados possam começar a fluir para o seu ambiente e possam ser acedidos através de consultas.

## <a name="create-the-environment"></a>Criar o ambiente

Para criar um ambiente Azure Time Series Insights Gen 2:

1. Crie um recurso Azure Time Series Insights no *internet of things* on [Azure portal](https://portal.azure.com/).

1. Selecione **Gen2(L1)** como **o Tier**. Forneça um nome ambiental e escolha o grupo de subscrição e o grupo de recursos para usar. Em seguida, selecione um local suportado para hospedar o ambiente.

   :::image type="content" source="media/how-to-create-environment-using-portal/environment-configuration.png" alt-text="Crie uma instância de Insights da Série de Tempo Azure." lightbox="media/how-to-create-environment-using-portal/environment-configuration.png":::

1. Introduza um ID da Série Temporal.

   :::image type="content" source="media/how-to-create-environment-using-portal/environment-configuration-2.png" alt-text="Crie uma configuração ambiental Azure Time Series Insights, continuada." lightbox="media/how-to-create-environment-using-portal/environment-configuration-2.png":::

   > [!NOTE]
   >
   > * O ID da Série De Tempo é *sensível a casos* e *imutável*. (Não pode ser mudado depois de definido.)
   > * Os IDs da Série De Tempo podem chegar a *três* teclas. Pense nisso como uma chave primária numa base de dados, que representa exclusivamente cada sensor de dispositivo que enviaria dados para o seu ambiente. Pode ser uma propriedade ou uma combinação de até três propriedades.
   > * Leia mais sobre [Como escolher um ID da Série De Tempo](./how-to-select-tsid.md)

1. Crie uma conta de Armazenamento Azure selecionando um nome de conta de armazenamento, tipo de conta e designando uma escolha de [replicação.](../storage/common/redundancy-migration.md?tabs=portal) Ao fazê-lo, cria-se automaticamente uma conta de Armazenamento Azure. Por predefinição, será criada a conta [V2 para fins gerais.](../storage/common/storage-account-overview.md) A conta é criada na mesma região que o ambiente Azure Time Series Insights Gen2 que selecionou anteriormente.
Em alternativa, também pode trazer o seu próprio armazenamento (BYOS) através de um [modelo de Gestor de Recursos Azure](./time-series-insights-manage-resources-using-azure-resource-manager-template.md) quando criar um novo ambiente Azure Time Series Gen2.

1. **(Opcional)** Ative a loja quente para o seu ambiente se quiser consultas mais rápidas e ilimitadas sobre os dados mais recentes no seu ambiente. Também pode criar ou eliminar uma loja quente através da opção **de configuração de armazenamento** no painel de navegação esquerdo, depois de criar um ambiente Azure Time Series Insights Gen2.

1. **(Opcional)** Pode adicionar uma fonte de evento agora. Também pode esperar até que o caso tenha sido a provisionado.

   * Azure Time Series Insights suporta [Azure IoT Hub](./how-to-ingest-data-iot-hub.md) e [Azure Event Hubs](./how-to-ingest-data-event-hub.md) como opções de origem de eventos. Embora possa adicionar apenas uma única fonte de eventos quando criar o ambiente, pode adicionar outra fonte de evento mais tarde.

     Pode selecionar um grupo de consumidores existente ou criar um novo grupo de consumidores quando adicionar a fonte do evento. Certifique-se de que a fonte do evento utiliza um grupo de consumidores único para o seu ambiente ler dados nele.

   * Escolha a propriedade timetamp apropriada. Por predefinição, o Azure Time Series Insights utiliza o tempo de mensagem para cada fonte de evento.

     > [!TIP]
     > O tempo de mensagem pode não ser a melhor configuração configurada para ser usado em cenários de eventos de lote ou cenários históricos de upload de dados. Nesses casos, certifique-se de verificar a sua decisão de utilizar ou não utilizar uma propriedade Timestamp.

   :::image type="content" source="media/how-to-create-environment-using-portal/configure-event-source.png" alt-text="Separador de configuração de Fonte de Evento" lightbox="media/how-to-create-environment-using-portal/configure-event-source.png":::

1. Selecione **Review + Crie** para confirmar que o seu ambiente foi provisionado e configurado da forma que deseja.

    :::image type="content" source="media/how-to-create-environment-using-portal/environment-confirmation.png" alt-text="Comentário + Criar separador" lightbox="media/how-to-create-environment-using-portal/environment-confirmation.png":::

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre a Azure Time Series Insights geralmente disponíveis ambientes e ambientes Gen2 lendo [Planeie o seu ambiente](./how-to-plan-your-environment.md).
* Saiba mais sobre [fontes de eventos de streaming](./concepts-streaming-ingestion-event-sources.md) para o seu ambiente Azure Time Series Insights Gen2.
* Saiba mais sobre [como gerir o seu ambiente.](./how-to-provision-manage.md)
