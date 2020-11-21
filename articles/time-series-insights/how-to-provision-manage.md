---
title: Provisão e gestão de um ambiente Gen 2 - Azure Time Series Microsoft Docs
description: Aprenda a fornecer e gerir um ambiente Azure Time Series Insights Gen 2.
author: shipra1mishra
ms.author: shmishr
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: 7c38c57a8480ef2addde494b94d70bd2eb679373
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95016773"
---
# <a name="provision-and-manage-azure-time-series-insights-gen2"></a>Provisão e gestão da Azure Time Series Insights Gen2

Este artigo descreve como criar e gerir um ambiente Azure Time Series Insights Gen2 utilizando o [portal Azure](https://portal.azure.com/).

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

   [![Crie uma instância de Insights da Série de Tempo Azure.](media/v2-update-manage/create-and-manage-configuration.png)](media/v2-update-manage/create-and-manage-configuration.png#lightbox)

1. Introduza um ID da Série Temporal.

    > [!NOTE]
    >
    > * O ID da Série De Tempo é *sensível a casos* e *imutável*. (Não pode ser mudado depois de definido.)
    > * Os IDs da Série De Tempo podem chegar a *três* teclas. Pense nisso como uma chave primária numa base de dados, que representa exclusivamente cada sensor de dispositivo que enviaria dados para o seu ambiente. Pode ser uma propriedade ou uma combinação de até três propriedades.
    > * Leia mais sobre [Como escolher um ID da Série De Tempo](./how-to-select-tsid.md)

1. Crie uma conta de Armazenamento Azure selecionando um nome de conta de armazenamento, tipo de conta e designando uma escolha de [replicação.](../storage/common/redundancy-migration.md?tabs=portal) Ao fazê-lo, cria-se automaticamente uma conta de Armazenamento Azure. Por predefinição, será criada a conta [V2 para fins gerais.](../storage/common/storage-account-overview.md) A conta é criada na mesma região que o ambiente Azure Time Series Insights Gen2 que selecionou anteriormente.
Em alternativa, também pode trazer o seu próprio armazenamento (BYOS) através [do modelo ARM](./time-series-insights-manage-resources-using-azure-resource-manager-template.md) quando criar um novo ambiente Azure Time Series Gen2.

1. **(Opcional)** Ative a loja quente para o seu ambiente se quiser consultas mais rápidas e ilimitadas sobre os dados mais recentes no seu ambiente. Também pode criar ou eliminar uma loja quente através da opção **de configuração de armazenamento** no painel de navegação esquerdo, depois de criar um ambiente Azure Time Series Insights Gen2.

1. **(Opcional)** Pode adicionar uma fonte de evento agora. Também pode esperar até que o caso tenha sido a provisionado.

   * Azure Time Series Insights suporta [Azure IoT Hub](./how-to-ingest-data-iot-hub.md) e [Azure Event Hubs](./how-to-ingest-data-event-hub.md) como opções de origem de eventos. Embora possa adicionar apenas uma única fonte de eventos quando criar o ambiente, pode adicionar outra fonte de evento mais tarde.

     Pode selecionar um grupo de consumidores existente ou criar um novo grupo de consumidores quando adicionar a fonte do evento. Por favor, note que a fonte do evento requer um grupo de consumidores único para o seu ambiente ler dados nele.

   * Escolha a propriedade timetamp apropriada. Por predefinição, o Azure Time Series Insights utiliza o tempo de mensagem para cada fonte de evento.

     > [!TIP]
     > O tempo de mensagem pode não ser a melhor configuração configurada para ser usado em cenários de eventos de lote ou cenários históricos de upload de dados. Nesses casos, certifique-se de verificar a sua decisão de utilizar ou não utilizar uma propriedade Timestamp.

     [![Separador de configuração de Fonte de Evento](media/v2-update-manage/create-and-manage-event-source.png)](media/v2-update-manage/create-and-manage-event-source.png#lightbox)

1. Confirme que o seu ambiente foi aprovisionado e configurado da forma que deseja.

    [![Comentário + Criar separador](media/v2-update-manage/create-and-manage-review-and-confirm.png)](media/v2-update-manage/create-and-manage-review-and-confirm.png#lightbox)

## <a name="manage-the-environment"></a>Gerir o ambiente

Pode gerir o seu ambiente Azure Time Series Insights Gen2 utilizando o portal Azure. Existem algumas diferenças fundamentais entre um ambiente Gen2 e os ambientes Gen1 S1 ou Gen1 S2 a ter em conta quando gere o seu ambiente através do portal Azure:

* A lâmina de **visão geral**  do portal Azure Gen2 tem as seguintes alterações:

  * A capacidade é removida porque não se aplica aos ambientes da Gen2.
  * A propriedade **de ID da série Time** é adicionada. Determina como os seus dados são divididos.
  * Os conjuntos de dados de referência são removidos.
  * O URL apresentado direciona-o para o [Azure Time Series Insights Explorer](./concepts-ux-panels.md).
  * O nome da sua conta Azure Storage está listado.

* A lâmina **de configuração** do portal Azure é removida porque as unidades de escala não se aplicam aos ambientes Azure Time Series Insights Gen2. No entanto, pode utilizar a **Configuração de Armazenamento** para configurar a loja quente recentemente introduzida.

* A lâmina de **dados** de referência do portal Azure é removida no Azure Time Series Insights Gen2 porque o conceito de dados de referência foi substituído pelo [Modelo de Séries de Tempo (TSM)](./concepts-model-overview.md).

[![Azure Time Series Insights Gen2 ambiente no portal Azure](media/v2-update-manage/create-and-manage-overview-confirm.png)](media/v2-update-manage/create-and-manage-overview-confirm.png#lightbox)

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre a Azure Time Series Insights geralmente disponíveis ambientes e ambientes Gen2 lendo [Planeie o seu ambiente](./how-to-plan-your-environment.md).

* Saiba como [adicionar uma fonte de centro de eventos.](./how-to-ingest-data-event-hub.md)

* Configure uma [fonte de hub IoT](./how-to-ingest-data-iot-hub.md).