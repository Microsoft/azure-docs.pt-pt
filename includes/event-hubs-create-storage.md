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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75692561"
---
### <a name="create-a-storage-account-for-event-processor-host"></a>Criar uma conta de armazenamento para o Anfitrião do Processador de Eventos
O Anfitrião do Processador de Eventos é um agente inteligente que simplifica a receção de eventos provenientes dos Hubs de Eventos ao gerir pontos de verificação persistentes e receções em paralelo. Para o ponto de verificação, o Anfitrião do Processador de Eventos requer uma conta de armazenamento. O exemplo seguinte mostra como criar uma conta de armazenamento e como obter as chaves de acesso:

1. A partir do menu do portal Azure, selecione **Criar um recurso**.

    ![Crie um item de menu de recursos, portal Microsoft Azure](./media/event-hubs-create-storage/create-resource.png)

2. Selecione**conta de** **armazenamento** > .
   
    ![Selecione conta de Armazenamento, portal Microsoft Azure](./media/event-hubs-create-storage/select-storage-account.png)

3. Na página **Criar conta de armazenamento**, siga os passos seguintes: 

   1. Introduza o nome da **conta de armazenamento**.
   2. Escolha uma **Subscrição** Azure que contenha o centro do evento.
   3. Escolha ou crie o **grupo Derecursos** que tem o centro do evento.
   4. Escolha um **Local** para criar o recurso. 
   5. Selecione **Rever + criar**.
   
        ![Review + criar, Criar conta de armazenamento, portal Microsoft Azure](./media/event-hubs-create-storage/review-create.png)

4. Na página **Rever + criar**, reveja os valores e selecione **Criar**. 

    ![Rever as definições da conta de armazenamento e criar, portal Microsoft Azure](./media/event-hubs-create-storage/create-storage-account.png)
5. Depois de ver a mensagem bem sucedida das **implementações** nas suas notificações, selecione **Ir recorrer** para abrir a página da Conta de Armazenamento. Em alternativa, pode expandir os detalhes da **Implementação** e, em seguida, selecionar o seu novo recurso a partir da lista de recursos.  

    ![Vá ao recurso, implementação de conta de armazenamento, portal Microsoft Azure](./media/event-hubs-create-storage/go-to-resource.png) 
6. Selecione **Recipientes**.

    ![Selecione o serviço de contentores Blobs, contas de armazenamento, portal Microsoft Azure](./media/event-hubs-create-storage/select-blob-container-service.png)
7. Selecione **+ Recipiente** na parte superior, introduza um **Nome** para o recipiente e selecione **OK**. 

    ![Criar um novo recipiente blob, contas de armazenamento, portal Microsoft Azure](./media/event-hubs-create-storage/create-new-blob-container.png)
8. Escolha **as chaves** de acesso no menu da página da conta de **armazenamento** e copie o valor do **key1**.

    Guarde os seguintes valores para o Bloco de Notas ou para qualquer outro local temporário.
    - Nome da conta de armazenamento
    - Chave de acesso para a conta de armazenamento
    - Nome do recipiente
