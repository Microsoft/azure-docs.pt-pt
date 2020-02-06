---
title: Criar um cluster de hubs de eventos dedicado usando o portal do Azure
description: Neste guia de início rápido, você aprende a criar um cluster de hubs de eventos do Azure usando portal do Azure.
services: event-hubs
documentationcenter: ''
author: femila
manager: ''
ms.service: event-hubs
ms.topic: quickstart
ms.custom: mvc
ms.date: 12/20/2019
ms.author: femila
ms.openlocfilehash: 7003cc90bdb456eba9cfaebc1e24332d2e4a6251
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77029933"
---
# <a name="quickstart-create-a-dedicated-event-hubs-cluster-using-azure-portal"></a>Início rápido: criar um cluster de hubs de eventos dedicado usando portal do Azure 
Os clusters de hubs de eventos oferecem implantações de locatário único para clientes com as necessidades de streaming mais exigentes. Esta oferta tem um SLA garantido de 99,99% e está disponível apenas no nosso nível de preços dedicado. Um [cluster de Hubs](event-hubs-dedicated-overview.md) de Eventos pode ingressar milhões de eventos por segundo com capacidade garantida e latência subssegundo. Os namespaces e os hubs de eventos criados em um cluster incluem todos os recursos da oferta padrão e muito mais, mas sem nenhum limite de entrada. A oferta dedicada também inclui a popular funcionalidade de captura de Hubs de Eventos sem [custos](event-hubs-capture-overview.md) adicionais, permitindo-lhe automaticamente emulá-lo e registar fluxos de dados para [o Armazenamento De Azure Blob](../storage/blobs/storage-blobs-introduction.md) ou para o Armazenamento do [Lago Azure](../data-lake-store/data-lake-store-overview.md)Data Gen 1 .

Os clusters dedicados são aprovisionados e faturados pelas Unidades de **Capacidade (CUs),** uma quantidade pré-atribuída de CPU e recursos de memória. Você pode comprar 1, 2, 4, 8, 12, 16 ou 20 CUs para cada cluster. Neste guia de início rápido, vamos orientá-lo na criação de um cluster de hubs de eventos de 1 CU por meio do portal do Azure.

