---
title: Criar, visualizar e gerir tópicos do sistema na Grelha de Eventos Azure (portal)
description: Este artigo mostra como visualizar o tópico do sistema existente, criar tópicos do sistema Azure Event Grid utilizando o portal Azure.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 2dbb0893668d72cfebf2d64e6515ff6ab6fed9bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86115116"
---
# <a name="create-view-and-manage-event-grid-system-topics-in-the-azure-portal"></a>Criar, ver e gerir tópicos do sistema de Grade de Eventos no portal Azure
Este artigo mostra-lhe como criar e gerir tópicos do sistema usando o portal Azure. Para uma visão geral dos tópicos do sistema, consulte [os tópicos do Sistema](system-topics.md).

## <a name="create-a-system-topic"></a>Criar um tópico de sistema
Pode criar um tópico de sistema para um recurso Azure (conta de armazenamento, espaço de nomes de Event Hubs, etc.) de duas formas:

- Utilizando a página **Eventos** de um recurso, por exemplo, Conta de Armazenamento ou Espaço de Nomes de Centros de Eventos. Quando utiliza a página **Eventos** no portal Azure para criar uma subscrição de eventos para um evento levantado por uma fonte Azure (por exemplo: conta de Armazenamento Azure), o portal cria um tópico de sistema para o recurso Azure e, em seguida, cria uma subscrição para o tópico do sistema. Especifica o nome do tópico do sistema se estiver a criar uma subscrição de eventos no recurso Azure pela primeira vez. A partir da segunda vez, o nome tópico do sistema é apresentado para si no modo apenas de leitura. Consulte [Quickstart: Route Blob storage events to web endpoint with the Azure portal](blob-event-quickstart-portal.md#subscribe-to-the-blob-storage) for detailed steps.
- Utilizando a página **tópicos do sistema de grelha de evento.** Os seguintes passos são para criar um tópico de sistema utilizando a página **Tópicos do Sistema de Grelha de Eventos.** 

1. Inscreva-se no [portal Azure](https://portal.azure.com).
2. Na caixa de pesquisa no topo, escreva **Tópicos do Sistema de Grelha de Eventos**e, em seguida, prima **ENTER**. 

    ![Pesquisa de tópicos do sistema](./media/create-view-manage-system-topics/search-system-topics.png)
3. Na página **Tópicos do Sistema de Grelha de Eventos,** selecione **+ Adicione** na barra de ferramentas.

    ![Adicionar tópico do sistema - botão de barra de ferramentas](./media/create-view-manage-system-topics/add-system-topic-menu.png)
4. Na página **Tópico do Sistema de Grelha de Eventos,** faça os seguintes passos:
    1. Selecione o **tipo de tópico**. No exemplo seguinte, é selecionada a opção **Contas de Armazenamento.** 
    2. Selecione a **subscrição Azure** que tem o seu recurso de conta de armazenamento. 
    3. Selecione o **grupo de recursos** que tem a conta de armazenamento. 
    4. Selecione a **conta de armazenamento**. 
    5. Insira um **nome** para o tópico do sistema a ser criado. 
    
        > [!NOTE]
        > Pode utilizar este nome tópico do sistema para pesquisar métricas e registos de diagnóstico.
    6. Selecione **Rever + criar**.

        ![Criar tópico de sistema](./media/create-view-manage-system-topics/create-event-grid-system-topic-page.png)
    5. Rever as definições e selecionar **Criar**. 
        
        ![Rever e criar tópico de sistema](./media/create-view-manage-system-topics/system-topic-review-create.png)
    6. Depois de a implementação ter sucesso, selecione **Ir para** o recurso para ver a página tópico do sistema de grelha **de evento** para o tópico do sistema que criou. 

        ![Página de tópico do sistema](./media/create-view-manage-system-topics/system-topic-page.png)


## <a name="view-all-system-topics"></a>Ver todos os tópicos do sistema
Siga estes passos para ver todos os tópicos do sistema de Grelha de Eventos existentes. 

1. Inscreva-se no [portal Azure](https://portal.azure.com).
2. Na caixa de pesquisa no topo, escreva **Tópicos do Sistema de Grelha de Eventos**e, em seguida, prima **ENTER**. 

    ![Pesquisa de tópicos do sistema](./media/create-view-manage-system-topics/search-system-topics.png)
3. Na página **tópicos do sistema de grelha de eventos,** você vê todos os tópicos do sistema. 

    ![Lista de tópicos do sistema](./media/create-view-manage-system-topics/list-system-topics.png)
4. Selecione um tópico do **sistema** na lista para ver detalhes sobre o mesmo. 

    ![Detalhes do tópico do sistema](./media/create-view-manage-system-topics/system-topic-details.png)

    Esta página mostra-lhe detalhes sobre o tópico do sistema, como as seguintes informações: 
    - A fonte. Nome do recurso sobre o qual o tópico do sistema foi criado.
    - Tipo de origem. Tipo de recurso. Por exemplo: `Microsoft.Storage.StorageAccounts` , e assim por `Microsoft.EventHub.Namespaces` `Microsoft.Resources.ResourceGroups` diante.
    - Quaisquer subscrições criadas para o tópico do sistema.

    Esta página permite operações como as seguintes:
    - Crie uma subscrição de evento Selecione **+Subscrição de evento** na barra de ferramentas. 
    - Excluir uma subscrição de evento. **Selecione Excluir** na barra de ferramentas. 
    - Adicione etiquetas para o tópico do sistema. Selecione **Tags** no menu esquerdo e especifique nomes e valores de marcação. 


## <a name="delete-a-system-topic"></a>Eliminar um tópico de sistema
1. Siga as instruções da secção [de tópicos](#view-all-system-topics) do sistema Ver para ver todos os tópicos do sistema e selecione o tópico do sistema que pretende eliminar da lista. 
2. Na página **tópico do sistema de grelha de evento,** selecione **Eliminar** na barra de ferramentas. 

    ![Tópico do sistema - eliminar botão](./media/create-view-manage-system-topics/system-topic-delete-button.png)
3. Na página de confirmação, selecione **OK** para confirmar a eliminação. Elimina o tópico do sistema e também todas as subscrições de eventos para o tópico do sistema.  

## <a name="create-an-event-subscription"></a>Criar uma subscrição de evento
1. Siga as instruções da secção [de tópicos](#view-all-system-topics) do sistema Ver para ver todos os tópicos do sistema e selecione o tópico do sistema que pretende eliminar da lista. 
2. Na página **tópico do sistema de grelha de evento,** selecione + **Subscrição** de evento a partir da barra de ferramentas. 

    ![Tópico do sistema - adicione botão de subscrição de evento](./media/create-view-manage-system-topics/add-event-subscription-button.png)
3. Confirme se o **Tipo tópico,** **o recurso de origem**e **o nome tópico** são automaticamente povoados. Introduza um nome, selecione um **Tipo de Ponto final**e especifique o ponto **final**. Em seguida, **selecione Criar** para criar a subscrição do evento. 

    ![Tópico do sistema - crie subscrição de eventos](./media/create-view-manage-system-topics/create-event-subscription.png)

## <a name="next-steps"></a>Passos seguintes
Consulte os tópicos do Sistema na secção [Azure Event Grid](system-topics.md) para saber mais sobre tópicos do sistema e tipos de tópicos suportados pela Azure Event Grid. 
