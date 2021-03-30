---
title: incluir ficheiro
description: incluir ficheiro
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 10/16/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: bf107da82fb3f772a341e70ce472f08ea674a450
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "75692561"
---
### <a name="create-a-storage-account-for-event-processor-host"></a>Criar uma conta de armazenamento para o Anfitrião do Processador de Eventos
O Anfitrião do Processador de Eventos é um agente inteligente que simplifica a receção de eventos provenientes dos Hubs de Eventos ao gerir pontos de verificação persistentes e receções em paralelo. Para o ponto de verificação, o Anfitrião do Processador de Eventos requer uma conta de armazenamento. O exemplo seguinte mostra como criar uma conta de armazenamento e como obter as chaves de acesso:

1. No menu do portal do Azure, selecione **Criar um recurso**.

    ![Criar um item de menu de recursos, portal Microsoft Azure](./media/event-hubs-create-storage/create-resource.png)

2. Selecione **Armazenamento** > **Conta de armazenamento**.
   
    ![Selecione a conta de Armazenamento, portal Microsoft Azure](./media/event-hubs-create-storage/select-storage-account.png)

3. Na página **Criar conta de armazenamento**, siga os passos seguintes: 

   1. Introduza o nome da **conta de Armazenamento.**
   2. Escolha uma **Subscrição** Azure que contenha o centro de eventos.
   3. Escolha ou crie o **grupo de Recursos** que tem o centro de eventos.
   4. Escolha uma **localização** para criar o recurso. 
   5. Selecione **Rever + criar**.
   
        ![Rever + criar, criar conta de armazenamento, portal Microsoft Azure](./media/event-hubs-create-storage/review-create.png)

4. Na página **Rever + criar**, reveja os valores e selecione **Criar**. 

    ![Rever as definições de conta de armazenamento e criar, portal Microsoft Azure](./media/event-hubs-create-storage/create-storage-account.png)
5. Depois de ver a mensagem **'Implementações' Mensagem Conseguida** nas suas notificações, selecione **Ir ao recurso** para abrir a página 'Conta de Armazenamento'. Em alternativa, pode expandir **os detalhes da Implementação** e, em seguida, selecionar o seu novo recurso na lista de recursos.  

    ![Vá para a implementação de conta de armazenamento, portal Microsoft Azure](./media/event-hubs-create-storage/go-to-resource.png) 
6. Selecione **Recipientes**.

    ![Selecione o serviço de contentores Blobs, contas de armazenamento, portal Microsoft Azure](./media/event-hubs-create-storage/select-blob-container-service.png)
7. Selecione **+ Recipiente** na parte superior, introduza um **Nome** para o recipiente e selecione **OK**. 

    ![Criar um novo recipiente blob, contas de armazenamento, portal Microsoft Azure](./media/event-hubs-create-storage/create-new-blob-container.png)
8. Escolha **as teclas de acesso** no menu da página da conta de **Armazenamento** e copie o valor da **chave1**.

    Guarde os seguintes valores para o Notepad ou para outro local temporário.
    - Nome da conta de armazenamento
    - Chave de acesso para a conta de armazenamento
    - Nome do recipiente
