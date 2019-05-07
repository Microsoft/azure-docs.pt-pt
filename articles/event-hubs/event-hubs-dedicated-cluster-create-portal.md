---
title: Início rápido do Azure - criar um cluster de dedicado de Hubs de eventos com o portal do Azure | Documentos da Microsoft
description: Neste início rápido, irá aprender a criar um cluster de Hubs de eventos do Azure com o portal do Azure.
services: event-hubs
documentationcenter: ''
author: xurui203
manager: ''
ms.service: event-hubs
ms.topic: quickstart
ms.custom: mvc
ms.date: 05/02/2019
ms.author: xurui
ms.openlocfilehash: ffdcd3e6314e659443630f74f8f17261659f1246
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65158512"
---
# <a name="quickstart-create-a-dedicated-event-hubs-cluster-preview-using-azure-portal"></a>Início rápido: Criar um cluster dos Hubs de eventos dedicado (pré-visualização) com o portal do Azure 
Clusters de Hubs de eventos oferecem implementações de inquilino único para os clientes com as necessidades de transmissão em fluxo mais exigentes. Esta oferta de inquilino único tem um SLA de 99,99% garantido e está disponível apenas no escalão de preço dedicado. Um cluster de Hubs de eventos pode entrada milhões de eventos por segundo com capacidade garantida e a latência de subsecond. Os hubs de eventos e espaços de nomes criados dentro do cluster dedicado incluem todas as funcionalidades da oferta standard e muito mais, mas sem limites de entrada. Ele também inclui o popular [captura de Hubs de eventos](event-hubs-capture-overview.md) funcionalidade sem custos adicionais, permitindo que automaticamente os dados de registo e batch transmite em fluxo para [armazenamento de Blobs do Azure](../storage/blobs/storage-blobs-introduction.md) ou [armazenamento do Azure Data Lake Ger 1](../data-lake-store/data-lake-store-overview.md). Para obter mais informações sobre clusters dedicados de Hubs de eventos, consulte [descrição geral dos Hubs de eventos dedicados](event-hubs-dedicated-overview.md).

