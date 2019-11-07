---
title: Início rápido do Azure-criar um hub de eventos usando o portal do Azure
description: Neste início rápido, irá aprender a criar um hub de eventos do Azure com o portal do Azure e, em seguida, enviar e receber eventos com o SDK .NET Standard.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/05/2019
ms.author: shvija
ms.openlocfilehash: 209bdb919a505e55bdfb8868d556fa1ad08db2fe
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73717941"
---
# <a name="quickstart-create-an-event-hub-using-azure-portal"></a>Início Rápido: Criar um hub de eventos com o portal do Azure
Os Hubs de Eventos do Azure são uma plataforma de fluxo de Macrodados e um serviço de ingestão de eventos capaz de receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos por dispositivos e software distribuído. Os dados enviados para um hub de eventos podem ser transformados e armazenados em qualquer fornecedor de análise em tempo real ou adaptadores de armazenamento/criação de batches. Para uma descrição geral detalhada dos Hubs de Eventos, veja [Descrição geral dos Hubs de Eventos](event-hubs-about.md) e [Funcionalidades dos Hubs de Eventos](event-hubs-features.md).

Neste início rápido, vai criar um hub de eventos com o [portal do Azure](https://portal.azure.com).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem:

- Subscrição do Azure. Se não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- [Visual Studio 2019)](https://www.visualstudio.com/vs) ou posterior.
- [SDK .NET Standard](https://www.microsoft.com/net/download/windows), versão 2.0 ou posterior.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é uma coleção lógica de recursos do Azure. Todos os recursos são implementados e geridos num grupo de recursos. Para criar um grupo de recursos:

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).
2. Na navegação à esquerda, clique em **Grupos de recursos**. Em seguida, clique em **Adicionar**.

   ![Grupos de recursos - Botão Adicionar](./media/event-hubs-quickstart-portal/resource-groups1.png)

2. Para **assinatura**, selecione o nome da assinatura do Azure no qual você deseja criar o grupo de recursos.
3. Digite um **nome exclusivo para o grupo de recursos**. O sistema verifica imediatamente a disponibilidade do nome na subscrição do Azure atualmente selecionada.
4. Selecione uma **região** para o grupo de recursos.
5. Selecione **revisão + criar**.

   ![Grupo de recursos - criar](./media/event-hubs-quickstart-portal/resource-groups2.png)
6. Na página **revisar + criar** , selecione **criar**. 

## <a name="create-an-event-hubs-namespace"></a>Criar um espaço de nomes dos Hubs de Eventos

Um espaço de nomes dos Hubs de Eventos fornece um contentor de âmbito exclusivo, referenciado pelo respetivo nome de domínio completamente qualificado, no qual cria um ou mais hubs de eventos. Para criar um espaço de nomes no seu grupo de recursos com o portal, faça as ações seguintes:

1. No portal do Azure, clique em **Criar um recurso** na parte superior esquerda do ecrã.
2. Selecione **todos os serviços** no menu à esquerda e selecione **estrela (`*`)** ao lado dos **hubs de eventos** na categoria **análise** . Confirme se os **hubs de eventos** são adicionados aos **favoritos** no menu de navegação esquerdo. 
    
   ![Pesquisar hubs de eventos](./media/event-hubs-quickstart-portal/select-event-hubs-menu.png)
3. Selecione **hubs de eventos** em **favoritos** no menu esquerdo de navegação e selecione **Adicionar** na barra de ferramentas.

   ![Botão Adicionar](./media/event-hubs-quickstart-portal/event-hubs-add-toolbar.png)
4. Na página **criar namespace** , execute as seguintes etapas:
    1. Insira um nome para o namespace. O sistema verifica imediatamente a disponibilidade do nome.
    2. Escolha o tipo de preço (básico ou padrão).
    3. Selecione a **assinatura** na qual você deseja criar o namespace.
    4. Selecione um **local** para o namespace.
    5. Selecione **Criar**. Poderá ter de aguardar alguns minutos para que o sistema aprovisione totalmente os recursos.

       ![Criar um espaço de nomes do hub de eventos](./media/event-hubs-quickstart-portal/create-event-hub1.png)
5. Atualize a página **hubs de eventos** para ver o namespace do hub de eventos. Você pode verificar o status da criação do hub de eventos nos alertas. 

    ![Criar um espaço de nomes do hub de eventos](./media/event-hubs-quickstart-portal/event-hubs-refresh.png)
6. Selecione o namespace. Você verá o home page para seu **namespace de hubs de eventos** no Portal. 

   ![Home Page do namespace](./media/event-hubs-quickstart-portal/namespace-home-page.png)
    
## <a name="create-an-event-hub"></a>Criar um hub de eventos

Para criar um hub de eventos no espaço de nomes, faça as ações seguintes:

1. Na página namespace de hubs de eventos, selecione **hubs de eventos** no menu à esquerda.
1. Na parte superior da janela, clique em **+ Hub de Eventos**.
   
    ![Adicionar Hub de Eventos - botão](./media/event-hubs-quickstart-portal/create-event-hub4.png)
1. Escreva um nome para o seu hub de eventos e, em seguida, clique em **Criar**.
   
    ![Criar hub de eventos](./media/event-hubs-quickstart-portal/create-event-hub5.png)
4. Você pode verificar o status da criação do hub de eventos em alertas. Depois que o Hub de eventos for criado, você o verá na lista de hubs de eventos, conforme mostrado na imagem a seguir:

    ![Hub de eventos criado](./media/event-hubs-quickstart-portal/event-hub-created.png)

Parabéns! Utilizou o portal para criar um espaço de nomes dos Hubs de Eventos e um hub de eventos nesse espaço de nomes. 

## <a name="next-steps"></a>Passos seguintes

Neste artigo, criou um grupo de recursos, um espaço de nomes de Hubs de Eventos e um hub de eventos. Para obter instruções detalhadas sobre como enviar eventos para (ou) receber eventos de um hub de eventos, consulte os tutoriais de **eventos de envio e recebimento** : 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [.NET Framework](event-hubs-dotnet-framework-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [Node.js](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (somente enviar)](event-hubs-c-getstarted-send.md)
- [Apache Storm (somente reecive)](event-hubs-storm-getstarted-receive.md)


[Azure portal]: https://portal.azure.com/
[3]: ./media/event-hubs-quickstart-portal/sender1.png
[4]: ./media/event-hubs-quickstart-portal/receiver1.png
