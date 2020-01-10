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
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2020
ms.locfileid: "75692561"
---
### <a name="create-a-storage-account-for-event-processor-host"></a>Criar uma conta de armazenamento para o Anfitrião do Processador de Eventos
O Anfitrião do Processador de Eventos é um agente inteligente que simplifica a receção de eventos provenientes dos Hubs de Eventos ao gerir pontos de verificação persistentes e receções em paralelo. Para o ponto de verificação, o Anfitrião do Processador de Eventos requer uma conta de armazenamento. O exemplo seguinte mostra como criar uma conta de armazenamento e como obter as chaves de acesso:

1. No menu portal do Azure, selecione **criar um recurso**.

    ![Criar um item de menu de recurso, portal do Microsoft Azure](./media/event-hubs-create-storage/create-resource.png)

2. Selecione **Armazenamento** > **Conta de armazenamento**.
   
    ![Selecione a conta de armazenamento portal do Microsoft Azure](./media/event-hubs-create-storage/select-storage-account.png)

3. Na página **Criar conta de armazenamento**, siga os passos seguintes: 

   1. Insira o **nome da conta de armazenamento**.
   2. Escolha uma **assinatura** do Azure que contenha o Hub de eventos.
   3. Escolha ou crie o **grupo de recursos** que tem o Hub de eventos.
   4. Escolha um **local** no qual criar o recurso. 
   5. Selecione **Rever + criar**.
   
        ![Examine + criar, criar conta de armazenamento portal do Microsoft Azure](./media/event-hubs-create-storage/review-create.png)

4. Na página **Rever + criar**, reveja os valores e selecione **Criar**. 

    ![Examine as configurações da conta de armazenamento e crie portal do Microsoft Azure](./media/event-hubs-create-storage/create-storage-account.png)
5. Depois de ver a mensagem **implantações bem-sucedidas** em suas notificações, selecione **ir para o recurso** para abrir a página da conta de armazenamento. Como alternativa, você pode expandir os **detalhes da implantação** e, em seguida, selecionar o novo recurso na lista de recursos.  

    ![Vá para recurso, implantação de conta de armazenamento portal do Microsoft Azure](./media/event-hubs-create-storage/go-to-resource.png) 
6. Selecione **contêineres**.

    ![Selecione o serviço de contêiner de BLOBs, contas de armazenamento portal do Microsoft Azure](./media/event-hubs-create-storage/select-blob-container-service.png)
7. Selecione **+ contêiner** na parte superior, insira um **nome** para o contêiner e selecione **OK**. 

    ![Criar um novo contêiner de BLOBs, contas de armazenamento portal do Microsoft Azure](./media/event-hubs-create-storage/create-new-blob-container.png)
8. Escolha **chaves de acesso** no menu da página da **conta de armazenamento** e copie o valor de **key1**.

    Salve os valores a seguir no bloco de notas ou em outro local temporário.
    - Nome da conta de armazenamento
    - Chave de acesso para a conta de armazenamento
    - Nome do contêiner
