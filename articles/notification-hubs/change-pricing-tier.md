---
title: Alterar o nível de preços do espaço de nome sinuoso dos Centros de Notificação [ Microsoft Docs
description: Saiba como alterar o nível de preços de um espaço de nome sinuoso do Azure Notification Hubs.
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
ms.openlocfilehash: 51d0880b7f56d523a01fbc993993b3caf7328134
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76261080"
---
# <a name="change-pricing-tier-of-an-azure-notification-hubs-namespace"></a>Alterar o nível de preços de um espaço de nome de centros de notificação Azure
Os Centros de Notificação são oferecidos em três níveis: **grátis,** **básicos**e **standard**. Este artigo mostra-lhe como alterar o nível de preços para um espaço de nome sinuoso do Azure Notification Hubs. 

## <a name="overview"></a>Descrição geral
Nos Hubs de Notificação Azure, um **hub** é o menor recurso/entidade. Geralmente mapeia uma aplicação e pode deter um certificado para cada Sistema de Notificação da Plataforma que suportamos para a aplicação. A aplicação pode ser um híbrido ou um nativo e uma aplicação cross-platform.

Um **espaço de nome** é uma coleção de centros de notificação. Cada espaço de nome é geralmente composto por centros que são relacionados e usados para um propósito específico. Por exemplo, pode ter três espaços de nome diferentes para fins de desenvolvimento, teste e produção, respectivamente. 

Pode associar um nível de preços ao nível do espaço de nome. O Notification Hubs suporta três níveis: **gratuito,** **básico**e **standard.** Pode usar o nível para um espaço de nome que se adequa às suas necessidades. As seguintes secções mostram-lhe como alterar o nível de preços de um espaço de nome Sitia Hubs de notificação. 

## <a name="use-azure-portal"></a>Utilizar o portal do Azure 
Ao utilizar o portal Azure, pode alterar o nível de preços para um espaço de nome na página do espaço de nome ou numa página de hub.  Quando se muda numa página do hub, muda-se ao nível do espaço de nome. Altera o nível de preços para o espaço de nome e todos os centros no espaço de nome. 

### <a name="change-tier-on-the-namespace-page"></a>Alterar o nível na página do espaço de nome
O procedimento seguinte dá-lhe passos para alterar o nível de preços para um espaço de nome na página do espaço de nome. Quando muda o nível para um espaço de nome, aplica-se a todos os centros do espaço de nome.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione **todos os serviços** no menu esquerdo. 
3. Selecione **Espaços de Nomes** do Hub de Notificação na secção **Internet das Coisas.** Se selecionar estrela`*`() ao lado do texto, é adicionado à barra de navegação esquerda em **FAVORITES**. Ajuda-o a aceder à página de espaços de nome mais rápido da próxima vez. Depois de adicioná-lo aos FAVORITOS, selecione Espaços de Nomes do **Hub de Notificação**. 

    ![Todos os serviços -> Centros de Notificação Espaços de Nome](./media/change-pricing-tier/all-services-nhub.png)
1. Na página **Notification Hub Namespaces,** selecione o espaço de nome para o qual pretende alterar o nível de preços. 
2. Na página **'Namespace'** do Centro de Notificação para o seu espaço de nome, pode ver o atual nível de preços para o espaço de nome na secção **Essentials.** Na imagem seguinte, pode ver que o nível de preços do espaço de nome é **Gratuito**. 

    ![Nível de preços atual na página do espaço de nome](./media/change-pricing-tier/pricing-tier-before.png)
1. Na página **'Namespace'** do Centro de Notificação para o seu espaço de nome, selecione **Nível de Preços** na secção **Gerir.** 

    ![Selecione o nível de preços na página do espaço de nome](./media/change-pricing-tier/namespace-select-pricing-menu.png)
6. Altere o seu nível de preços e clique no botão **Select.**    
7. Vê-se o estado da ação de alteração de nível nos **alertas**. 
8. Mude para a página **de visão geral.** Confirme que o novo nível é apresentado para o campo **De Nível de Preços** na secção **Essentials.**     
1. Este passo é opcional. Selecione qualquer hub no espaço de nome. Confirme que vê o mesmo nível de preços na secção **Essentials.** Você deve ver o mesmo nível de preços para todos os centros no espaço de nome. 

### <a name="change-tier-on-the-hub-page"></a>Alterar o nível na página do hub
O procedimento seguinte dá-lhe passos para alterar o nível de preços para um espaço de nome na página do hub. Mesmo que você faça estes passos a partir da página do hub, você realmente muda o nível de preços para o espaço de nome e todos os hubs no espaço de nome. 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione **todos os serviços** no menu esquerdo.
3. Selecione Centros de **Notificação** na secção **Internet das Coisas.** 
4. Selecione o seu **centro**de notificação . 
5. Selecione **Nível de Preços** no menu esquerdo. 
6. Altere o nível de preços e clique no botão **Select.** Esta ação altera a definição do nível de preços para o espaço de nome que contém o hub. Então, você vê o novo nível de preços na página do espaço de nome e em todas as páginas do hub. 

## <a name="use-rest-api"></a>Utilizar a API REST
Pode utilizar as seguintes APIs REST do Fornecedor de Recursos para obter o atual nível de preços e atualizá-lo. 

### <a name="get-current-pricing-tier-for-a-namespace"></a>Obtenha o nível de preços atual para um espaço de nome
Para obter o **atual nível**de espaço de nome, envie um comando GET como mostrado no seguinte exemplo: 

```REST
GET: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
```

### <a name="update-pricing-tier-for-a-namespace"></a>Atualizar o nível de preços para um espaço de nome
Para **atualizar o nível**de espaço de nome, envie um comando PUT como mostrado no seguinte exemplo: 

```REST
PUT: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
Body: <NotificationHubPlan xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"><SKU>Standard</SKU></NotificationHubPlan>
```



## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre estes níveis e preços, consulte os preços dos Centros de [Notificação.](https://azure.microsoft.com/pricing/details/notification-hubs/)
