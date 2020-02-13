---
title: Criar um cluster dedicado ao Event Hubs utilizando o portal Azure
description: Neste arranque rápido, aprende-se a criar um cluster azure Event Hubs utilizando o portal Azure.
services: event-hubs
documentationcenter: ''
author: femila
manager: ''
ms.service: event-hubs
ms.topic: quickstart
ms.custom: mvc
ms.date: 12/20/2019
ms.author: femila
ms.openlocfilehash: 5b1574eaac8771043e09500225b65e4835c8e627
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77157487"
---
# <a name="quickstart-create-a-dedicated-event-hubs-cluster-using-azure-portal"></a>Quickstart: Criar um cluster dedicado de Hubs de Eventos usando o portal Azure 
Os clusters do Event Hubs oferecem implementações de inquilinos únicos para clientes com as necessidades de streaming mais exigentes. Esta oferta tem um SLA garantido de 99,99% e está disponível apenas no nosso nível de preços dedicado. Um [cluster de Hubs](event-hubs-dedicated-overview.md) de Eventos pode ingressar milhões de eventos por segundo com capacidade garantida e latência subssegundo. Espaços de nome e centros de eventos criados dentro de um cluster incluem todas as características da oferta padrão e muito mais, mas sem limites de ingresso. A oferta dedicada também inclui a popular funcionalidade de captura de Hubs de Eventos sem [custos](event-hubs-capture-overview.md) adicionais, permitindo-lhe automaticamente emulá-lo e registar fluxos de dados para [o Armazenamento De Azure Blob](../storage/blobs/storage-blobs-introduction.md) ou para o Armazenamento do [Lago Azure](../data-lake-store/data-lake-store-overview.md)Data Gen 1 .

Os clusters dedicados são aprovisionados e faturados pelas Unidades de **Capacidade (CUs),** uma quantidade pré-atribuída de CPU e recursos de memória. Pode comprar 1, 2, 4, 8, 12, 16 ou 20 CUs para cada cluster. Neste arranque rápido, vamos acompanhá-lo através da criação de um cluster de 1 CU Event Hubs através do portal Azure.

