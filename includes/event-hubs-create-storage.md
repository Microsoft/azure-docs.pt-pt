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
ms.openlocfilehash: 690e0da160042b7ac432b1a36c024a8a8f439823
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58125135"
---
## <a name="create-a-storage-account-for-event-processor-host"></a>Criar uma conta de armazenamento para o Anfitrião do Processador de Eventos
O Anfitrião do Processador de Eventos é um agente inteligente que simplifica a receção de eventos provenientes dos Hubs de Eventos ao gerir pontos de verificação persistentes e receções em paralelo. Para o ponto de verificação, o Anfitrião do Processador de Eventos requer uma conta de armazenamento. O exemplo seguinte mostra como criar uma conta de armazenamento e como obter as chaves de acesso:

1. No portal do Azure, selecione **Criar um recurso** na parte superior esquerda do ecrã.

2. Selecione **Armazenamento** e, em seguida, **Conta de Armazenamento – blob, ficheiro, tabela, fila**.
   
    ![Selecionar uma Conta de Armazenamento](./media/event-hubs-create-storage/create-storage1.png)

3. Na página **Criar conta de armazenamento**, siga os passos seguintes: 

   1. Introduza um nome para a conta de armazenamento. 
   2. Selecione uma subscrição do Azure que contenha o hub de eventos.
   3. Selecione o grupo de recursos com o hub de eventos.
   4. Selecione uma localização onde criar o recurso. 
   5. Em seguida, clique em **Rever + criar**.
   
      ![Criar conta de armazenamento - página](./media/event-hubs-create-storage/create-storage2.png)

4. Na página **Rever + criar**, reveja os valores e selecione **Criar**. 

    ![Rever as definições da conta de armazenamento e criar](./media/event-hubs-create-storage/review-create-storage-account.png)
5. Depois de confirmar a **implementações bem-sucedidas** mensagem, selecione **Ir para recurso** na parte superior da página. Também pode iniciar a página de conta de armazenamento ao selecionar a sua conta de armazenamento na lista de recursos.  

    ![Selecionar a conta de armazenamento a partir da implementação](./media/event-hubs-create-storage/select-storage-deployment.png) 
7. Na janela **Informações Básicas**, selecione **Blobs**. 

    ![Selecione o serviço de Blobs](./media/event-hubs-create-storage/select-blobs-service.png)
1. Selecione **+ contentor** na parte superior, introduza um **nome** para o contentor e selecione **OK**. 

    ![Criar um contentor de blobs](./media/event-hubs-create-storage/create-blob-container.png)
1. Selecione **chaves de acesso** no menu à esquerda e copie o valor de **chave1**. 

    Guarde os seguintes valores no bloco de notas ou noutra localização temporária.
    - Nome da conta de armazenamento
    - Chave de acesso para a conta de armazenamento
    - Nome do contentor
