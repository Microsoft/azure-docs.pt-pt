---
title: Preços do escalão de espaço de nomes de Hubs de notificação de alteração | Documentos da Microsoft
description: Saiba como alterar o escalão de preço de um espaço de nomes de Hubs de notificação do Azure.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 01/28/2019
ms.author: jowargo
ms.openlocfilehash: 99ea21b3eb01a674a89c70a1b923f02e600cc3c5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57999471"
---
# <a name="change-pricing-tier-of-an-azure-notification-hubs-namespace"></a>Alterar o escalão de preço de um espaço de nomes de hubs de notificação do Azure
Os Hubs de notificação são disponibilizados em três escalões: **gratuita**, **básica**, e **padrão**. Este artigo mostra-lhe como alterar o escalão de preço para um espaço de nomes de Hubs de notificação do Azure. 

## <a name="overview"></a>Descrição geral
Nos Hubs de notificação do Azure, um **hub** é a entidade/recurso mais pequena. Ele geralmente é mapeado para um aplicativo e pode conter um certificado para cada sistema de notificação de plataforma oferecemos suporte para a aplicação. A aplicação pode ser híbrida ou um nativo e um aplicativo de várias plataformas.

R **espaço de nomes** é uma coleção dos hubs de notificação. Cada espaço de nomes geralmente consiste em hubs que estejam relacionados com e utilizados para um fim específico. Por exemplo, poderia ter três namespaces diferentes para fins de desenvolvimento, teste e produção, respetivamente. 

Pode associar um escalão de preço ao nível do espaço de nomes. Os Notification Hubs suporta três escalões: **gratuita**, **básica**, e **padrão**. Pode utilizar o escalão para um espaço de nomes que atenda aos seus requisitos. As secções seguintes mostram como alterar o escalão de preço de um espaço de nomes de Hubs de notificação. 

## <a name="use-azure-portal"></a>Utilizar o portal do Azure 
Ao utilizar o portal do Azure, pode alterar o escalão de preço para um espaço de nomes na página espaço de nomes ou numa página de hub.  Quando alterá-lo numa página de hub, na verdade, alterá-lo ao nível do espaço de nomes. Ele altera o escalão de preço para o espaço de nomes e todos os hubs num espaço de nomes. 

### <a name="change-tier-on-the-namespace-page"></a>Alteração do escalão na página do espaço de nomes
O procedimento seguinte fornece passos para alterar o escalão de preço para um espaço de nomes na página do espaço de nomes. Quando alterar a camada para um espaço de nomes, aplica-se a todos os hubs no espaço de nomes.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **todos os serviços** no menu da esquerda. 
3. Selecione **espaços de nomes de Hub de notificação** no **Internet das coisas** secção. Se selecionar asterisco (`*`) junto do texto, é adicionado à barra de navegação à esquerda sob **Favoritos**. Ajuda-o com o acesso à página de espaços de nomes mais rapidamente da próxima vez em diante. Depois de adicioná-lo para os favoritos, selecione **espaços de nomes de Hub de notificação**. 

    ![Todos os serviços -> espaços de nomes de Hub de notificação](./media/change-pricing-tier/all-services-nhub.png)
1. Sobre o **espaços de nomes de Hub de notificação** , selecione o espaço de nomes para o qual pretende alterar o escalão de preço. 
2. Sobre o **espaço de nomes de Hub de notificação** página para o espaço de nomes, pode ver o escalão de preço atual para o espaço de nomes no **Essentials** secção. Na imagem seguinte, pode ver que é o escalão de preço do espaço de nomes **gratuito**. 

    ![Escalão de preço na página do espaço de nomes de atual](./media/change-pricing-tier/pricing-tier-before.png)
1. Sobre o **espaço de nomes de Hub de notificação** página para o espaço de nomes, selecione **escalão de preço** sob **gerir** secção. 

    ![Selecione o escalão de preço na página do espaço de nomes](./media/change-pricing-tier/namespace-select-pricing-menu.png)
6. Alterar o escalão de preço e clique nas **selecione** botão.    
7. Verá o estado da camada de ação de alterar o **alertas**. 
8. Mude para o **descrição geral** página. Confirme que o novo escalão é apresentado para o **escalão de preço** campo na **Essentials** secção.     
1. Este passo é opcional. Selecione qualquer hub no espaço de nomes. Confirme que consegue ver os preços mesmo escalão no **Essentials** secção. Deverá ver o mesmo escalão de preços para todos os hubs no espaço de nomes. 

### <a name="change-tier-on-the-hub-page"></a>Alteração do escalão na página de hub
O procedimento seguinte fornece passos para alterar o escalão de preço para um espaço de nomes na página de hub. Apesar de efetuar estes passos a partir da página de hub, na verdade, alterar o escalão de preço para o espaço de nomes e todos os hubs num espaço de nomes. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **todos os serviços** no menu da esquerda.
3. Selecione **os Hubs de notificação** no **Internet das coisas** secção. 
4. Selecione a sua notificação **hub**. 
5. Selecione **escalão de preço** no menu da esquerda. 
6. Alterar o escalão de preço e clique nas **selecione** botão. Esta ação altera a definição de escalão de preço para o espaço de nomes que contém o hub. Por isso, verá o novo escalão de preço na página de espaço de nomes e todas as páginas de hub. 

## <a name="use-rest-api"></a>Utilizar a API REST
Pode utilizar as APIs REST do fornecedor de recursos seguintes para obter o escalão de preço atual e atualizá-lo. 

### <a name="get-current-pricing-tier-for-a-namespace"></a>Obter o escalão de preço atual para um espaço de nomes
Para obter o **escalão atual do espaço de nomes**, enviar um comando GET, conforme mostrado no exemplo a seguir: 

```REST
GET: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
```

### <a name="update-pricing-tier-for-a-namespace"></a>Atualizar o escalão de preço para um espaço de nomes
Para **atualizar o escalão de espaço de nomes**, enviar um comando PUT, conforme mostrado no exemplo a seguir: 

```REST
PUT: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
Body: <NotificationHubPlan xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"><SKU>Standard</SKU></NotificationHubPlan>
```



## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre estes escalões e preços, consulte [preços de Hubs de notificação](https://azure.microsoft.com/pricing/details/notification-hubs/).
