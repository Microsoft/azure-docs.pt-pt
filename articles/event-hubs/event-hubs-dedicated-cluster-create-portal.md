---
title: Criar um cluster de hubs de eventos dedicado usando o portal do Azure
description: Neste guia de início rápido, você aprende a criar um cluster de hubs de eventos do Azure usando portal do Azure.
services: event-hubs
documentationcenter: ''
author: xurui203
manager: ''
ms.service: event-hubs
ms.topic: quickstart
ms.custom: mvc
ms.date: 12/20/2019
ms.author: xurui
ms.openlocfilehash: dbe34b95652d972a9e75f4ab8f2c208061359ae9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437216"
---
# <a name="quickstart-create-a-dedicated-event-hubs-cluster-using-azure-portal"></a>Início rápido: criar um cluster de hubs de eventos dedicado usando portal do Azure 
Os clusters de hubs de eventos oferecem implantações de locatário único para clientes com as necessidades de streaming mais exigentes. Esta oferta tem um SLA de 99,99% garantido e está disponível apenas em nosso tipo de preço dedicado. Um [cluster de hubs de eventos](event-hubs-dedicated-overview.md) pode ingressar milhões de eventos por segundo com a capacidade garantida e a latência de subsegundos. Os namespaces e os hubs de eventos criados em um cluster incluem todos os recursos da oferta padrão e muito mais, mas sem nenhum limite de entrada. A oferta dedicada também inclui o recurso popular de [captura de hubs de eventos](event-hubs-capture-overview.md) sem custo adicional, permitindo que você registre automaticamente os fluxos de dados de lote e de log no armazenamento de [BLOBs do Azure](../storage/blobs/storage-blobs-introduction.md) ou [Azure data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md).

Os clusters dedicados são provisionados e cobrados por **unidades de capacidade (cus)** , uma quantidade alocada de CPU e recursos de memória. Você pode comprar 1, 2, 4, 8, 12, 16 ou 20 CUs para cada cluster. Neste guia de início rápido, vamos orientá-lo na criação de um cluster de hubs de eventos de 1 CU por meio do portal do Azure.