> [!NOTE]
> Esta experiência de self-serve está atualmente disponível em pré-visualização no [Portal Azure.](https://aka.ms/eventhubsclusterquickstart) Se tiver alguma dúvida sobre a oferta dedicada, contacte a equipa do [Event Hubs.](mailto:askeventhubs@microsoft.com)


## <a name="prerequisites"></a>Pré-requisitos
Para concluir este início rápido, certifique-se de que tem:

- Uma conta do Azure. Se não tiver uma, [compre uma conta](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) antes de começar. Esta funcionalidade não é suportada com uma conta Azure gratuita. 
- [Visual Studio](https://visualstudio.microsoft.com/vs/) 2017 Update 3 (versão 15.3, 26730.01) ou mais tarde.
- [SDK .NET Standard](https://dotnet.microsoft.com/download), versão 2.0 ou posterior.
- [Criou um grupo](../event-hubs/event-hubs-create.md#create-a-resource-group)de recursos.

## <a name="create-an-event-hubs-dedicated-cluster"></a>Criar um Cluster Dedicado a Centros de Eventos
Um cluster de Hubs de Eventos fornece um recipiente de digitalização único no qual você pode criar um ou mais espaços de nome. Nesta fase de pré-visualização da experiência de autosserviço do portal, pode criar 1 clusters CU em regiões selecionadas. Se precisar de um cluster maior do que 1 CU, pode submeter um pedido de apoio Azure para aumentar o seu cluster após a sua criação.

Para criar um cluster no seu grupo de recursos utilizando o portal Azure, por favor preencha os seguintes passos:

1. Siga [este link](https://aka.ms/eventhubsclusterquickstart) para criar um cluster no portal Azure. Inversamente, selecione **Todos os serviços** do painel de navegação esquerdo, em seguida, digite em "Clusters de Hubs de Eventos" na barra de pesquisa e selecione "Clusters de Hubs de Eventos" da lista de resultados.
2. Na página **Criar Cluster,** configure o seguinte:
    1. Introduza um **nome para o cluster**. O sistema verifica imediatamente a disponibilidade do nome.
    2. Selecione a **subscrição** na qual pretende criar o cluster.
    3. Selecione o **grupo de recursos** no qual pretende criar o cluster.
    4. Selecione um **local** para o cluster. Se a sua região preferida estiver cinzenta, está temporariamente fora de capacidade e pode submeter um pedido de [apoio](#submit-a-support-request) à equipa do Event Hubs.
    5. Selecione o botão **Seguinte: Etiquetas** na parte inferior da página. Poderá ter de aguardar alguns minutos para que o sistema aprovisione totalmente os recursos.

        ![Criar cluster de hubs de eventos - página básica](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-basics-page.png)
3. Na página **Tags,** configure o seguinte:
    1. Introduza um **nome** e um **valor** para a etiqueta que pretende adicionar. Este passo é **opcional**.  
    2. Selecione o botão **Rever + Criar.**

        ![Criar página cluster de Hubs de Eventos - Página de tags](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-tags-page.png)
4. Na página **Review + Criar,** reveja os detalhes e selecione **Criar**. 

    ![Criar página cluster de Hubs de Eventos - Rever + Criar página](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-review-create-page.png)

## <a name="create-a-namespace-and-event-hub-within-a-cluster"></a>Criar um espaço de nome e um centro de eventos dentro de um cluster

1. Para criar um espaço de nome dentro de um cluster, na página cluster do **Event Hubs** para o seu cluster, selecione **+Espaço de nome** no menu superior.

    ![Página de gestão de cluster - adicione botão namespace](./media/event-hubs-dedicated-cluster-create-portal/cluster-management-page-add-namespace-button.png)
2. Na página de criar um espaço de nome, faça os seguintes passos:
    1. Introduza um **nome para o espaço de nome**.  O sistema verifica se o nome está disponível.
    2. O espaço de nome herda as seguintes propriedades:
        1. ID da subscrição
        2. Grupo de Recursos
        3. Localização
        4. Nome do Cluster
    3. Selecione **Criar** para criar o espaço de nome. Agora pode gerir o seu aglomerado.  

        ![Criar espaço de nome na página do cluster](./media/event-hubs-dedicated-cluster-create-portal/create-namespace-cluster-page.png)
3. Uma vez criado o seu espaço de nome, pode [criar um centro](event-hubs-create.md#create-an-event-hub) de eventos, pois normalmente criaria um dentro de um espaço de nome. 


## <a name="submit-a-support-request"></a>Submeter pedidos de suporte

Se desejar alterar o tamanho do seu cluster após a criação ou se a sua região preferida não estiver disponível, por favor envie um pedido de apoio seguindo estas etapas:

1. No [portal Azure,](https://portal.azure.com)selecione **Ajuda + suporte** do menu esquerdo.
2. Selecione **+ Novo pedido** de suporte do menu Suporte.
3. Na página de suporte, siga estes passos:
    1. Para **O Tipo de Emissão,** selecione **Técnico** da lista de lançamentos.
    2. Em **Subscrição**, selecione a sua subscrição.
    3. Para **o Serviço,** selecione **Os meus serviços**e, em seguida, selecione Centros de **Eventos**.
    4. Para **Recursos,** selecione o seu cluster se já existe, caso contrário selecione **A Pergunta Geral/Recurso Não Disponível**.
    5. Para **o tipo de problema,** selecione **Quota**.
    6. Para **o subtipo Problema,** selecione um dos seguintes valores da lista de lançamentos:
        1. Selecione **Request for Dicated SKU** para solicitar que a funcionalidade seja suportada na sua região.
        2. Selecione **Request to Scale Up ou Scale Down Dicated Cluster** se quiser escalar ou reduzir o seu cluster dedicado. 
    7. Para **o Sujeito,** descreva a questão.

        ![Página de bilhete de apoio](./media/event-hubs-dedicated-cluster-create-portal/support-ticket.png)

 ## <a name="delete-a-dedicated-cluster"></a>Eliminar um cluster dedicado
 
1. Para eliminar o cluster, selecione **Eliminar** a partir do menu superior. Por favor, note que o seu cluster será cobrado por um mínimo de 4 horas de utilização após a criação. 
2. Aparecerá uma mensagem confirmando o seu desejo de eliminar o cluster.
3. Digite o **nome do cluster** e selecione **Eliminar** para eliminar o cluster.

    ![Eliminar página de cluster](./media/event-hubs-dedicated-cluster-create-portal/delete-cluster-page.png)


## <a name="next-steps"></a>Passos seguintes
Neste artigo, criou um cluster de Centros de Eventos. Para instruções passo a passo para enviar e receber eventos de um centro de eventos e capturar eventos para um armazenamento Azure ou Azure Data Lake Store, consulte os seguintes tutoriais:

- Enviar e receber eventos 
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
- [Utilize o portal Azure para permitir a captura de hubs de eventos](event-hubs-capture-enable-through-portal.md)
- [Use hubs de eventos Azure para Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