Clusters dedicados são aprovisionados e a faturação é ao **unidades de capacidade (CUs)**, uma quantidade previamente alocada de recursos de CPU e memória. Pode comprar CUs 1, 2, 4, 8, 12, 16 ou 20 para cada cluster. Para aumentar verticalmente um cluster, primeiro crie um cluster com um valor de CU, em seguida, submeter uma [pedido de suporte](#submit-a-support-request-for-your-dedicated-cluster). 

Neste início rápido, irá criar um cluster dedicado de Hubs de eventos com o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este início rápido, certifique-se de que tem:

- Uma conta do Azure. Se não tiver uma, [Compre uma](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) antes de começar. Esta funcionalidade não é suportada com uma conta gratuita do Azure. 
- [Visual Studio](https://visualstudio.microsoft.com/vs/) 2017 atualização 3 (versão 15.3, 26730.01) ou posterior.
- [SDK .NET Standard](https://dotnet.microsoft.com/download), versão 2.0 ou posterior.
- [Criou um grupo de recursos](../event-hubs/event-hubs-create.md#create-a-resource-group).

## <a name="create-an-event-hubs-dedicated-cluster"></a>Criar um Cluster de dedicado de Hubs de eventos
Um cluster dedicado de Hubs de eventos fornece um contentor de âmbito exclusivo, referenciado pelo respetivo nome de domínio completamente qualificado, no qual pode criar um ou mais espaços de nomes. Na fase de pré-visualização do portal Self-Service experiência, pode criar um cluster CU nas regiões suportadas. Se precisar de um cluster maior do que um valor de CU, criar um cluster com um valor de CU primeiro e, em seguida, submeter um pedido para aumentar verticalmente o cluster. 

Para criar um cluster no seu grupo de recursos com o portal do Azure, siga os passos abaixo:

1. Na [portal do Azure](https://portal.azure.com), selecione **+ criar um recurso** no menu de navegação à esquerda.
2. Tipo **Clusters de Hubs de eventos** na barra de pesquisa, em seguida, prima ENTER.
3. Sobre o **criar clusters** página, siga os passos seguintes:
    1. Introduza um **nome para o cluster**. O sistema verifica imediatamente a disponibilidade do nome.
    2. Selecione o **subscrição** no qual pretende criar o cluster.
    3. Selecione o **grupo de recursos** no qual pretende criar o cluster.
    4. Selecione um **localização** para o cluster. Se a sua região preferencial está a cinzento, submeta um [pedido de suporte](#submit-a-support-request-for-your-dedicated-cluster).
    5. Selecione o **seguinte: Etiquetas** na parte inferior da página. Poderá ter de aguardar alguns minutos para que o sistema aprovisione totalmente os recursos.

        ![Criar o Cluster de Hubs de eventos - página de noções básicas](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-basics-page.png)
4. Sobre o **etiquetas** página, siga os passos seguintes:
    1. Introduza um **name** e uma **valor** para a etiqueta que pretende adicionar. Este passo é **opcional**.  
    2. Selecione o **rever + criar** botão.

        ![Criar Cluster de Hubs de eventos de página - página de etiquetas](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-tags-page.png)
5. Sobre o **rever + criar** página, reveja os detalhes e selecione **criar**. 

    ![Criar a página de Cluster de Hubs de eventos - rever + criar página](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-review-create-page.png)

## <a name="create-a-namespace-and-event-hub-within-a-cluster"></a>Criar um hub de eventos e de espaço de nomes dentro de um cluster

1. Para criar um espaço de nomes dentro de um cluster, o **Cluster de Hubs de eventos** página para o seu cluster, selecione **+ espaço de nomes** no menu superior.

    ![Página de gerenciamento de cluster – Adicionar botão de espaço de nomes](./media/event-hubs-dedicated-cluster-create-portal/cluster-management-page-add-namespace-button.png)
2. Em criar uma página de espaço de nomes, efetue os seguintes passos:
    1. Introduza um **nome para o espaço de nomes**.  O sistema verifica se o nome está disponível.
    2. O espaço de nomes herda as seguintes propriedades:
        1. ID da subscrição
        2. Grupo de Recursos
        3. Location
        4. Nome do Cluster
    3. Selecione **criar** para criar o espaço de nomes. Agora pode gerir o cluster.  

        ![Criar o espaço de nomes na página do cluster](./media/event-hubs-dedicated-cluster-create-portal/create-namespace-cluster-page.png)
3. Assim que o seu espaço de nomes é criado, pode [criar um hub de eventos](event-hubs-create.md#create-an-event-hub) como normalmente criaria um dentro de um espaço de nomes. 


## <a name="submit-a-support-request-for-your-dedicated-cluster"></a>Submeter um pedido de suporte para o cluster dedicado

Para submeter um pedido de suporte, siga estes passos:

1. Na [portal do Azure](https://portal.azure.com), selecione **ajuda + suporte** no menu à esquerda.
2. Selecione **+ novo pedido de suporte** no menu de suporte.
3. Na página de suporte, siga estes passos:
    1. Para **tipo de problema**, selecione **técnica** na lista pendente.
    2. Em **Subscrição**, selecione a sua subscrição.
    3. Para **serviço**, selecione **meus serviços**e, em seguida, selecione **Hubs de eventos**.
    4. Para **Resource**, selecione o cluster, se já existir, caso contrário, selecione **geral pergunta/recurso não está disponível**.
    5. Para **tipo de problema**, selecione **Quota**.
    6. Para **subtipo de problema**, selecione um dos seguintes valores na lista pendente:
        1. Selecione **pedido para o SKU dedicado** para pedir para a funcionalidade de suporte na sua região.
        2. Selecione **pedido para aumentar verticalmente ou dimensionar para baixo Cluster dedicado** se pretender aumentar ou reduzir verticalmente o cluster dedicado. 
    7. Para **assunto**, descreva o problema.

        ![Página de pedido de suporte](./media/event-hubs-dedicated-cluster-create-portal/support-ticket.png)

 ## <a name="delete-a-dedicated-cluster"></a>Eliminar um cluster dedicado
1. Para eliminar o cluster, selecione **eliminar** no menu superior.
2. Será apresentada uma mensagem a confirmar o deseja eliminar o cluster.
3. Tipo de **nome do cluster** e selecione **eliminar** para eliminar o cluster.

    ![Eliminar página do cluster](./media/event-hubs-dedicated-cluster-create-portal/delete-cluster-page.png)


## <a name="next-steps"></a>Passos Seguintes
Neste artigo, criou um cluster de Hubs de eventos. Para obter instruções passo a passo enviar e receber eventos de um hub de eventos e capturar eventos para um armazenamento do Azure ou do Azure Data Lake Store, veja os tutoriais seguintes:

- [Enviar e receber eventos no .NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Utilizar o portal do Azure para ativar a captura de Hubs de eventos](event-hubs-capture-enable-through-portal.md)
- [Utilizar os Hubs de eventos do Azure para o Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
