---
title: Azure Quickstart - Criar um hub de eventos utilizando o portal Azure
description: Neste início rápido, irá aprender a criar um hub de eventos do Azure com o portal do Azure e, em seguida, enviar e receber eventos com o SDK .NET Standard.
services: event-hubs
documentationcenter: ''
author: spelluru
ms.service: event-hubs
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: 5e80ab6d5ed0076e03f5378cbe975b15d0a28f47
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78355839"
---
# <a name="quickstart-create-an-event-hub-using-azure-portal"></a>Início Rápido: Criar um hub de eventos com o portal do Azure
Os Hubs de Eventos do Azure são uma plataforma de fluxo de Macrodados e um serviço de ingestão de eventos capaz de receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos por dispositivos e software distribuído. Os dados enviados para um hub de eventos podem ser transformados e armazenados em qualquer fornecedor de análise em tempo real ou adaptadores de armazenamento/criação de batches. Para uma descrição geral detalhada dos Hubs de Eventos, veja [Descrição geral dos Hubs de Eventos](event-hubs-about.md) e [Funcionalidades dos Hubs de Eventos](event-hubs-features.md).

Neste início rápido, vai criar um hub de eventos com o [portal do Azure](https://portal.azure.com).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem:

- Subscrição do Azure. Se não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- [Estúdio Visual 2019)](https://www.visualstudio.com/vs) ou mais tarde.
- [SDK .NET Standard](https://www.microsoft.com/net/download/windows), versão 2.0 ou posterior.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos:

Um grupo de recursos é uma coleção lógica de recursos do Azure. Todos os recursos são implementados e geridos num grupo de recursos. Para criar um grupo de recursos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Na navegação à esquerda, clique em **Grupos de recursos**. Em seguida, clique em **Adicionar**.

   ![Grupos de recursos - Botão Adicionar](./media/event-hubs-quickstart-portal/resource-groups1.png)

2. Para **Subscrição,** selecione o nome da subscrição Azure na qual pretende criar o grupo de recursos.
3. Digite um nome único **para o grupo de recursos**. O sistema verifica imediatamente a disponibilidade do nome na subscrição do Azure atualmente selecionada.
4. Selecione uma **região** para o grupo de recursos.
5. Selecione **Rever + Criar**.

   ![Grupo de recursos - criar](./media/event-hubs-quickstart-portal/resource-groups2.png)
6. Na página **Review + Criar,** selecione **Criar**. 

## <a name="create-an-event-hubs-namespace"></a>Criar um espaço de nomes dos Hubs de Eventos

Um espaço de nomes dos Hubs de Eventos fornece um contentor de âmbito exclusivo, referenciado pelo respetivo nome de domínio completamente qualificado, no qual cria um ou mais hubs de eventos. Para criar um espaço de nomes no seu grupo de recursos com o portal, faça as ações seguintes:

1. No portal do Azure, clique em **Criar um recurso** na parte superior esquerda do ecrã.
2. Selecione **todos os serviços** no menu esquerdo e selecione **star (`*`)** ao lado dos Centros de **Eventos** na categoria **Analytics.** Confirme que **o Event Hubs** é adicionado aos **FAVORITES** no menu de navegação esquerdo. 
    
   ![Pesquisa de Centros de Eventos](./media/event-hubs-quickstart-portal/select-event-hubs-menu.png)
3. Selecione Centros de **Eventos** em **FAVORITOS** no menu de navegação esquerdo e selecione **Adicionar** na barra de ferramentas.

   ![Botão Adicionar](./media/event-hubs-quickstart-portal/event-hubs-add-toolbar.png)
4. Na página Criar espaço de **nome,** tome os seguintes passos:
    1. Insira um **nome** para o espaço de nome. O sistema verifica imediatamente a disponibilidade do nome.
    2. Escolha o **nível de preços** (Básico ou Standard).
    3. Note que a opção **Enable Kafka** está ativada automaticamente. Os Hubs de eventos do Azure fornece um ponto de extremidade do Kafka. Este ponto final permite que o seu espaço de nome Sem Nome Do Evento Para entender de forma nativa o protocolo de mensagens [Apache Kafka](https://kafka.apache.org/intro) e APIs. Com esta capacidade, pode comunicar com os hubs de eventos, tal como faria com tópicos do Kafka sem alterar os seus clientes de protocolo ou os seus próprios clusters em execução. O Event Hubs suporta as [versões Apache Kafka 1.0](https://kafka.apache.org/10/documentation.html) e mais tarde.
    4. Selecione a **subscrição** na qual pretende criar o espaço de nome.
    5. Selecione um grupo de **recursos** existente ou crie um novo grupo de recursos. 
    4. Selecione um **local** para o espaço de nome.
    5. Selecione **Criar**. Poderá ter de aguardar alguns minutos para que o sistema aprovisione totalmente os recursos.

       ![Criar um espaço de nomes do hub de eventos](./media/event-hubs-quickstart-portal/create-event-hub1.png)
5. Refresque a página **Do Event Hubs** para ver o espaço de nome do centro do evento. Pode verificar o estado da criação do hub do evento nos alertas. 

    ![Criar um espaço de nomes do hub de eventos](./media/event-hubs-quickstart-portal/event-hubs-refresh.png)
6. Selecione o espaço de nome. Você vê a página inicial para o seu espaço de **nome Sem Nome Do Evento Hubs** no portal. 

   ![Página inicial para o espaço de nome](./media/event-hubs-quickstart-portal/namespace-home-page.png)
    
## <a name="create-an-event-hub"></a>Criar um hub de eventos

Para criar um hub de eventos no espaço de nomes, faça as ações seguintes:

1. Na página Namespace do Event Hubs, selecione **Event Hubs** no menu esquerdo.
1. Na parte superior da janela, clique em **+ Hub de Eventos**.
   
    ![Adicionar Hub de Eventos - botão](./media/event-hubs-quickstart-portal/create-event-hub4.png)
1. Escreva um nome para o seu hub de eventos e, em seguida, clique em **Criar**.
   
    ![Criar hub de eventos](./media/event-hubs-quickstart-portal/create-event-hub5.png)
4. Pode verificar o estado da criação do hub do evento em alertas. Após a criação do hub do evento, vê-lo na lista de centros de eventos como mostrado na seguinte imagem:

    ![Hub de eventos criado](./media/event-hubs-quickstart-portal/event-hub-created.png)

Parabéns! Utilizou o portal para criar um espaço de nomes dos Hubs de Eventos e um hub de eventos nesse espaço de nomes. 

## <a name="next-steps"></a>Passos seguintes

Neste artigo, criou um grupo de recursos, um espaço de nomes de Hubs de Eventos e um hub de eventos. Para instruções passo a passo para enviar eventos para (ou) receber eventos de um centro de eventos, consulte o **Envio e receba** tutoriais de eventos: 

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](get-started-java-send-v2.md)
- [python](get-started-python-send-v2.md)
- [JavaScript](get-started-java-send-v2.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (apenas enviar)](event-hubs-c-getstarted-send.md)
- [Tempestade Apache (receber apenas)](event-hubs-storm-getstarted-receive.md)


[Azure portal]: https://portal.azure.com/
[3]: ./media/event-hubs-quickstart-portal/sender1.png
[4]: ./media/event-hubs-quickstart-portal/receiver1.png
