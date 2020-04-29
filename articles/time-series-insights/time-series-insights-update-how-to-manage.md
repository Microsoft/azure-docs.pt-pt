---
title: Provision and manage a Preview environment - Azure Time Series [ Microsoft Docs
description: Aprenda a fornecer e gerir um ambiente de pré-visualização da Série De Tempo Azure Insights.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77087210"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Provision and manage Azure Time Series Insights Preview

Este artigo descreve como criar e gerir um ambiente de pré-visualização da Série De Tempo Azure Insights utilizando o [portal Azure](https://portal.azure.com/).

## <a name="overview"></a>Descrição geral

Os ambientes de pré-visualização da Série De Tempo Azure Insights são ambientes *pay-as-you-go* (PAYG).

Ao fornecer um ambiente de pré-visualização de Insights da Série De Tempo Azure, cria estes recursos Azure:

* Um ambiente de pré-visualização da série de tempo azure Insights  
* Uma conta V1 de uso geral de armazenamento azure
* Uma loja quente opcional para consultas mais rápidas e ilimitadas

> [!TIP]
> * Aprenda [a planear o seu ambiente.](./time-series-insights-update-plan.md)
> * Leia sobre como adicionar uma fonte de centro de [eventos](./time-series-insights-how-to-add-an-event-source-eventhub.md) ou como [adicionar uma fonte de hub IoT](./time-series-insights-how-to-add-an-event-source-iothub.md).

Vai aprender a:

1. **(Opcional)** Associe cada ambiente de pré-visualização da Série de Tempo Azure com uma fonte de evento. Você também fornecerá uma propriedade timestamp ID e um grupo de consumidores único para garantir que o ambiente tem acesso aos eventos apropriados.

   > [!NOTE]
   > O passo anterior é facultativo ao providenciar um ambiente. Se saltar este passo, deve anexar uma fonte de evento ao ambiente mais tarde para que os dados possam ser acedidos no ambiente.

1. Após o fornecimento estar completo, pode modificar as suas políticas de acesso e outros atributos ambientais de acordo com os seus requisitos de negócio.

## <a name="create-the-environment"></a>Criar o ambiente

Para criar um ambiente de pré-visualização da Série De Tempo Azure Insights:

1. Selecione **PAYG** como o **Tier**. Forneça um nome de ambiente e escolha o grupo de subscrição e o grupo de recursos para usar. Em seguida, selecione um local suportado para hospedar o ambiente.

   [![Crie um exemplo de Insights da Série Temporal Azure.](media/v2-update-manage/create-and-manage-configuration.png)](media/v2-update-manage/create-and-manage-configuration.png#lightbox)

1. Introduza um ID da Série De Tempo.

    > [!NOTE]
    > * O ID da Série Time é *sensível a casos* e *imutável.* (Não pode ser mudado depois de definido.)
    > * Os IDs da Série Tempora podem chegar a *três* chaves.
    > * Leia mais sobre [como escolher um ID](time-series-insights-update-how-to-id.md) da Série De Tempo

1. Crie uma conta de Armazenamento Azure selecionando um nome de conta de armazenamento e designando uma escolha de replicação. Fazê-lo automaticamente cria uma conta V1 de uso geral de armazenamento azure. A conta é criada na mesma região que o ambiente de pré-visualização da Série De Tempo Azure insights que selecionou anteriormente.

    [![Configuração de armazenamento frio](media/v2-update-manage/create-and-manage-cold-store.png)](media/v2-update-manage/create-and-manage-cold-store.png#lightbox)

1. **(Opcional)** Ative uma loja quente para o seu ambiente se quiser consultas mais rápidas e ilimitadas sobre os dados mais recentes no seu ambiente. Também pode criar ou eliminar uma loja quente através da opção **configuração** de armazenamento no painel de navegação esquerdo, depois de criar um ambiente de pré-visualização de Insights da Série De Tempo.

    [![Configuração de armazenamento quente](media/v2-update-manage/create-and-manage-warm-storage.png)](media/v2-update-manage/create-and-manage-warm-storage.png#lightbox)

1. **(Opcional)** Pode adicionar uma fonte de evento agora. Também pode esperar até que a instância tenha sido prevista.

   * Time Series Insights suporta [O Hub Azure IoT](./time-series-insights-how-to-add-an-event-source-iothub.md) e [o Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) como opções de fonte de eventos. Embora possa adicionar apenas uma única fonte de evento quando criar o ambiente, pode adicionar outra fonte de evento mais tarde. 
   
     Pode selecionar um grupo de consumidores existente ou criar um novo grupo de consumidores quando adicionar a fonte do evento. É melhor criar um grupo de consumidores único para garantir que todos os eventos são visíveis para o seu ambiente de pré-visualização da Série de Tempo Azure Insights.

   * Escolha a propriedade timestamp apropriada. Por padrão, a Azure Time Series Insights utiliza o tempo enquecto de mensagem para cada fonte de evento.

     > [!TIP]
     > O tempo enqueuado por mensagens pode não ser a melhor configuração configurada para usar em cenários de eventos de lote ou cenários históricos de upload de dados. Nesses casos, certifique-se de verificar a sua decisão de utilizar ou não utilizar uma propriedade timestamp.

     [![Separador de configuração de origem de eventos](media/v2-update-manage/create-and-manage-event-source.png)](media/v2-update-manage/create-and-manage-event-source.png#lightbox)

1. Confirme que o seu ambiente foi aprovisionado e configurado da forma que pretende.

    [![Rever + Criar o separador](media/v2-update-manage/create-and-manage-review-and-confirm.png)](media/v2-update-manage/create-and-manage-review-and-confirm.png#lightbox)

## <a name="manage-the-environment"></a>Gerir o ambiente

Pode gerir o seu ambiente de pré-visualização da Série De Tempo Azure através do portal Azure. Existem algumas diferenças fundamentais entre um ambiente de pré-visualização da Série de Tempo PAYG Azure Insights e os ambientes S1 ou S2 geralmente disponíveis para ter em conta quando gere o seu ambiente através do portal Azure:

* A lâmina de **visualização do** portal Azure Preview tem as seguintes alterações:

  * A capacidade é removida porque não se aplica aos ambientes PAYG.
  * A propriedade id da **série time** é adicionada. Determina como os seus dados são divididos.
  * Os conjuntos de dados de referência são removidos.
  * O URL apresentado direciona-o para o explorador de [pré-visualização](./time-series-insights-update-explorer.md)da Série de Tempo Azure Insights .
  * O nome da sua conta Azure Storage está listado.

* A lâmina **configure** do portal Azure é removida na Pré-visualização da Série de Tempo Azure Porque os ambientes PAYG não são configuráveis. No entanto, pode utilizar a **Configuração** de Armazenamento para configurar a loja quente recém-introduzida.

* A lâmina de **dados** de referência do portal Azure é removida na Pré-visualização da Série de Tempo Azure Porque os dados de referência não fazem parte dos ambientes PAYG.

[![Time Series Insights Preview ambiente no portal Azure](media/v2-update-manage/create-and-manage-overview-confirm.png)](media/v2-update-manage/create-and-manage-overview-confirm.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os insights da Time Series geralmente disponíveis ambientes e ambientes de pré-visualização, lendo [Plan o seu ambiente.](./time-series-insights-update-plan.md)

- Saiba como [adicionar uma fonte](./time-series-insights-how-to-add-an-event-source-eventhub.md)de hub de eventos.

- Configure uma [fonte de hub IoT](./time-series-insights-how-to-add-an-event-source-iothub.md).
