---
title: Provisionar e gerenciar um ambiente de visualização – série temporal do Azure | Microsoft Docs
description: Saiba como provisionar e gerenciar um ambiente de visualização de Azure Time Series Insights.
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
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77087210"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Provisionar e gerenciar Azure Time Series Insights visualização

Este artigo descreve como criar e gerir um ambiente de pré-visualização da Série De Tempo Azure Insights utilizando o [portal Azure](https://portal.azure.com/).

## <a name="overview"></a>Descrição geral

Os ambientes de pré-visualização da Série De Tempo Azure Insights são ambientes *pay-as-you-go* (PAYG).

Ao provisionar um ambiente de visualização de Azure Time Series Insights, você cria esses recursos do Azure:

* Um ambiente de visualização Azure Time Series Insights  
* Uma conta v1 de uso geral do armazenamento do Azure
* Um armazenamento inesperado opcional para consultas mais rápidas e ilimitadas

> [!TIP]
> * Aprenda [a planear o seu ambiente.](./time-series-insights-update-plan.md)
> * Leia sobre como adicionar uma fonte de centro de [eventos](./time-series-insights-how-to-add-an-event-source-eventhub.md) ou como [adicionar uma fonte de hub IoT](./time-series-insights-how-to-add-an-event-source-iothub.md).

Vai aprender a:

1. **(Opcional)** Associe cada ambiente de pré-visualização da Série de Tempo Azure com uma fonte de evento. Você também fornecerá uma propriedade de ID de carimbo de data/hora e um grupo de consumidores exclusivo para garantir que o ambiente tenha acesso aos eventos apropriados.

   > [!NOTE]
   > A etapa anterior é opcional ao provisionar um ambiente. Se você ignorar esta etapa, deverá anexar uma origem do evento ao ambiente posteriormente para que os dados possam ser acessados no ambiente.

1. Após a conclusão do provisionamento, você pode modificar suas políticas de acesso e outros atributos de ambiente para atender às suas necessidades de negócios.

## <a name="create-the-environment"></a>Criar o ambiente

Para criar um ambiente de visualização de Azure Time Series Insights:

1. Selecione **PAYG** como o **Tier**. Forneça um nome de ambiente e escolha o grupo de assinaturas e o grupo de recursos a serem usados. Em seguida, selecione um local com suporte para hospedar o ambiente.

   [![Criar um exemplo de Insights da Série Temporal Azure.](media/v2-update-manage/create-and-manage-configuration.png)](media/v2-update-manage/create-and-manage-configuration.png#lightbox)

1. Insira uma ID de série temporal.

    > [!NOTE]
    > * O ID da Série Time é *sensível a casos* e *imutável.* (Ele não pode ser alterado após ser definido.)
    > * Os IDs da Série Tempora podem chegar a *três* chaves.
    > * Leia mais sobre [como escolher um ID](time-series-insights-update-how-to-id.md) da Série De Tempo

1. Crie uma conta de armazenamento do Azure selecionando um nome de conta de armazenamento e designando uma opção de replicação. Fazer isso cria automaticamente uma conta v1 de uso geral do armazenamento do Azure. A conta é criada na mesma região que o ambiente de visualização de Azure Time Series Insights que você selecionou anteriormente.

    [configuração de armazenamento frio ![](media/v2-update-manage/create-and-manage-cold-store.png)](media/v2-update-manage/create-and-manage-cold-store.png#lightbox)

1. **(Opcional)** Ative uma loja quente para o seu ambiente se quiser consultas mais rápidas e ilimitadas sobre os dados mais recentes no seu ambiente. Também pode criar ou eliminar uma loja quente através da opção **configuração** de armazenamento no painel de navegação esquerdo, depois de criar um ambiente de pré-visualização de Insights da Série De Tempo.

    [![Configuração de armazenamento quente](media/v2-update-manage/create-and-manage-warm-storage.png)](media/v2-update-manage/create-and-manage-warm-storage.png#lightbox)

1. **(Opcional)** Pode adicionar uma fonte de evento agora. Você também pode aguardar até que a instância tenha sido provisionada.

   * Time Series Insights suporta [O Hub Azure IoT](./time-series-insights-how-to-add-an-event-source-iothub.md) e [o Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) como opções de fonte de eventos. Embora seja possível adicionar apenas uma única origem do evento ao criar o ambiente, você pode adicionar outra origem do evento posteriormente. 
   
     Você pode selecionar um grupo de consumidores existente ou criar um novo grupo de consumidores ao adicionar a origem do evento. É melhor criar um grupo de consumidores exclusivo para garantir que todos os eventos estejam visíveis para o ambiente de visualização de Azure Time Series Insights.

   * Escolha a propriedade de carimbo de data/hora apropriada. Por padrão, Azure Time Series Insights usa o tempo de enfileiramento de mensagens para cada origem de evento.

     > [!TIP]
     > O tempo de enfileiramento de mensagens pode não ser a configuração mais adequada para uso em cenários de eventos de lote ou em cenários de carregamento de dados históricos. Nesses casos, certifique-se de verificar sua decisão de usar ou não usar uma propriedade Timestamp.

     [separador de configuração de fonte de evento ![](media/v2-update-manage/create-and-manage-event-source.png)](media/v2-update-manage/create-and-manage-event-source.png#lightbox)

1. Confirme se o seu ambiente foi provisionado e configurado da maneira desejada.

    [![Review + Criar o separador](media/v2-update-manage/create-and-manage-review-and-confirm.png)](media/v2-update-manage/create-and-manage-review-and-confirm.png#lightbox)

## <a name="manage-the-environment"></a>Gerenciar o ambiente

Você pode gerenciar seu ambiente de Azure Time Series Insights visualização usando o portal do Azure. Há algumas diferenças importantes entre um ambiente de visualização do PAYG Azure Time Series Insights e os ambientes S1 ou S2 geralmente disponíveis para se lembrar ao gerenciar seu ambiente por meio do portal do Azure:

* A lâmina de **visualização do** portal Azure Preview tem as seguintes alterações:

  * A capacidade é removida porque não se aplica a ambientes PAYG.
  * A propriedade id da **série time** é adicionada. Ele determina como os dados são particionados.
  * Os conjuntos de dados de referência são removidos.
  * O URL apresentado direciona-o para o explorador de [pré-visualização](./time-series-insights-update-explorer.md)da Série de Tempo Azure Insights .
  * O nome da conta de armazenamento do Azure está listado.

* A lâmina **configure** do portal Azure é removida na Pré-visualização da Série de Tempo Azure Porque os ambientes PAYG não são configuráveis. No entanto, pode utilizar a **Configuração** de Armazenamento para configurar a loja quente recém-introduzida.

* A lâmina de **dados** de referência do portal Azure é removida na Pré-visualização da Série de Tempo Azure Porque os dados de referência não fazem parte dos ambientes PAYG.

[![Time Series Insights Preview ambiente no portal Azure](media/v2-update-manage/create-and-manage-overview-confirm.png)](media/v2-update-manage/create-and-manage-overview-confirm.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os insights da Time Series geralmente disponíveis ambientes e ambientes de pré-visualização, lendo [Plan o seu ambiente.](./time-series-insights-update-plan.md)

- Saiba como [adicionar uma fonte](./time-series-insights-how-to-add-an-event-source-eventhub.md)de hub de eventos.

- Configure uma [fonte de hub IoT](./time-series-insights-how-to-add-an-event-source-iothub.md).
