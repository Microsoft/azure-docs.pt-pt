---
title: Criar um cluster dedicado ao Event Hubs utilizando o portal Azure
description: Neste arranque rápido, você aprende a criar um cluster Azure Event Hubs usando o portal Azure.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 596b07210d35f648af78e45b7472c620a4361e8a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104601028"
---
# <a name="quickstart-create-a-dedicated-event-hubs-cluster-using-azure-portal"></a>Quickstart: Criar um cluster dedicado de Centros de Eventos usando o portal Azure 
Os clusters de eventos Hubs oferecem implantações de inquilinos únicos para clientes com as necessidades de streaming mais exigentes. Esta oferta tem um SLA garantido de 99,99% e está disponível apenas no nosso nível de preços dedicado. Um [cluster Event Hubs](event-hubs-dedicated-overview.md) pode entrar em milhões de eventos por segundo com capacidade garantida e latência do subsegundo. Os espaços de nome e os centros de eventos criados dentro de um cluster incluem todas as características da oferta padrão e muito mais, mas sem limites de entrada. A oferta dedicada também inclui a popular funcionalidade [de captura de Centros de Eventos](event-hubs-capture-overview.md) sem custos adicionais, permitindo-lhe abastruto e registar fluxos de dados para [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) ou [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md).

Os clusters dedicados são a provisionados e faturados pelas **Unidades de Capacidade (CUs),** uma quantidade pré-atribuída de CPU e recursos de memória. Você pode comprar 1, 2, 4, 8, 12, 16 ou 20 CUs para cada cluster. Neste quickstart, vamos acompanhá-lo através da criação de um cluster de 1 CU Event Hubs através do portal Azure.

