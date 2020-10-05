---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: notification-hubs
author: jwargo
ms.service: notification-hubs
ms.topic: include
ms.date: 01/17/2019
ms.author: jowargo
ms.custom: include file
ms.openlocfilehash: 5afcc8e4524a0e8353766ba239d5ab9161b29d86
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "67509149"
---
1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **todos os serviços** no menu esquerdo e, em seguida, selecione **'Centros de Notificação'** na secção **Mobile.** Selecione o ícone estrela ao lado do nome de serviço para adicionar o serviço à secção **FAVORITOS** no menu esquerdo. Depois de adicionar **Os Centros de Notificação** aos **FAVORITOS,** selecione-o no menu esquerdo.

      ![Portal Azure - selecione Centros de Notificação](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)

1. Na página **'Centros de Notificação',** selecione **Adicionar** na barra de ferramentas.

      ![Centros de Notificação - Adicionar botão de barra de ferramentas](./media/notification-hubs-portal-create-new-hub/add-toolbar-button.png)

1. Na página Do **Centro de Notificação,** faça os seguintes passos:

    1. Introduza um nome no **Centro de Notificação**.  

    1. Introduza um nome em **Criar um novo espaço de nome.** Um espaço com nomes contém um ou mais centros.

    1. Selecione um valor na caixa de lista de **localização.** Este valor especifica a localização em que pretende criar o hub.

    1. Selecione um grupo de recursos existente no **Grupo de Recursos**ou crie um nome para um novo grupo de recursos.

    1. Selecione **Criar**.

        ![Portal do Azure – definir as propriedades do hub de notificação](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

1. Selecione **Notificações** (o ícone da campainha) e, em seguida, selecione **Ir para o recurso**. Também pode atualizar a lista na página **'Centros de Notificação'** e selecionar o seu hub.

      ![Portal do Azure - notificações -> Ir para o recurso](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)

1. Selecione **Políticas de Acesso** na lista. Note que as duas cordas de ligação estão disponíveis para si. Vai precisar deles mais tarde para lidar com notificações push.

      >[!IMPORTANT]
      >*Não* utilize a política **de assinatura DefaultFulFuldAccesss na** sua aplicação. Isto é para ser usado apenas na sua parte de trás.
      >

      ![Portal do Azure – cadeias de ligação do hub de notificação](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
