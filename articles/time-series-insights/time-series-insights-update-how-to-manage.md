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
ms.date: 10/23/2019
ms.custom: seodec18
ms.openlocfilehash: f66925c20f6767286eb98a7f5fab180845da4c33
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014788"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Provisionar e gerenciar Azure Time Series Insights visualização

Este artigo descreve como criar e gerenciar um ambiente de visualização de Azure Time Series Insights usando o [portal do Azure](https://portal.azure.com/).

## <a name="overview"></a>Descrição geral

Azure Time Series Insights ambientes de visualização são ambientes PAYG (pré-pago).

Ao provisionar um ambiente de visualização de Azure Time Series Insights, você cria esses recursos do Azure:

* Um ambiente de visualização Azure Time Series Insights  
* Uma conta v1 de uso geral do armazenamento do Azure
* Um armazenamento inesperado opcional para consultas mais rápidas e ilimitadas
  
Saiba [como planejar seu ambiente](./time-series-insights-update-plan.md).

Associar cada ambiente de visualização de Azure Time Series Insights a uma origem de evento. Para obter mais informações, leia [Adicionar uma origem do hub de eventos](./time-series-insights-how-to-add-an-event-source-eventhub.md) e [Adicionar uma origem do Hub IOT](./time-series-insights-how-to-add-an-event-source-iothub.md). Você fornece uma propriedade de ID de carimbo de data/hora e um grupo de consumidores exclusivo durante esta etapa. Isso garante que o ambiente tenha acesso aos eventos apropriados.

> [!NOTE]
> A etapa anterior é opcional no fluxo de trabalho de provisionamento enquanto você cria o ambiente de visualização de série temporal. No entanto, você deve anexar uma origem do evento ao ambiente para que os dados possam começar a fluir nesse ambiente.

Após a conclusão do provisionamento, você pode modificar suas políticas de acesso e outros atributos de ambiente para atender às suas necessidades de negócios.

## <a name="create-the-environment"></a>Criar o ambiente

Para criar um ambiente de visualização de Azure Time Series Insights:

1. No menu **SKU** , selecione o botão **PAYG** . Forneça um nome de ambiente e escolha o grupo de assinaturas e o grupo de recursos a serem usados. Em seguida, selecione um local com suporte para hospedar o ambiente.

   [![criar uma instância de Azure Time Series Insights.](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

1. Insira uma ID de série temporal.

    >[!NOTE]
    > * A ID da série temporal diferencia maiúsculas de minúsculas e imutável. (Ele não pode ser alterado após ser definido.)
    > * As IDs de série temporal podem ter até três chaves.
    > * Para obter mais informações sobre como selecionar uma ID de série temporal, leia [escolher uma ID de série temporal](./time-series-insights-update-how-to-id.md).

1. Crie uma conta de armazenamento do Azure selecionando um nome de conta de armazenamento e designando uma opção de replicação. Fazer isso cria automaticamente uma conta v1 de uso geral do armazenamento do Azure. A conta é criada na mesma região que o ambiente de visualização de Azure Time Series Insights que você selecionou anteriormente.

    [![criar uma conta de armazenamento do Azure para sua instância](media/v2-update-manage/manage-five.png)](media/v2-update-manage/manage-five.png#lightbox)

1. **(Opcional)** Habilite a loja a quente para seu ambiente se você quiser consultas mais rápidas e ilimitadas sobre os dados mais recentes em seu ambiente. Você também pode criar ou excluir uma loja a quente por meio da opção de **configuração de armazenamento** no painel de navegação esquerdo, depois de criar um ambiente de visualização de time Series insights.

1. **(Opcional)** Você pode adicionar uma origem do evento agora. Como alternativa, você pode aguardar até que a instância tenha sido provisionada.

   * O Time Series Insights dá suporte ao [Hub IOT do Azure](./time-series-insights-how-to-add-an-event-source-iothub.md) e aos [hubs de eventos do Azure](./time-series-insights-how-to-add-an-event-source-eventhub.md) como opções de origem do evento. Embora seja possível adicionar apenas uma única origem do evento ao criar o ambiente, você pode adicionar outra origem do evento posteriormente. Você pode selecionar um grupo de consumidores existente ou criar um novo grupo de consumidores ao adicionar a origem do evento. É melhor criar um grupo de consumidores exclusivo para garantir que todos os eventos estejam visíveis para o ambiente de visualização de Azure Time Series Insights.

   * Escolha a propriedade de carimbo de data/hora apropriada. Por padrão, Azure Time Series Insights usa o tempo de enfileiramento de mensagens para cada origem de evento.

     > [!TIP]
     > O tempo de enfileiramento de mensagens pode não ser a configuração mais adequada para uso em cenários de eventos de lote ou em cenários de carregamento de dados históricos. Nesses casos, certifique-se de verificar sua decisão de usar ou não usar uma propriedade Timestamp.

     [guia origem do evento ![](media/v2-update-manage/manage-two.png)](media/v2-update-manage/manage-two.png#lightbox)

1. Confirme se o seu ambiente foi provisionado com as configurações desejadas.

    [![revisar + criar guia](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

## <a name="manage-the-environment"></a>Gerenciar o ambiente

Você pode gerenciar seu ambiente de Azure Time Series Insights visualização usando o portal do Azure. Quando você gerencia o por meio do portal do Azure, você vê algumas diferenças importantes entre um ambiente de visualização de pré-pago Azure Time Series Insights e os ambientes S1 ou S2 geralmente disponíveis:

* A folha de **visão geral** do portal do Azure não é alterada em Azure Time Series insights, exceto das seguintes maneiras:
  * A capacidade é removida porque não se aplica aos ambientes pagos conforme o uso.
  * A propriedade ID da série temporal é adicionada. Ele determina como os dados são particionados.
  * Os conjuntos de dados de referência são removidos.
  * A URL exibida direciona você para o [Gerenciador de visualização Azure Time Series insights](./time-series-insights-update-explorer.md).
  * O nome da conta de armazenamento do Azure está listado.

* A folha **Configurar** do portal do Azure é removida na visualização Azure Time Series insights porque ambientes PAYG não são configuráveis. No entanto, você pode usar a **configuração de armazenamento** para configurar o armazenamento quente introduzido recentemente.

* A folha de **dados de referência** do portal do Azure é removida na visualização Azure Time Series insights porque os dados de referência não fazem parte dos ambientes pagos conforme o uso.

[![Time Series Insights ambiente de visualização no portal do Azure](media/v2-update-manage/manage-four.png)](media/v2-update-manage/manage-four.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre Time Series Insights ambientes geralmente disponíveis e ambientes de visualização lendo [planejar seu ambiente](./time-series-insights-update-plan.md).

- Saiba como [Adicionar uma origem do hub de eventos](./time-series-insights-how-to-add-an-event-source-eventhub.md).

- Configurar uma [origem do Hub IOT](./time-series-insights-how-to-add-an-event-source-iothub.md).
