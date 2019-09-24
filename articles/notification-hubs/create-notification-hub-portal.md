---
title: Criar um hub de notificação do Azure usando o portal do Azure | Microsoft Docs
description: Neste tutorial, você aprenderá a criar um hub de notificação do Azure usando o portal do Azure.
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
ms.openlocfilehash: 53abc28a6923c2d55b3bb39defb08778485a9744
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212454"
---
# <a name="create-an-azure-notification-hub-in-the-azure-portal"></a>Criar um hub de notificação do Azure no portal do Azure 
Os Hubs de Notificação do Azure oferecem um motor de envio de escalamento horizontal e utilização fácil que lhe permite enviar notificações para qualquer plataforma (iOS, Android, Windows, Kindle, Baidu, etc.) a partir de qualquer back-end (cloud ou no local). Para obter mais informações sobre o serviço, consulte [o que são hubs de notificação do Azure?](notification-hubs-push-notification-overview.md).

Neste guia de início rápido, você cria um hub de notificação no portal do Azure. A primeira seção fornece as etapas para criar um namespace de hubs de notificação e um Hub nesse namespace. A segunda seção fornece as etapas para criar um hub de notificação em um namespace existente dos hubs de notificação. 

## <a name="create-a-namespace-and-a-notification-hub"></a>Criar um namespace e um hub de notificação
Nesta seção, você cria um namespace e um Hub no namespace. 

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

## <a name="create-a-notification-hub-in-an-existing-namespace"></a>Criar um hub de notificação em um namespace existente
Nesta seção, você cria um hub de notificação em um namespace existente. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **todos os serviços** no menu à esquerda, procure **Hub de notificação**, selecione Estrela`*`() ao lado de **namespaces do hub de notificação** para adicioná-lo à seção **favoritos** no menu à esquerda. Selecione os **namespaces do hub de notificação**. 

      ![Portal do Azure-selecionar namespaces do hub de notificação](./media/create-notification-hub-portal/select-notification-hub-namespaces-all-services.png)
3. Na página **namespaces do hub de notificação** , selecione o namespace na lista. 

      ![Selecione o namespace na lista](./media/create-notification-hub-portal/select-namespace.png)
1. Na página **namespace do hub de notificação** , selecione **Adicionar Hub** na barra de ferramentas. 

      ![Namespaces do hub de notificação-botão Adicionar Hub](./media/create-notification-hub-portal/add-hub-button.png)
4. Na página **novo hub de notificação** , insira um nome para o Hub de notificação e selecione **OK**.

      ![Página novo hub de notificação-> Insira um nome para o Hub](./media/create-notification-hub-portal/new-notification-hub-page.png)
4. Selecione **notificações** (ícone de sino) na parte superior para ver o status da implantação do novo hub. Selecione **X** no canto direito para fechar a janela de notificação. 

      ![Notificação de implantação](./media/create-notification-hub-portal/deployment-notification.png)
5. Atualize a página da Web **namespaces do hub de notificação** para ver o novo hub na lista. 

      ![Portal do Azure - notificações -> Ir para o recurso](./media/create-notification-hub-portal/new-hub-in-list.png)
6. Selecione o **Hub de notificação** para ver o home page do hub de notificação. 

      ![Portal do Azure - notificações -> Ir para o recurso](./media/create-notification-hub-portal/hub-home-page.png)

## <a name="next-steps"></a>Passos seguintes
Neste guia de início rápido, você criou um hub de notificação. Para saber como configurar o Hub com as configurações do PNS (sistema de notificação de plataforma), consulte [configurar um hub de notificação com as configurações do PNS](configure-notification-hub-portal-pns-settings.md). 