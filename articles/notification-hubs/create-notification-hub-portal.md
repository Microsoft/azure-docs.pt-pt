---
title: Criar um hub de notificação Azure utilizando o portal Azure [ Microsoft Docs
description: Neste tutorial, aprende-se a criar um hub de notificação Azure utilizando o portal Azure.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/14/2019
ms.openlocfilehash: 3aeeb989d15dc74849c85fa58cbefa891809f3c5
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80347079"
---
# <a name="quickstart-create-an-azure-notification-hub-in-the-azure-portal"></a>Quickstart: Criar um hub de notificação Azure no portal Azure 
Os Hubs de Notificação do Azure oferecem um motor de envio de escalamento horizontal e utilização fácil que lhe permite enviar notificações para qualquer plataforma (iOS, Android, Windows, Kindle, Baidu, etc.) a partir de qualquer back-end (cloud ou no local). Para mais informações sobre o serviço, consulte [o Que é o Azure Notification Hubs?](notification-hubs-push-notification-overview.md)

Neste arranque rápido, cria-se um centro de notificação no portal Azure. A primeira secção dá-lhe passos para criar um espaço de nome sinuoso de Centros de Notificação e um hub nesse espaço de nome. A segunda secção dá-lhe passos para criar um hub de notificação num espaço de nome sinuoso de Centros de Notificação existente. 

## <a name="create-a-namespace-and-a-notification-hub"></a>Criar um espaço de nome e um centro de notificação
Nesta secção, cria-se um espaço de nome e um hub no espaço de nome. 

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

## <a name="create-a-notification-hub-in-an-existing-namespace"></a>Criar um centro de notificação num espaço de nome existente
Nesta secção, cria um centro de notificação num espaço de nome existente. 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione **todos os serviços** no menu esquerdo, procure o Centro de **Notificação,** selecione **star** ()`*`ao lado dos espaços de nomes do Centro de **Notificação** para adicioná-lo à secção **FAVORITES** no menu esquerdo. Selecione Espaços de Nome sinuosos do Centro de **Notificação**. 

      ![Portal Azure - selecione Notification Hub Namespaces](./media/create-notification-hub-portal/select-notification-hub-namespaces-all-services.png)
3. Na página **'Namespaces'** do Centro de Notificação, selecione o seu espaço de nome na lista. 

      ![Selecione o seu espaço de nome da lista](./media/create-notification-hub-portal/select-namespace.png)
1. Na página **'Namespace' do Hub de Notificação,** selecione **Adicionar Hub** na barra de ferramentas. 

      ![Espaços de nome do hub de notificação - Adicionar botão Hub](./media/create-notification-hub-portal/add-hub-button.png)
4. Na página **New Notification Hub,** introduza um nome para o centro de notificação e selecione **OK**.

      ![Nova página do Hub de Notificação -> insira um nome para o seu hub](./media/create-notification-hub-portal/new-notification-hub-page.png)
4. Selecione **Notificações** (ícone bell) na parte superior para ver o estado da implantação do novo hub. Selecione **X** no canto direito para fechar a janela de notificação. 

      ![Notificação de implantação](./media/create-notification-hub-portal/deployment-notification.png)
5. Refresque a página web Notification **Hub Namespaces** para ver o seu novo hub na lista. 

      ![Portal do Azure - notificações -> Ir para o recurso](./media/create-notification-hub-portal/new-hub-in-list.png)
6. Selecione o seu centro de **notificação** para ver a página inicial para o seu centro de notificação. 

      ![Portal do Azure - notificações -> Ir para o recurso](./media/create-notification-hub-portal/hub-home-page.png)

## <a name="next-steps"></a>Passos seguintes
Neste arranque rápido, criou um centro de notificação. Para saber configurar o hub com as definições do sistema de notificação da plataforma (PNS), consulte Configurar um centro de [notificação com definições de PNS](configure-notification-hub-portal-pns-settings.md). 