> [!NOTE]
> Essa experiência de autoatendimento está disponível atualmente na visualização no [portal do Azure](https://aka.ms/eventhubsclusterquickstart). Se você tiver alguma dúvida sobre a oferta dedicada, entre em contato com a [equipe dos hubs de eventos](mailto:askeventhubs@microsoft.com).


## <a name="prerequisites"></a>Pré-requisitos
Para concluir este início rápido, certifique-se de que tem:

- Uma conta do Azure. Se você não tiver uma, [compre uma conta](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) antes de começar. Não há suporte para esse recurso com uma conta gratuita do Azure. 
- [Visual Studio](https://visualstudio.microsoft.com/vs/) 2017 atualização 3 (versão 15,3, 26730, 1) ou posterior.
- [SDK .NET Standard](https://dotnet.microsoft.com/download), versão 2.0 ou posterior.
- [Criou um grupo de recursos](../event-hubs/event-hubs-create.md#create-a-resource-group).

## <a name="create-an-event-hubs-dedicated-cluster"></a>Criar um cluster Hubs de Eventos Dedicados
Um cluster de hubs de eventos fornece um contêiner de escopo exclusivo no qual você pode criar um ou mais namespaces. Nesta fase de visualização da experiência de autoatendimento do portal, você pode criar 1 CU clusters em regiões selecionadas. Se você precisar de um cluster maior que 1 CU, poderá enviar uma solicitação de suporte do Azure para escalar verticalmente o cluster após sua criação.

Para criar um cluster em seu grupo de recursos usando o portal do Azure, execute as seguintes etapas:

1. Siga [este link](https://aka.ms/eventhubsclusterquickstart) para criar um cluster no portal do Azure. Por outro lado, selecione **todos os serviços** no painel de navegação esquerdo, digite "clusters de hubs de eventos" na barra de pesquisa e selecione "clusters de hubs de eventos" na lista de resultados.
2. Na página **criar cluster** , configure o seguinte:
    1. Insira um **nome para o cluster**. O sistema verifica imediatamente a disponibilidade do nome.
    2. Selecione a **assinatura** na qual você deseja criar o cluster.
    3. Selecione o **grupo de recursos** no qual você deseja criar o cluster.
    4. Selecione um **local** para o cluster. Se sua região preferida estiver esmaecida, ela estará temporariamente sem capacidade e você poderá enviar uma [solicitação de suporte](#submit-a-support-request) para a equipe de hubs de eventos.
    5. Selecione o botão **próximo: marcas** na parte inferior da página. Poderá ter de aguardar alguns minutos para que o sistema aprovisione totalmente os recursos.

        ![Criar cluster de hubs de eventos-página noções básicas](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-basics-page.png)
3. Na página **marcas** , configure o seguinte:
    1. Insira um **nome** e um **valor** para a marca que você deseja adicionar. Este passo é **opcional**.  
    2. Selecione o botão **revisar + criar** .

        ![Página criar um cluster de hubs de eventos – página marcas](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-tags-page.png)
4. Na página **revisar + criar** , examine os detalhes e selecione **criar**. 

    ![Página criar cluster de hubs de eventos – examinar + criar página](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-review-create-page.png)

## <a name="create-a-namespace-and-event-hub-within-a-cluster"></a>Criar um namespace e um hub de eventos em um cluster

1. Para criar um namespace em um cluster, na página do **cluster de hubs de eventos** para seu cluster, selecione **+ namespace** no menu superior.

    ![Página de gerenciamento de cluster – botão Adicionar namespace](./media/event-hubs-dedicated-cluster-create-portal/cluster-management-page-add-namespace-button.png)
2. Na página criar um namespace, execute as seguintes etapas:
    1. Insira um **nome para o namespace**.  O sistema verifica se o nome está disponível.
    2. O namespace herda as seguintes propriedades:
        1. ID de Subscrição
        2. Grupo de Recursos
        3. Localização
        4. Nome do cluster
    3. Selecione **criar** para criar o namespace. Agora você pode gerenciar seu cluster.  

        ![Criar namespace na página do cluster](./media/event-hubs-dedicated-cluster-create-portal/create-namespace-cluster-page.png)
3. Depois que o namespace for criado, você poderá [criar um hub de eventos](event-hubs-create.md#create-an-event-hub) como faria normalmente criar um em um namespace. 


## <a name="submit-a-support-request"></a>Submeter pedidos de suporte

Se você quiser alterar o tamanho do cluster após a criação ou se sua região preferida não estiver disponível, envie uma solicitação de suporte seguindo estas etapas:

1. Em [portal do Azure](https://portal.azure.com), selecione **ajuda + suporte** no menu à esquerda.
2. Selecione **+ nova solicitação de suporte** no menu de suporte.
3. Na página de suporte, siga estas etapas:
    1. Para **tipo de problema**, selecione **técnico** na lista suspensa.
    2. Em **Subscrição**, selecione a sua subscrição.
    3. Para **serviço**, selecione **meus serviços**e, em seguida, selecione **hubs de eventos**.
    4. Para **recurso**, selecione o cluster se ele já existir, caso contrário, selecione **pergunta/recurso geral não disponível**.
    5. Para **tipo de problema**, selecione **cota**.
    6. Para **subtipo de problema**, selecione um dos seguintes valores na lista suspensa:
        1. Selecione **solicitação de SKU dedicada** para solicitar que o recurso tenha suporte em sua região.
        2. Selecione **solicitação para escalar ou reduzir verticalmente o cluster dedicado** se você quiser escalar ou reduzir verticalmente seu cluster dedicado. 
    7. Para **assunto**, descreva o problema.

        ![Página tíquete de suporte](./media/event-hubs-dedicated-cluster-create-portal/support-ticket.png)

 ## <a name="delete-a-dedicated-cluster"></a>Excluir um cluster dedicado
 
1. Para excluir o cluster, selecione **excluir** no menu superior. Observe que o cluster será cobrado por um mínimo de 4 horas de uso após a criação. 
2. Uma mensagem será exibida confirmando seu desejo de excluir o cluster.
3. Digite o **nome do cluster** e selecione **excluir** para excluir o cluster.

    ![Página excluir cluster](./media/event-hubs-dedicated-cluster-create-portal/delete-cluster-page.png)


## <a name="next-steps"></a>Passos seguintes
Neste artigo, você criou um cluster de hubs de eventos. Para obter instruções detalhadas sobre como enviar e receber eventos de um hub de eventos e capturar eventos para um armazenamento do Azure ou Azure Data Lake Store, consulte os seguintes tutoriais:

- [Enviar e receber eventos no .NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Usar portal do Azure para habilitar a captura de hubs de eventos](event-hubs-capture-enable-through-portal.md)
- [Usar os hubs de eventos do Azure para Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
