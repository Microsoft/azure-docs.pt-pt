---
title: Alterar o nível de preços do espaço de nomes dos Centros de Notificação | Microsoft Docs
description: Saiba como alterar o nível de preços de um espaço de nome azure Notification Hubs.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 08/03/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/28/2019
ms.openlocfilehash: 1455259bc42aea9d506a9a2a19d725cac3d643f8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87562774"
---
# <a name="change-pricing-tier-of-an-azure-notification-hubs-namespace"></a>Alterar o nível de preços de um espaço de nome de centros de notificação Azure

Os Centros de Notificação são oferecidos em três níveis: **gratuito,** **básico** e **padrão.** Este artigo mostra-lhe como alterar o nível de preços para um espaço de nomes Azure Notification Hubs.

## <a name="overview"></a>Descrição Geral

Nos Hubs de Notificação Azure, um *centro de notificação* é o menor recurso/entidade. Geralmente mapeia para uma aplicação e pode conter um certificado para cada Sistema de Notificação de Plataforma (PNS) que apoiamos para a aplicação. A aplicação pode ser um híbrido, ou um nativo e uma aplicação cross-platform.

Um *espaço de nome* é uma coleção de centros de notificação. Cada espaço de nome geralmente consiste em centros que estão relacionados e usados para um propósito específico. Por exemplo, pode ter três espaços de nome diferentes para fins de desenvolvimento, teste e produção, respectivamente.

Pode associar um espaço de nome aos níveis de preços **gratuitos,** **básicos** ou **padrão.** Para qualquer espaço de nome, pode utilizar o nível que se adequa às suas necessidades. As secções seguintes mostram-lhe como alterar o nível de preços de um espaço de nomes de Centros de Notificação.

## <a name="use-azure-portal"></a>Utilizar o portal do Azure

Ao utilizar o portal Azure, pode alterar o nível de preços para um espaço de nome na página do espaço de nomes ou numa página do hub. Quando o mudas numa página de hub, mudas mesmo ao nível do espaço de identificação. Muda o nível de preços para o espaço de nomes e todos os centros no espaço de nomes.

### <a name="change-tier-on-the-namespace-page"></a>Alterar o nível na página do espaço de nome

O procedimento a seguir mostra como alterar o nível de preços para um espaço de nome na página do espaço de nome. Quando muda o nível para um espaço de nome, aplica-se a todos os centros nesse espaço de nome.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** no menu esquerdo.
3. Selecione Os espaços de nome do hub de **notificação** na secção **Internet of Things.** Se selecionar o asterisco `*` ao lado do texto, é adicionado à barra de navegação esquerda em **FAVORITOS**. Isto ajuda-o a aceder à página de espaços de nome mais rápido da próxima vez. Depois de o adicionar aos **FAVORITOS,** selecione **Notification Hub Namespaces**.

    ![Todos os serviços -> Avisos De Nomes](./media/change-pricing-tier/all-services-nhub.png)

4. Na página **'Notificação Hub Namespaces',** selecione o espaço de nomes para o qual pretende alterar o nível de preços.
5. Na página **'Notification Hub Namespace'** para o seu espaço de nome, pode ver o atual nível de preços para o espaço de nomes na secção **Essentials.** Na imagem a seguir, pode ver que o nível de preços do espaço de nome é **gratuito.**

    ![Nível de preços atual na página do espaço de nome](./media/change-pricing-tier/pricing-tier-before.png)

6. Na página **'Notificação Hub Namespace'** para o seu espaço de nome, selecione **O Nível de Preços** na secção **Gerir.**

    ![Selecione o nível de preços na página do espaço de nome](./media/change-pricing-tier/namespace-select-pricing-menu.png)

7. Mude o nível de preços e, em seguida, clique no botão **Seleção.**
8. Pode ver o estado da ação de alteração de nível nos **alertas**.
9. Mude para a página **'Vista Geral'.** Confirme se o novo nível é mostrado para o campo **Priceing Tier** na secção **Essentials.**
10. Este passo é opcional. Selecione qualquer hub no espaço de nomes. Confirme que vê o mesmo nível de preços na secção **Essentials.** Você deve ver o mesmo nível de preços para todos os centros no espaço de nomes.

### <a name="change-tier-on-the-hub-page"></a>Alterar o nível na página do hub

Siga estes passos para alterar o nível de preços de um espaço de nome na página do hub. Mesmo fazendo estes passos a partir da página do hub, você realmente muda o nível de preços para o espaço de nome e todos os centros no espaço de nome:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** no menu esquerdo.
3. Selecione Centros de **Notificação** na secção **Internet das Coisas.**
4. Selecione o seu **centro de** notificação .
5. Selecione **O Nível de Preços** no menu esquerdo.
6. Mude o nível de preços e clique no botão **Seleção.** Esta ação altera a definição do nível de preços para o espaço de nome que contém o hub. Então, você vê o novo nível de preços na página do espaço de nome e todas as páginas do hub.

> [!NOTE]
> Todas as alterações ao nível dos preços são eficazes imediatamente.

## <a name="use-rest-api"></a>Utilizar a API REST

Pode utilizar as seguintes APIs do Fornecedor de Recursos PARA obter o nível de preços atual e atualizá-lo.

### <a name="get-current-pricing-tier-for-a-namespace"></a>Obtenha o nível de preços atual para um espaço de nome

Para obter o nível atual do espaço de nome, envie um comando GET, como mostra o seguinte exemplo:

```REST
GET: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
```

### <a name="update-pricing-tier-for-a-namespace"></a>Nível de preços de atualização para um espaço de nome

Para atualizar o nível de espaço de nome, envie um comando PUT, como mostra o seguinte exemplo:

```REST
PUT: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
Body: <NotificationHubPlan xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"><SKU>Standard</SKU></NotificationHubPlan>
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre estes níveis e preços, consulte [os preços dos Centros de Notificação](https://azure.microsoft.com/pricing/details/notification-hubs/).
