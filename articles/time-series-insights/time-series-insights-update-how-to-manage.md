---
title: Provisão e gestão de um ambiente de pré-visualização - Azure Time Series / Microsoft Docs
description: Aprenda a fornecer e gerir um ambiente de visualização de insights de séries de tempo Azure.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 1ec0d9c7ecf16c60c32abdf08b358268f460edb0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77087210"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Provisão e gestão de insights de séries de tempo Azure

Este artigo descreve como criar e gerir um ambiente de visualização de insights da série de tempo Azure utilizando o [portal Azure](https://portal.azure.com/).

## <a name="overview"></a>Descrição geral

Azure Time Series Insights Os ambientes de visualização são ambientes *pay-as-you-go* (PAYG).

Ao fornecer um ambiente de visualização de insights de séries de tempo Azure, cria estes recursos Azure:

* Um ambiente de visualização de insights de séries de tempo Azure  
* Uma conta V1 de armazenamento Azure
* Uma loja de aquecimento opcional para consultas mais rápidas e ilimitadas

> [!TIP]
> * Saiba [como planear o seu ambiente.](./time-series-insights-update-plan.md)
> * Leia sobre como [adicionar uma fonte de hub](./time-series-insights-how-to-add-an-event-source-eventhub.md) de evento ou como adicionar uma fonte de hub [IoT](./time-series-insights-how-to-add-an-event-source-iothub.md).

Vai aprender a:

1. **(Opcional)** Associar cada ambiente de visualização de séries de tempo Azure com uma fonte de evento. Você também fornecerá uma propriedade de ID Timestamp e um grupo de consumidores único para garantir que o ambiente tem acesso aos eventos apropriados.

   > [!NOTE]
   > O passo anterior é facultativo quando se disponibiliza um ambiente. Se saltar este passo, deve anexar uma fonte de evento ao ambiente mais tarde para que os dados possam ser acedidos no ambiente.

1. Após o fornecimento estar completo, pode modificar as suas políticas de acesso e outros atributos ambientais de acordo com os requisitos do seu negócio.

## <a name="create-the-environment"></a>Criar o ambiente

Para criar um ambiente de visualização de insights de séries de tempo Azure:

1. Selecione **PAYG** como **o Tier**. Forneça um nome ambiental e escolha o grupo de subscrição e o grupo de recursos para usar. Em seguida, selecione um local suportado para hospedar o ambiente.

   [![Crie uma instância de Insights da Série de Tempo Azure.](media/v2-update-manage/create-and-manage-configuration.png)](media/v2-update-manage/create-and-manage-configuration.png#lightbox)

1. Introduza um ID da Série Temporal.

    > [!NOTE]
    > * O ID da Série De Tempo é *sensível a casos* e *imutável*. (Não pode ser mudado depois de definido.)
    > * Os IDs da Série De Tempo podem chegar a *três* teclas.
    > * Leia mais sobre [Como escolher um ID da Série De Tempo](time-series-insights-update-how-to-id.md)

1. Crie uma conta de Armazenamento Azure selecionando um nome de conta de armazenamento e designando uma escolha de replicação. Ao fazê-lo, cria-se automaticamente uma conta V1 para fins gerais do Azure Storage. A conta é criada na mesma região que o ambiente de pré-visualização da Série de TempoS Azure que selecionou anteriormente.

    [![Configuração de armazenamento a frio](media/v2-update-manage/create-and-manage-cold-store.png)](media/v2-update-manage/create-and-manage-cold-store.png#lightbox)

1. **(Opcional)** Ative a loja quente para o seu ambiente se quiser consultas mais rápidas e ilimitadas sobre os dados mais recentes no seu ambiente. Também pode criar ou eliminar uma loja quente através da opção **de configuração de armazenamento** no painel de navegação esquerdo, depois de criar um ambiente de pré-visualização de insights de séries temporítem.

    [![Configuração de armazenamento quente](media/v2-update-manage/create-and-manage-warm-storage.png)](media/v2-update-manage/create-and-manage-warm-storage.png#lightbox)

1. **(Opcional)** Pode adicionar uma fonte de evento agora. Também pode esperar até que o caso tenha sido a provisionado.

   * Time Series Insights suporta [Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) e [Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) como opções de origem de eventos. Embora possa adicionar apenas uma única fonte de eventos quando criar o ambiente, pode adicionar outra fonte de evento mais tarde. 
   
     Pode selecionar um grupo de consumidores existente ou criar um novo grupo de consumidores quando adicionar a fonte do evento. É melhor criar um grupo de consumidores único para garantir que todos os eventos sejam visíveis para o seu ambiente de visualização de insights de séries de tempo Azure.

   * Escolha a propriedade timetamp apropriada. Por predefinição, o Azure Time Series Insights utiliza o tempo de mensagem para cada fonte de evento.

     > [!TIP]
     > O tempo de mensagem pode não ser a melhor configuração configurada para ser usado em cenários de eventos de lote ou cenários históricos de upload de dados. Nesses casos, certifique-se de verificar a sua decisão de utilizar ou não utilizar uma propriedade Timestamp.

     [![Separador de configuração de Fonte de Evento](media/v2-update-manage/create-and-manage-event-source.png)](media/v2-update-manage/create-and-manage-event-source.png#lightbox)

1. Confirme que o seu ambiente foi aprovisionado e configurado da forma que deseja.

    [![Comentário + Criar separador](media/v2-update-manage/create-and-manage-review-and-confirm.png)](media/v2-update-manage/create-and-manage-review-and-confirm.png#lightbox)

## <a name="manage-the-environment"></a>Gerir o ambiente

Pode gerir o seu ambiente de pré-visualização de Insights da Série De Tempo Azure utilizando o portal Azure. Existem algumas diferenças fundamentais entre um ambiente de pré-visualização da Série de Tempos PAYG Azure e os ambientes S1 ou S2 geralmente disponíveis para ter em conta quando gere o seu ambiente através do portal Azure:

* A **lâmina** de visualização geral do portal Azure tem as seguintes alterações:

  * A capacidade é removida porque não se aplica aos ambientes PAYG.
  * A propriedade **de ID da série Time** é adicionada. Determina como os seus dados são divididos.
  * Os conjuntos de dados de referência são removidos.
  * O URL apresentado direciona-o para o [explorador de pré-visualização da Série de Tempo Azure.](./time-series-insights-update-explorer.md)
  * O nome da sua conta Azure Storage está listado.

* A lâmina **de configuração** do portal Azure é removida no Azure Time Series Insights Preview porque os ambientes PAYG não são configuráveis. No entanto, pode utilizar a **Configuração de Armazenamento** para configurar a loja quente recentemente introduzida.

* A lâmina de **dados** de referência do portal Azure é removida no Azure Time Series Insights Preview porque os dados de referência não fazem parte dos ambientes PAYG.

[![Séries de tempo Insights Visualização ambiente no portal Azure](media/v2-update-manage/create-and-manage-overview-confirm.png)](media/v2-update-manage/create-and-manage-overview-confirm.png#lightbox)

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre as séries de tempo Insights geralmente disponíveis ambientes e ambientes de pré-visualização, lendo [Planeie o seu ambiente](./time-series-insights-update-plan.md).

- Saiba como [adicionar uma fonte de centro de eventos.](./time-series-insights-how-to-add-an-event-source-eventhub.md)

- Configure uma [fonte de hub IoT](./time-series-insights-how-to-add-an-event-source-iothub.md).
