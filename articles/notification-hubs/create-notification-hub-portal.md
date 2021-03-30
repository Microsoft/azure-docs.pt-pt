---
title: Crie um centro de notificação Azure utilizando o portal Azure | Microsoft Docs
description: Neste tutorial, aprende-se a criar um centro de notificação Azure utilizando o portal Azure.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 08/04/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 02/14/2019
ms.openlocfilehash: 141812e83653b458a2562bf73b70117c39bf7be0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92426554"
---
# <a name="quickstart-create-an-azure-notification-hub-in-the-azure-portal"></a>Quickstart: Criar um centro de notificação Azure no portal Azure

Os Hubs de Notificação do Azure oferecem um motor de envio de escalamento horizontal e utilização fácil que lhe permite enviar notificações para qualquer plataforma (iOS, Android, Windows, Kindle, Baidu, etc.) a partir de qualquer back-end (cloud ou no local). Para mais informações sobre o serviço, consulte [o que é o Azure Notification Hubs?](notification-hubs-push-notification-overview.md)

Neste arranque rápido, cria-se um centro de notificação no portal Azure. A primeira secção dá-lhe passos para criar um espaço de nomes de Centros de Notificação e um hub nesse espaço de nome. A segunda secção dá-lhe passos para criar um centro de notificação num espaço de nomes de Centros de Notificação existente.

## <a name="create-a-namespace-and-a-notification-hub"></a>Crie um espaço de nome e um centro de notificação

Nesta secção, você cria um espaço de nome e um hub no espaço de nomes.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

## <a name="create-a-notification-hub-in-an-existing-namespace"></a>Criar um centro de notificação num espaço de nome existente

Nesta secção, você cria um centro de notificação num espaço de nome existente.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** no menu esquerdo, procure por **Notification Hub**, selecione **star** ( ) ao lado de Notification `*` Hub **Namespaces** para adicioná-lo à secção **FAVORITOS** no menu esquerdo. Selecione **espaços de nome do hub de notificação**.

      ![Portal Azure - selecione Notification Hub Namespaces](./media/create-notification-hub-portal/select-notification-hub-namespaces-all-services.png)
3. Na página **'Notificação Hub Namespaces',** selecione o seu espaço de nome na lista.

      ![Selecione o seu espaço de nome na lista](./media/create-notification-hub-portal/select-namespace.png)
4. Na página **'Notification Hub Namespace',** selecione **Add Hub** na barra de ferramentas.

      ![Espaçoes de nome do hub de notificação - Adicionar botão Hub](./media/create-notification-hub-portal/add-hub-button.png)
5. Na página **New Notification Hub,** insira um nome para o centro de notificação e selecione **OK**.

      ![Página do Novo Hub de Notificação -> insira um nome para o seu hub](./media/create-notification-hub-portal/new-notification-hub-page.png)
6. Selecione **Notificações** (ícone bell) no topo para ver o estado da implantação do novo hub. Selecione **X** no canto direito para fechar a janela de notificação.

      ![Notificação de implantação](./media/create-notification-hub-portal/deployment-notification.png)
7. Atualize a página web **do Centro de Notificações Para** ver o seu novo hub na lista.

      ![Screenshot que mostra a página web do Centro de Notificações com o novo hub da lista.](./media/create-notification-hub-portal/new-hub-in-list.png)
8. Selecione o seu **centro de notificações** para ver a página inicial do seu centro de notificação.

      ![Screenshot que mostra a página inicial para o seu centro de notificação.](./media/create-notification-hub-portal/hub-home-page.png)

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um centro de notificação. Para saber como configurar o hub com as definições do sistema de notificação da plataforma (PNS), consulte [configurar um centro de notificação com definições de PNS](configure-notification-hub-portal-pns-settings.md).