> [!NOTE]
> Esta experiência de autosserviço está atualmente disponível em pré-visualização no [Portal Azure](https://aka.ms/eventhubsclusterquickstart). Se tiver alguma dúvida sobre a oferta dedicada, contacte a equipa do [Event Hubs.](mailto:askeventhubs@microsoft.com)


## <a name="prerequisites"></a>Pré-requisitos
Para concluir este início rápido, certifique-se de que tem:

- Uma conta do Azure. Se não tiver uma, [compre uma conta](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) antes de começar. Esta funcionalidade não é suportada com uma conta Azure gratuita. 
- [Visual Studio](https://visualstudio.microsoft.com/vs/) 2017 Update 3 (versão 15.3, 26730.01) ou posterior.
- [SDK .NET Standard](https://dotnet.microsoft.com/download), versão 2.0 ou posterior.
- [Criei um grupo de recursos.](../event-hubs/event-hubs-create.md#create-a-resource-group)

## <a name="create-an-event-hubs-dedicated-cluster"></a>Criar um cluster dedicado a centros de eventos
Um cluster Event Hubs fornece um recipiente de scoping único no qual você pode criar um ou mais espaços de nome. Nesta fase de pré-visualização da experiência de autosserviço do portal, pode criar 1 aglomerados cu em regiões selecionadas. Se precisar de um cluster maior do que 1 CU, pode submeter um pedido de apoio ao Azure para escalar o seu cluster após a sua criação.

> [!IMPORTANT]
> Não poderá apagar o cluster durante pelo menos 4 horas após a sua criação. Portanto, você será cobrado por um mínimo de 4 horas de utilização do cluster. Para obter mais informações sobre preços, consulte [Os Centros de Eventos - Preços](https://azure.microsoft.com/pricing/details/event-hubs/). 

Para criar um cluster no seu grupo de recursos utilizando o portal Azure, complete os seguintes passos:

1. Siga [este link](https://aka.ms/eventhubsclusterquickstart) para criar um cluster no portal Azure. Em sentido inverso, selecione **Todos os serviços** do painel de navegação à esquerda e, em seguida, digite em "Clusters de Centros de Eventos" na barra de pesquisa e selecione "Clusters de Centros de Eventos" da lista de resultados.
2. Na página **'Criar Cluster',** configurar as seguintes definições:
    1. Insira um **nome para o cluster**. O sistema verifica imediatamente a disponibilidade do nome.
    2. Selecione a **subscrição** na qual pretende criar o cluster.
    3. Selecione o **grupo de recursos** no qual pretende criar o cluster.
    4. Selecione um **local** para o cluster. Se a sua região preferida estiver acinzentada, está temporariamente fora de capacidade e pode apresentar um pedido de [apoio](#submit-a-support-request) à equipa do Event Hubs.
    5. Selecione o **seguinte: Botão tags** na parte inferior da página. Poderá ter de aguardar alguns minutos para que o sistema aprovisione totalmente os recursos.

        ![Criar Cluster de Centros de Eventos - Página básica](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-basics-page.png)
3. Na página **Tags,** configuure o seguinte:
    1. Insira um **nome** e um **valor** para a etiqueta que pretende adicionar. Este passo é **opcional.**  
    2. Selecione o botão **'Rever + Criar'.**

        ![Criar página de cluster de centros de eventos - página de tags](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-tags-page.png)
4. Na página **'Analisar + Criar',** rever os detalhes e selecionar **Criar.** 

    ![Criar página de cluster de centros de eventos - Rever + criar página](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-review-create-page.png)

## <a name="create-a-namespace-and-event-hub-within-a-cluster"></a>Crie um espaço de nome e centro de eventos dentro de um cluster

1. Para criar um espaço de nome dentro de um cluster, na página cluster **de Centros de Eventos** para o seu cluster, selecione **+Namespace** a partir do menu superior.

    ![Página de gestão de cluster - adicione botão de espaço de nome](./media/event-hubs-dedicated-cluster-create-portal/cluster-management-page-add-namespace-button.png)
2. Na página **Criar um espaço de nome,** faça os seguintes passos:
    1. Insira um **nome para o espaço de nomes**.  O sistema verifica se o nome está disponível.
    2. O espaço de nome herda as seguintes propriedades:
        1. ID da subscrição
        2. Grupo de Recursos
        3. Localização
        4. Nome do Cluster
    3. Selecione **Criar** para criar o espaço de nomes. Agora pode gerir o seu agrupamento.  

        ![Criar espaço de nome na página de cluster](./media/event-hubs-dedicated-cluster-create-portal/create-namespace-cluster-page.png)
3. Uma vez criado o seu espaço de nome, pode [criar um centro de eventos,](event-hubs-create.md#create-an-event-hub) uma vez que normalmente criaria um dentro de um espaço de nome. 


## <a name="submit-a-support-request"></a>Submeter um pedido de apoio

Se desejar alterar o tamanho do seu cluster após a criação ou se a sua região preferida não estiver disponível, envie um pedido de apoio seguindo estes passos:

1. No [portal Azure,](https://portal.azure.com)selecione **Help + support** from the left menu.
2. Selecione **+ Novo pedido** de suporte do menu De apoio.
3. Na página de suporte, siga estes passos:
    1. Para **o Tipo de Emissão**, selecione **Técnico** da lista de espera.
    2. Em **Subscrição**, selecione a sua subscrição.
    3. Para **o Serviço**, selecione os meus **serviços** e, em seguida, selecione Centros de **Eventos**.
    4. Para **recursos**, selecione o seu cluster se já existir, caso contrário selecione **Pergunta Geral/Recurso Não Disponível**.
    5. Para **o tipo de problema**, selecione **Quota**.
    6. Para **o subtipo de problemas,** selecione um dos seguintes valores da lista de down-down:
        1. Selecione **Pedido de SKU dedicado** para solicitar que a funcionalidade seja suportada na sua região.
        2. Selecione **Request to Scale Up ou Scale Down Dedicated Cluster** se quiser escalar ou escalar o seu cluster dedicado. 
    7. Para **o Sujeito,** descreva a questão.

        ![Página de bilhete de apoio](./media/event-hubs-dedicated-cluster-create-portal/support-ticket.png)

 ## <a name="delete-a-dedicated-cluster"></a>Excluir um cluster dedicado
 
1. Para eliminar o cluster, **selecione Eliminar** do menu superior. 

    > [!IMPORTANT]
    > Não poderá apagar o cluster durante pelo menos 4 horas após a sua criação. Portanto, você será cobrado por um mínimo de 4 horas de utilização do cluster. Para obter mais informações sobre preços, consulte [Os Centros de Eventos - Preços](https://azure.microsoft.com/pricing/details/event-hubs/).     
1. Aparecerá uma mensagem confirmando o seu desejo de eliminar o cluster.
1. Digite o **nome do cluster** e selecione **Eliminar** para eliminar o cluster.

    ![Eliminar página de cluster](./media/event-hubs-dedicated-cluster-create-portal/delete-cluster-page.png)


## <a name="next-steps"></a>Passos seguintes
Neste artigo, criou um cluster De Clusters de Eventos. Para instruções passo a passo para enviar e receber eventos de um centro de eventos, e capturar eventos para um armazenamento Azure ou Azure Data Lake Store, consulte os seguintes tutoriais:

- Enviar e receber eventos 
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-node-get-started-send.md)
- [Use o portal Azure para permitir a captura de centros de eventos](event-hubs-capture-enable-through-portal.md)
- [Use hubs de eventos Azure para Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
