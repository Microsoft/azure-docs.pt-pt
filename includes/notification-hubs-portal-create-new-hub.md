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
ms.openlocfilehash: 5afcc8e4524a0e8353766ba239d5ab9161b29d86
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509149"
---
1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **todos os serviços** no menu à esquerda e, em seguida, selecione **os Hubs de notificação** no **Mobile** secção. Selecione o ícone de estrela junto ao nome do serviço para adicionar o serviço para o **Favoritos** secção no menu da esquerda. Depois de adicionar **os Hubs de notificação** ao **Favoritos**, selecione-o no menu da esquerda.

      ![Portal do Azure – selecione os Hubs de notificação](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)

1. Sobre o **os Hubs de notificação** página, selecione **Add** na barra de ferramentas.

      ![Os Hubs de notificação - adicionar botão de barra de ferramentas](./media/notification-hubs-portal-create-new-hub/add-toolbar-button.png)

1. Sobre o **Hub de notificação** página, efetue os seguintes passos:

    1. Introduza um nome na **Hub de notificação**.  

    1. Introduza um nome na **criar um novo namespace**. Um espaço de nomes contém um ou mais hubs.

    1. Selecione um valor de **localização** caixa de lista pendente. Este valor Especifica a localização na qual pretende criar o hub.

    1. Selecione um grupo de recursos existente no **grupo de recursos**, ou criar um nome para um novo grupo de recursos.

    1. Selecione **Criar**.

        ![Portal do Azure – definir as propriedades do hub de notificação](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

1. Selecione **notificações** (o ícone de sino) e, em seguida, selecione **Ir para recurso**. Pode também atualizar a lista a **os Hubs de notificação** página e selecione o seu hub.

      ![Portal do Azure - notificações -> Ir para o recurso](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)

1. Selecione **Políticas de Acesso** na lista. Tenha em atenção que as duas cadeias de ligação estão disponíveis para. Irá precisar posteriormente para processar as notificações push.

      >[!IMPORTANT]
      >Fazer *não* utilizar o **DefaultFullSharedAccessSignature** política na sua aplicação. Isso se destina a ser utilizado no seu back-end.
      >

      ![Portal do Azure – cadeias de ligação do hub de notificação](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
