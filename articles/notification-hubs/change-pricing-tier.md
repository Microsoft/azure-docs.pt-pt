---
title: Alterar o tipo de preço do namespace de hubs de notificação | Microsoft Docs
description: Saiba como alterar o tipo de preço de um namespace de hubs de notificação do Azure.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 01/28/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/28/2019
ms.openlocfilehash: 679e8b58e5876d5460b426e694df85ded992a36d
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212560"
---
# <a name="change-pricing-tier-of-an-azure-notification-hubs-namespace"></a>Alterar o tipo de preço de um namespace de hubs de notificação do Azure
Os hubs de notificação são oferecidos em três camadas: **gratuita**, **básica**e **Standard**. Este artigo mostra como alterar o tipo de preço para um namespace de hubs de notificação do Azure. 

## <a name="overview"></a>Descrição geral
Nos hubs de notificação do Azure, um **Hub** é o menor recurso/entidade. Em geral, ele é mapeado para um aplicativo e pode conter um certificado para cada Sistema de Notificação de Plataforma que damos suporte para o aplicativo. O aplicativo pode ser um aplicativo híbrido ou nativo e de plataforma cruzada.

Um **namespace** é uma coleção de hubs de notificação. Cada namespace geralmente consiste em hubs relacionados e usados para uma finalidade específica. Por exemplo, você poderia ter três namespaces diferentes para fins de desenvolvimento, teste e produção, respectivamente. 

Você pode associar um tipo de preço no nível de namespace. Os hubs de notificação dão suporte a três camadas: **gratuita**, **básica**e **Standard**. Você pode usar a camada para um namespace que atenda às suas necessidades. As seções a seguir mostram como alterar o tipo de preço de um namespace de hubs de notificação. 

## <a name="use-azure-portal"></a>Utilizar o portal do Azure 
Ao usar portal do Azure, você pode alterar o tipo de preço de um namespace na página de namespace ou em uma página de Hub.  Ao alterá-lo em uma página de Hub, você realmente o altera no nível de namespace. Ele altera o tipo de preço para o namespace e todos os hubs no namespace. 

### <a name="change-tier-on-the-namespace-page"></a>Alterar camada na página do namespace
O procedimento a seguir fornece as etapas para alterar o tipo de preço de um namespace na página namespace. Quando você altera a camada de um namespace, ela se aplica a todos os hubs no namespace.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **todos os serviços** no menu à esquerda. 
3. Selecione os **namespaces do hub de notificação** na seção **Internet das coisas** . Se você selecionar Star (`*`) ao lado do texto, ele será adicionado à barra de navegação à esquerda em **favoritos**. Ele ajuda você a acessar a página de namespaces mais rápido na próxima vez em diante. Depois de adicioná-lo aos favoritos, selecione **namespaces do hub de notificação**. 

    ![Todos os serviços-> namespaces do hub de notificação](./media/change-pricing-tier/all-services-nhub.png)
1. Na página **namespaces do hub de notificação** , selecione o namespace para o qual você deseja alterar o tipo de preço. 
2. Na página **namespace do hub de notificação** para seu namespace, você pode ver o tipo de preço atual para o namespace na seção **Essentials** . Na imagem a seguir, você pode ver que o tipo de preço do namespace é **gratuito**. 

    ![Tipo de preço atual na página de namespace](./media/change-pricing-tier/pricing-tier-before.png)
1. Na página **namespace do hub de notificação** para seu namespace, selecione **tipo de preço** na seção **gerenciar** . 

    ![Selecione o tipo de preço na página namespace](./media/change-pricing-tier/namespace-select-pricing-menu.png)
6. Altere o tipo de preço e clique no botão **selecionar** .    
7. Você verá o status da ação de alteração de camada nos **alertas**. 
8. Alterne para a página **visão geral** . Confirme se a nova camada é mostrada para o campo **tipo de preço** na seção **Essentials** .     
1. Este passo é opcional. Selecione qualquer Hub no namespace. Confirme que você vê o mesmo tipo de preço na seção **Essentials** . Você deve ver o mesmo tipo de preço para todos os hubs no namespace. 

### <a name="change-tier-on-the-hub-page"></a>Alterar camada na página de Hub
O procedimento a seguir fornece as etapas para alterar o tipo de preço de um namespace na página Hub. Embora você execute essas etapas a partir da página de Hub, você realmente altera o tipo de preço para o namespace e todos os hubs no namespace. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **todos os serviços** no menu à esquerda.
3. Selecione **hubs de notificação** na seção **Internet das coisas** . 
4. Selecione o **Hub**de notificação. 
5. Selecione **tipo de preço** no menu à esquerda. 
6. Altere o tipo de preço e clique no botão **selecionar** . Essa ação altera a configuração do tipo de preço para o namespace que contém o Hub. Portanto, você verá o novo tipo de preço na página de namespace e em todas as páginas de Hub. 

## <a name="use-rest-api"></a>Utilizar a API REST
Você pode usar as seguintes APIs REST do provedor de recursos para obter o tipo de preço atual e atualizá-lo. 

### <a name="get-current-pricing-tier-for-a-namespace"></a>Obter o tipo de preço atual para um namespace
Para obter a **camada de namespace atual**, envie um comando Get, conforme mostrado no exemplo a seguir: 

```REST
GET: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
```

### <a name="update-pricing-tier-for-a-namespace"></a>Atualizar o tipo de preço para um namespace
Para **atualizar a camada de namespace**, envie um comando put, conforme mostrado no exemplo a seguir: 

```REST
PUT: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
Body: <NotificationHubPlan xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"><SKU>Standard</SKU></NotificationHubPlan>
```



## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre essas camadas e preços, consulte [preços dos hubs de notificação](https://azure.microsoft.com/pricing/details/notification-hubs/).
