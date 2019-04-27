---
title: Criar um hub de notificação do Azure com o portal do Azure | Documentos da Microsoft
description: Neste tutorial, saiba como criar um hub de notificação do Azure com o portal do Azure.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: jowargo
ms.openlocfilehash: 62e72f27e48f7bf220901f4eb36090f926724a2a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60682042"
---
# <a name="create-an-azure-notification-hub-in-the-azure-portal"></a>Criar um hub de notificação do Azure no portal do Azure 
Os Hubs de Notificação do Azure oferecem um motor de envio de escalamento horizontal e utilização fácil que lhe permite enviar notificações para qualquer plataforma (iOS, Android, Windows, Kindle, Baidu, etc.) a partir de qualquer back-end (cloud ou no local). Para obter mais informações sobre o serviço, consulte [o que é o Notification Hubs do Azure?](notification-hubs-push-notification-overview.md).

Neste início rápido, vai criar um hub de notificação no portal do Azure. A primeira secção dá-lhe os passos para criar um espaço de nomes de Hubs de notificação e um hub nesse espaço de nomes. A segunda secção dá-lhe os passos para criar um hub de notificação num espaço de nomes de Hubs de notificação existente. 

## <a name="create-a-namespace-and-a-notification-hub"></a>Criar um espaço de nomes e um hub de notificação
Nesta secção, vai criar um espaço de nomes e um hub no espaço de nomes. 

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

## <a name="create-a-notification-hub-in-an-existing-namespace"></a>Criar um hub de notificação num espaço de nomes existente
Nesta secção, vai criar um hub de notificação num espaço de nomes existente. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **todos os serviços** no menu à esquerda e procure **Hub de notificação**, selecione **estrela** (`*`) junto a **espaços de nomes do Hub de notificação** para adicioná-lo para o **Favoritos** secção no menu da esquerda. Selecione **espaços de nomes de Hub de notificação**. 

      ![Portal do Azure – selecione espaços de nomes de Hub de notificação](./media/create-notification-hub-portal/select-notification-hub-namespaces-all-services.png)
3. Sobre o **espaços de nomes de Hub de notificação** , selecione o seu espaço de nomes da lista. 

      ![Selecione o seu espaço de nomes da lista](./media/create-notification-hub-portal/select-namespace.png)
1. Sobre o **espaço de nomes de Hub de notificação** página, selecione **adicionar Hub** na barra de ferramentas. 

      ![Adicionar espaços de nomes do Notification Hub - botão de Hub](./media/create-notification-hub-portal/add-hub-button.png)
4. Sobre o **novo Hub de notificação** página, introduza um nome para o hub de notificação e selecione **OK**.

      ![Novo Hub de notificação página -> introduza um nome para o seu hub](./media/create-notification-hub-portal/new-notification-hub-page.png)
4. Selecione **notificações** (ícone de sino) na parte superior para ver o estado da implementação do novo hub. Selecione **X** no canto direito para fechar a janela de notificação. 

      ![Notificação de implementação](./media/create-notification-hub-portal/deployment-notification.png)
5. Atualizar o **espaços de nomes de Hub de notificação** página da web para ver o novo hub na lista. 

      ![Portal do Azure - notificações -> Ir para o recurso](./media/create-notification-hub-portal/new-hub-in-list.png)
6. Selecione seu **hub de notificação** para ver a home page para o seu hub de notificação. 

      ![Portal do Azure - notificações -> Ir para o recurso](./media/create-notification-hub-portal/hub-home-page.png)

## <a name="next-steps"></a>Passos Seguintes
Neste início rápido, criou um hub de notificação. Para saber como configurar o hub com definições de sistema (PNS) de notificação de plataforma, veja [configurar um hub de notificações com as definições de PNS](configure-notification-hub-portal-pns-settings.md). 