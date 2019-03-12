---
title: Início Rápido do Azure - Criar um hub de eventos com o portal do Azure | Microsoft Docs
description: Neste início rápido, irá aprender a criar um hub de eventos do Azure com o portal do Azure e, em seguida, enviar e receber eventos com o SDK .NET Standard.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/23/2019
ms.author: shvija
ms.openlocfilehash: a7976c0798bd78fd51632d3025147526a645b041
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57528801"
---
# <a name="quickstart-create-an-event-hub-using-azure-portal"></a>Início rápido: Criar um hub de eventos com o portal do Azure
Os Hubs de Eventos do Azure são uma plataforma de fluxo de Macrodados e um serviço de ingestão de eventos capaz de receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos por dispositivos e software distribuído. Os dados enviados para um hub de eventos podem ser transformados e armazenados em qualquer fornecedor de análise em tempo real ou adaptadores de armazenamento/criação de batches. Para uma descrição geral detalhada dos Hubs de Eventos, veja [Descrição geral dos Hubs de Eventos](event-hubs-about.md) e [Funcionalidades dos Hubs de Eventos](event-hubs-features.md).

Neste início rápido, vai criar um hub de eventos com o [portal do Azure](https://portal.azure.com).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem:

- Subscrição do Azure. Se não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- [Visual Studio 2017 Atualização 3 (versão 15.3, 26730.01)](https://www.visualstudio.com/vs) ou posterior.
- [SDK .NET Standard](https://www.microsoft.com/net/download/windows), versão 2.0 ou posterior.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é uma coleção lógica de recursos do Azure. Todos os recursos são implementados e geridos num grupo de recursos. Para criar um grupo de recursos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Na navegação à esquerda, clique em **Grupos de recursos**. Em seguida, clique em **Adicionar**.

   ![Grupos de recursos - Botão Adicionar](./media/event-hubs-quickstart-portal/resource-groups1.png)

2. Para **subscrição**, selecione o nome da subscrição do Azure na qual pretende criar o grupo de recursos.
3. Escreva um exclusivo **nome do grupo de recursos**. O sistema verifica imediatamente a disponibilidade do nome na subscrição do Azure atualmente selecionada.
4. Selecione um **região** para o grupo de recursos.
5. Selecione **rever + criar**.

   ![Grupo de recursos - criar](./media/event-hubs-quickstart-portal/resource-groups2.png)
6. Sobre o **rever + criar** página, selecione **criar**. 

## <a name="create-an-event-hubs-namespace"></a>Criar um espaço de nomes dos Hubs de Eventos

Um espaço de nomes dos Hubs de Eventos fornece um contentor de âmbito exclusivo, referenciado pelo respetivo nome de domínio completamente qualificado, no qual cria um ou mais hubs de eventos. Para criar um espaço de nomes no seu grupo de recursos com o portal, faça as ações seguintes:

1. No portal do Azure, clique em **Criar um recurso** na parte superior esquerda do ecrã.
2. Selecione **todos os serviços** no menu à esquerda e selecione **estrela (`*`)** junto a **dos Hubs de eventos** no **Analytics** categoria. Confirme que **os Hubs de eventos** é adicionado ao **Favoritos** no menu de navegação à esquerda. 
    
   ![Procure Hubs de eventos](./media/event-hubs-quickstart-portal/select-event-hubs-menu.png)
3. Selecione **os Hubs de eventos** sob **Favoritos** no menu de navegação à esquerda e selecione **Add** na barra de ferramentas.

   ![Adicionar botão de barra de ferramentas](./media/event-hubs-quickstart-portal/event-hubs-add-toolbar.png)
4. Sobre o **criar espaço de nomes** página, siga os passos seguintes:
    1. Introduza um nome para o espaço de nomes. O sistema verifica imediatamente a disponibilidade do nome.
    2. Escolha o escalão de preço (básico ou Standard).
    3. Selecione o **subscrição** no qual pretende criar o espaço de nomes.
    4. Selecione um **localização** para o espaço de nomes.
    5. Selecione **Criar**. Poderá ter de aguardar alguns minutos para que o sistema aprovisione totalmente os recursos.

       ![Criar um espaço de nomes do hub de eventos](./media/event-hubs-quickstart-portal/create-event-hub1.png)
5. Atualizar o **os Hubs de eventos** página para ver o espaço de nomes do hub de eventos. Pode verificar o estado da criação do hub de eventos nos alertas. 

    ![Criar um espaço de nomes do hub de eventos](./media/event-hubs-quickstart-portal/event-hubs-refresh.png)
6. Selecione o espaço de nomes. Verá a home page de sua **espaço de nomes de Hubs de eventos** no portal. 

   ![Home page de espaço de nomes](./media/event-hubs-quickstart-portal/namespace-home-page.png)
    
## <a name="create-an-event-hub"></a>Criar um hub de eventos

Para criar um hub de eventos no espaço de nomes, faça as ações seguintes:

1. Na página do espaço de nomes de Hubs de eventos, selecione **os Hubs de eventos** no menu à esquerda.
1. Na parte superior da janela, clique em **+ Hub de Eventos**.
   
    ![Adicionar Hub de Eventos - botão](./media/event-hubs-quickstart-portal/create-event-hub4.png)
1. Escreva um nome para o seu hub de eventos e, em seguida, clique em **Criar**.
   
    ![Criar hub de eventos](./media/event-hubs-quickstart-portal/create-event-hub5.png)
4. Pode verificar o estado da criação do hub de eventos em alertas. Depois o hub de eventos é criado, vê-la na lista de hubs de eventos conforme mostrado na imagem seguinte:

    ![Hub de eventos criado](./media/event-hubs-quickstart-portal/event-hub-created.png)

Parabéns! Utilizou o portal para criar um espaço de nomes dos Hubs de Eventos e um hub de eventos nesse espaço de nomes. 

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, criou um grupo de recursos, um espaço de nomes de Hubs de Eventos e um hub de eventos. Para obter instruções passo a passo para enviar eventos para (ou) receber eventos de um hub de eventos, veja os seguintes tutoriais:  

- **Enviar eventos para um hub de eventos**: [.NET Core](event-hubs-dotnet-standard-getstarted-send.md), [.NET Framework](event-hubs-dotnet-framework-getstarted-send.md), [Java](event-hubs-java-get-started-send.md), [Python](event-hubs-python-get-started-send.md), [denode.js](event-hubs-node-get-started-send.md), [Vá](event-hubs-go-get-started-send.md), [C](event-hubs-c-getstarted-send.md)
- **Receber eventos de um hub de eventos**: [.NET Core](event-hubs-dotnet-standard-getstarted-receive-eph.md), [.NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md), [Java](event-hubs-java-get-started-receive-eph.md), [Python](event-hubs-python-get-started-receive.md), [node. js ](event-hubs-node-get-started-receive.md), [Vá](event-hubs-go-get-started-receive-eph.md), [Apache Storm](event-hubs-storm-getstarted-receive.md)


[Azure portal]: https://portal.azure.com/
[3]: ./media/event-hubs-quickstart-portal/sender1.png
[4]: ./media/event-hubs-quickstart-portal/receiver1.png
