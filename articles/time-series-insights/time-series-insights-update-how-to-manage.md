---
title: Provisionar e gerenciar a visualização da série temporal do Azure | Microsoft Docs
description: Noções básicas sobre como provisionar e gerenciar Azure Time Series Insights visualização.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/04/2019
ms.custom: seodec18
ms.openlocfilehash: a84c587a2666982e862f0640b30d2d6bef3b9a42
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70744673"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Provisionar e gerenciar Azure Time Series Insights visualização

Este artigo descreve como criar e gerenciar um ambiente de visualização de Azure Time Series Insights usando o [portal do Azure](https://portal.azure.com/).

## <a name="overview"></a>Descrição geral

Azure Time Series Insights ambientes de visualização são ambientes PAYG (pré-pago).

Ao provisionar um ambiente de visualização de Azure Time Series Insights, você cria dois recursos do Azure:

* Um ambiente de visualização Azure Time Series Insights  
* Uma conta v1 de uso geral do armazenamento do Azure
  
Saiba [como planejar seu ambiente](./time-series-insights-update-plan.md).

>[!IMPORTANT]
> Para a versão prévia, verifique se você está usando uma conta de uso geral V1 (GPv1) do armazenamento do Azure.

Opcionalmente, você pode associar cada ambiente de visualização de Azure Time Series Insights a uma origem de evento. Para obter mais informações, leia [Adicionar uma origem do hub de eventos](./time-series-insights-how-to-add-an-event-source-eventhub.md) e [Adicionar uma origem do Hub IOT](./time-series-insights-how-to-add-an-event-source-iothub.md). Você fornece uma propriedade de ID de carimbo de data/hora e um grupo de consumidores exclusivo durante esta etapa. Isso garante que o ambiente tenha acesso aos eventos apropriados.

Após a conclusão do provisionamento, você pode modificar suas políticas de acesso e outros atributos de ambiente para atender às suas necessidades de negócios.

## <a name="create-the-environment"></a>Criar o ambiente

As etapas a seguir descrevem como criar um ambiente de visualização de Azure Time Series Insights:

1. Selecione o botão **PAYG** no menu **SKU** . Forneça um nome de ambiente e escolha qual grupo de assinatura e qual grupo de recursos usar. Em seguida, selecione um local com suporte no qual o ambiente seja hospedado.

   [![Crie uma instância de Azure Time Series Insights.](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

1. Insira uma ID de série temporal.

    >[!NOTE]
    > * A ID da série temporal diferencia maiúsculas de minúsculas e imutável. (Ele não pode ser alterado após ser definido.)
    > * As IDs de série temporal podem ter até três chaves.
    > * Para obter mais informações sobre como selecionar uma ID de série temporal, leia [escolher uma ID de série temporal](./time-series-insights-update-how-to-id.md).

1. Crie uma conta de armazenamento do Azure selecionando um nome de conta de armazenamento e designando uma opção de replicação. Fazer isso cria automaticamente uma conta v1 de uso geral do armazenamento do Azure. Ele será criado na mesma região que o ambiente de visualização de Azure Time Series Insights que você selecionou anteriormente.

    [![Criar uma conta de armazenamento do Azure para sua instância](media/v2-update-manage/manage-five.png)](media/v2-update-manage/manage-five.png#lightbox)

1. Opcionalmente, você pode adicionar uma origem do evento.

   * O Time Series Insights dá suporte ao [Hub IOT do Azure](./time-series-insights-how-to-add-an-event-source-iothub.md) e aos [hubs de eventos do Azure](./time-series-insights-how-to-add-an-event-source-eventhub.md) como opções. Embora seja possível adicionar apenas uma única origem do evento no momento da criação do ambiente, você pode adicionar outra origem do evento posteriormente. É melhor criar um grupo de consumidores exclusivo para garantir que todos os eventos estejam visíveis para sua instância de visualização de Azure Time Series Insights. Você pode selecionar um grupo de consumidores existente ou criar um novo grupo de consumidores ao adicionar a origem do evento.

   * Você também deve escolher a propriedade de carimbo de data/hora apropriada. Por padrão, Azure Time Series Insights usa o tempo de enfileiramento da mensagem para cada origem de evento.

     > [!TIP]
     > O tempo de enfileiramento da mensagem pode não ser a configuração mais adequada para uso em cenários de carregamento de dados de lote ou históricos. Certifique-se de verificar sua decisão de usar ou não usar uma propriedade Timestamp nesses casos.

     [![Guia origem do evento](media/v2-update-manage/manage-two.png)](media/v2-update-manage/manage-two.png#lightbox)

1. Confirme se o seu ambiente foi provisionado com as configurações desejadas.

    [![Revisar + criar guia](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

## <a name="manage-the-environment"></a>Gerenciar o ambiente

Você pode gerenciar seu ambiente de Azure Time Series Insights visualização usando o portal do Azure. Aqui estão as principais diferenças no gerenciamento de um ambiente de visualização do PAYG Azure Time Series Insights, em oposição a um ambiente S1 ou S2, usando o portal do Azure:

* A folha de **visão geral** do portal do Azure não é alterada em Azure Time Series insights, exceto das seguintes maneiras:
  * A capacidade é removida, pois esse conceito não é relevante para ambientes PAYG.
  * A propriedade ID da série temporal foi adicionada. Ele determina como os dados são particionados.
  * Os conjuntos de dados de referência são removidos.
  * A URL exibida direciona você para o [Gerenciador de visualização Azure Time Series insights](./time-series-insights-update-explorer.md).
  * O nome da conta de armazenamento do Azure está listado.

* A folha **Configurar** do portal do Azure foi removida na visualização Azure Time Series insights porque ambientes PAYG não são configuráveis.

* A folha de **dados de referência** do portal do Azure foi removida na visualização Azure Time Series insights porque os dados de referência não são um componente de ambientes PAYG.

[![Time Series Insights ambiente de visualização no portal do Azure](media/v2-update-manage/manage-four.png)](media/v2-update-manage/manage-four.png#lightbox)

## <a name="next-steps"></a>Passos Seguintes

- Leia [planejar seu ambiente](./time-series-insights-update-plan.md).

- Saiba como [Adicionar uma origem do hub de eventos](./time-series-insights-how-to-add-an-event-source-eventhub.md).

- Configurar [uma origem do Hub IOT](./time-series-insights-how-to-add-an-event-source-iothub.md).