> [!NOTE]
> Esta experiência de self-serve está atualmente disponível em pré-visualização no [Portal Azure.](https://aka.ms/eventhubsclusterquickstart) Se tiver alguma dúvida sobre a oferta dedicada, contacte a equipa do [Event Hubs.](mailto:askeventhubs@microsoft.com)


## <a name="prerequisites"></a>Pré-requisitos
Para concluir este início rápido, certifique-se de que tem:

- Uma conta do Azure. Se não tiver uma, [compre uma conta](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) antes de começar. Não há suporte para esse recurso com uma conta gratuita do Azure. 
- [Visual Studio](https://visualstudio.microsoft.com/vs/) 2017 Update 3 (versão 15.3, 26730.01) ou mais tarde.
- [SDK .NET Standard](https://dotnet.microsoft.com/download), versão 2.0 ou posterior.
- [Criou um grupo](../event-hubs/event-hubs-create.md#create-a-resource-group)de recursos.

## <a name="create-an-event-hubs-dedicated-cluster"></a>Criar um cluster Hubs de Eventos Dedicados
Um cluster de hubs de eventos fornece um contêiner de escopo exclusivo no qual você pode criar um ou mais namespaces. Nesta fase de visualização da experiência de autoatendimento do portal, você pode criar 1 CU clusters em regiões selecionadas. Se você precisar de um cluster maior que 1 CU, poderá enviar uma solicitação de suporte do Azure para escalar verticalmente o cluster após sua criação.

Para criar um cluster em seu grupo de recursos usando o portal do Azure, execute as seguintes etapas:

1. Siga [este link](https://aka.ms/eventhubsclusterquickstart) para criar um cluster no portal Azure. Inversamente, selecione **Todos os serviços** do painel de navegação esquerdo, em seguida, digite em "Clusters de Hubs de Eventos" na barra de pesquisa e selecione "Clusters de Hubs de Eventos" da lista de resultados.
2. Na página **Criar Cluster,** configure o seguinte:
    1. Introduza um **nome para o cluster**. O sistema verifica imediatamente a disponibilidade do nome.
    2. Selecione a **subscrição** na qual pretende criar o cluster.
    3. Selecione o **grupo de recursos** no qual pretende criar o cluster.
    4. Selecione um **local** para o cluster. Se a sua região preferida estiver cinzenta, está temporariamente fora de capacidade e pode submeter um pedido de [apoio](#submit-a-support-request) à equipa do Event Hubs.
    5. Selecione o botão **Seguinte: Etiquetas** na parte inferior da página. Poderá ter de aguardar alguns minutos para que o sistema aprovisione totalmente os recursos.

        ![Criar cluster de hubs de eventos-página noções básicas](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-basics-page.png)
3. Na página **Tags,** configure o seguinte:
    1. Introduza um **nome** e um **valor** para a etiqueta que pretende adicionar. Este passo é **opcional**.  
    2. Selecione o botão **Rever + Criar.**

        ![Página criar um cluster de hubs de eventos – página marcas](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-tags-page.png)
4. Na página **Review + Criar,** reveja os detalhes e selecione **Criar**. 

    ![Página criar cluster de hubs de eventos – examinar + criar página](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-review-create-page.png)

## <a name="create-a-namespace-and-event-hub-within-a-cluster"></a>Criar um namespace e um hub de eventos em um cluster

1. Para criar um espaço de nome dentro de um cluster, na página cluster do **Event Hubs** para o seu cluster, selecione **+Espaço de nome** no menu superior.

    ![Página de gerenciamento de cluster – botão Adicionar namespace](./media/event-hubs-dedicated-cluster-create-portal/cluster-management-page-add-namespace-button.png)
2. Na página criar um namespace, execute as seguintes etapas:
    1. Introduza um **nome para o espaço de nome**.  O sistema verifica se o nome está disponível.
    2. O namespace herda as seguintes propriedades:
        1. ID da subscrição
        2. Grupo de Recursos
        3. Localização
        4. Nome do cluster
    3. Selecione **Criar** para criar o espaço de nome. Agora você pode gerenciar seu cluster.  

        ![Criar namespace na página do cluster](./media/event-hubs-dedicated-cluster-create-portal/create-namespace-cluster-page.png)
3. Uma vez criado o seu espaço de nome, pode [criar um centro](event-hubs-create.md#create-an-event-hub) de eventos, pois normalmente criaria um dentro de um espaço de nome. 


## <a name="submit-a-support-request"></a>Submeter pedidos de suporte

Se você quiser alterar o tamanho do cluster após a criação ou se sua região preferida não estiver disponível, envie uma solicitação de suporte seguindo estas etapas:

1. No [portal Azure,](https://portal.azure.com)selecione **Ajuda + suporte** do menu esquerdo.
2. Selecione **+ Novo pedido** de suporte do menu Suporte.
3. Na página de suporte, siga estas etapas:
    1. Para **O Tipo de Emissão,** selecione **Técnico** da lista de lançamentos.
    2. Em **Subscrição**, selecione a sua subscrição.
    3. Para **o Serviço,** selecione **Os meus serviços**e, em seguida, selecione Centros de **Eventos**.
    4. Para **Recursos,** selecione o seu cluster se já existe, caso contrário selecione **A Pergunta Geral/Recurso Não Disponível**.
    5. Para **o tipo de problema,** selecione **Quota**.
    6. Para **o subtipo Problema,** selecione um dos seguintes valores da lista de lançamentos:
        1. Selecione **Request for Dicated SKU** para solicitar que a funcionalidade seja suportada na sua região.
        2. Selecione **Request to Scale Up ou Scale Down Dicated Cluster** se quiser escalar ou reduzir o seu cluster dedicado. 
    7. Para **o Sujeito,** descreva a questão.

        ![Página tíquete de suporte](./media/event-hubs-dedicated-cluster-create-portal/support-ticket.png)

 ## <a name="delete-a-dedicated-cluster"></a>Excluir um cluster dedicado
 
1. Para eliminar o cluster, selecione **Eliminar** a partir do menu superior. Observe que o cluster será cobrado por um mínimo de 4 horas de uso após a criação. 
2. Uma mensagem será exibida confirmando seu desejo de excluir o cluster.
3. Digite o **nome do cluster** e selecione **Eliminar** para eliminar o cluster.

    ![Página excluir cluster](./media/event-hubs-dedicated-cluster-create-portal/delete-cluster-page.png)


## <a name="next-steps"></a>Passos seguintes
Neste artigo, você criou um cluster de hubs de eventos. Para obter instruções detalhadas sobre como enviar e receber eventos de um hub de eventos e capturar eventos para um armazenamento do Azure ou Azure Data Lake Store, consulte os seguintes tutoriais:

- [Enviar e receber eventos em .NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Utilize o portal Azure para permitir a captura de hubs de eventos](event-hubs-capture-enable-through-portal.md)
- [Use hubs de eventos Azure para Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
