---
title: Aprovisionar e gerir a pré-visualização de série de tempo do Azure | Documentos da Microsoft
description: Noções básicas sobre como aprovisionar e gerir a pré-visualização do Azure Time Series Insights.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2018
ms.custom: seodec18
ms.openlocfilehash: 3c6b5094613ee482b0fd3f63e3d9d69dfe4d40f4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58094876"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Aprovisionar e gerir a pré-visualização do Azure Time Series Insights

Este artigo descreve como criar e gerir um ambiente de pré-visualização do Azure Time Series Insights com o [portal do Azure](https://portal.azure.com/).

## <a name="overview"></a>Descrição geral

Ambientes do Time Series Insights pré-visualização do Azure são ambientes de pay as you go (PAYG).

Quando aprovisiona um ambiente de pré-visualização do Azure Time Series Insights, cria dois recursos do Azure:

* Um ambiente de pré-visualização do Azure Time Series Insights  
* Uma conta do armazenamento do Azure para fins gerais v1
  
Saiba mais [como planejar seu ambiente](./time-series-insights-update-plan.md).

>[!IMPORTANT]
> Se estiver a utilizar uma conta de v2, não ative a frio/arquivamento propriedades na conta de armazenamento que irá utilizar.

Opcionalmente, pode associar cada ambiente de pré-visualização do Azure Time Series Insights uma origem de evento. Para obter mais informações, leia [adicionar uma origem de hub de eventos](./time-series-insights-how-to-add-an-event-source-eventhub.md) e [adicionar uma origem do hub IoT](./time-series-insights-how-to-add-an-event-source-iothub.md). Fornece uma propriedade de ID de Timestamp e um grupo de consumidores exclusivo durante este passo. Isso faz com que o ambiente tem acesso para os eventos adequados.

Depois de o aprovisionamento estiver concluído, pode modificar as políticas de acesso e outros atributos de ambiente para atender às suas necessidades de negócio.

## <a name="create-the-environment"></a>Criar o ambiente

Os passos seguintes descrevem como criar um ambiente de pré-visualização do Azure Time Series Insights:

1. Selecione o **PAYG** botão sob os **SKU** menu. Fornecer um nome de ambiente e escolha o grupo de subscrição e o grupo de recursos para utilizar. Em seguida, selecione uma localização suportada para o ambiente para serem alojadas na.

   ![Crie uma instância do Azure Time Series Insights.][1]

1. Introduza uma série de tempo ID.

    >[!NOTE]
    > * O ID de série de tempo é imutável e diferencia maiúsculas de minúsculas. (Ele não pode ser alterado após ser definido.)
    > * IDs de série de tempo pode ser até três chaves.
    > * Para obter mais informações acerca da seleção de um ID de série de tempo, leia [escolher um ID de série de tempo](./time-series-insights-update-how-to-id.md).

1. Crie uma conta de armazenamento do Azure ao selecionar um nome de conta de armazenamento e designar uma opção de replicação. Fazer automaticamente cria uma conta do armazenamento do Azure para fins gerais v1. Será criada na mesma região que o ambiente de pré-visualização do Azure Time Series Insights que selecionou anteriormente.

    ![Criar uma conta de armazenamento do Azure para a sua instância][5]

1. Opcionalmente, pode adicionar uma origem de evento.

   * Time Series Insights suporta [IoT Hub do Azure](./time-series-insights-how-to-add-an-event-source-iothub.md) e [Event Hubs do Azure](./time-series-insights-how-to-add-an-event-source-eventhub.md) como opções. Embora possa adicionar uma origem de evento único no momento de criação do ambiente, é possível adicionar outra origem de evento mais tarde. É melhor criar um grupo de consumidores exclusivo para garantir que todos os eventos estão visíveis para a sua instância de pré-visualização do Azure Time Series Insights. Pode selecionar um grupo de consumidor existente ou criar um novo grupo de consumidor, ao adicionar a origem do evento.

   * Também deve escolher a propriedade Timestamp apropriada. Por predefinição, o Azure Time Series Insights utiliza a hora de colocados em fila de mensagens para cada origem de evento.

     > [!TIP]
     > O tempo de colocados em fila de mensagens pode não ser a melhor definição configurada para utilizar no evento de batch ou cenários de carregamento de dados históricos. Certifique-se verificar a sua decisão de usar ou não usar uma propriedade Timestamp nestes casos.

     ![Separador de origem do evento][2]

1. Confirme que o seu ambiente tiver sido aprovisionado com as definições pretendidas.

    ![Rever + criar separador][3]

## <a name="manage-the-environment"></a>Gerir o ambiente

Pode gerenciar seu ambiente de pré-visualização do Azure Time Series Insights com o portal do Azure. Aqui estão as principais diferenças de gerir um ambiente de pré-visualização do PAYG do Azure Time Series Insights, em vez de um S1 ou S2 ambiente, ao utilizar o portal do Azure:

* O portal do Azure **descrição geral** painel permanece inalterado no Azure Time Series Insights, com exceção das seguintes formas:
  * Capacidade for removida, porque não é relevante para ambientes de PAYG esse conceito.
  * Foi adicionada a propriedade de ID de série de tempo. Determina como os seus dados estão particionados.
  * Conjuntos de dados de referência são removidos.
  * A URL exibida lhe dá instruções para o [Explorador de pré-visualização do Azure Time Series Insights](./time-series-insights-update-explorer.md).
  * O nome da sua conta de armazenamento do Azure está listado.

* O portal do Azure **configurar** painel foi removido na pré-visualização do Azure Time Series Insights porque ambientes PAYG não são configuráveis.

* O portal do Azure **dados de referência** painel foi removido na pré-visualização do Azure Time Series Insights porque os dados de referência não são um componente de ambientes de PAYG.

![Ambiente de pré-visualização de informações de série de tempo no portal do Azure][4]

## <a name="next-steps"></a>Passos Seguintes

Leia [planejar seu ambiente](./time-series-insights-update-plan.md).

Leia [adicionar uma origem de hub de eventos](./time-series-insights-how-to-add-an-event-source-eventhub.md).

Leia [adicionar uma origem do hub IoT](./time-series-insights-how-to-add-an-event-source-iothub.md).

<!-- Images -->
[1]: media/v2-update-manage/manage_one.PNG
[2]: media/v2-update-manage/manage_two.PNG
[3]: media/v2-update-manage/manage_three.PNG
[4]: media/v2-update-manage/manage_four.PNG
[5]: media/v2-update-manage/manage_five.PNG
