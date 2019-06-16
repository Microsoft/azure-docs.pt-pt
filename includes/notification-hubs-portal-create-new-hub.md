---
title: incluir ficheiro
description: incluir ficheiro
services: notification-hubs
author: jwargo
ms.service: notification-hubs
ms.topic: include
ms.date: 01/17/2019
ms.author: jowargo
ms.custom: include file
ms.openlocfilehash: 244a4ebe20863945bfc3b6236e70e786387c8909
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67116645"
---
1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **todos os serviços** no menu à esquerda e selecione **os Hubs de notificação** no **Mobile** secção. Selecione o ícone de estrela junto ao nome do serviço para adicionar o serviço para o **Favoritos** secção no menu da esquerda. Depois de adicionar **os Hubs de notificação** ao **Favoritos**, selecione-o no menu da esquerda.

      ![Portal do Azure – selecione os Hubs de notificação](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)

1. Sobre o **os Hubs de notificação** página, selecione **Add** na barra de ferramentas.

      ![Os Hubs de notificação - adicionar botão de barra de ferramentas](./media/notification-hubs-portal-create-new-hub/add-toolbar-button.png)

1. Sobre o **Hub de notificação** página, efetue os seguintes passos:

    1. Introduza um nome na **Hub de notificação**.  

    1. Introduza um nome na **criar um novo namespace**. Um espaço de nomes contém um ou mais hubs.

    1. Selecione um valor de **localização** caixa de lista pendente. Este valor Especifica a localização na qual pretende criar o hub de notificação.

    1. Selecione um grupo de recursos existente no **grupo de recursos**, ou criar um nome para um novo grupo de recursos.

    1. Selecione **Criar**.

        ![Portal do Azure – definir as propriedades do hub de notificação](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

1. Selecione **notificações** (o ícone de sino) e selecione **Ir para recurso**. Pode também atualizar a lista a **os Hubs de notificação** página e selecione o hub de notificação.

      ![Portal do Azure - notificações -> Ir para o recurso](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)

1. Selecione **Políticas de Acesso** na lista. Anote as duas cadeias de ligação que tem à sua disposição. Irá precisar destas para processar as notificações push mais tarde.

      >[!IMPORTANT]
      >Fazer *não* utilizar o **DefaultFullSharedAccessSignature** política na sua aplicação. Isso se destina a ser utilizado no seu back-end.
      >

      ![Portal do Azure – cadeias de ligação do hub de notificação](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
