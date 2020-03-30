---
title: Mesa Azure no programa de marketplace comercial Mercado Azure
description: Configure gestão de chumbo para Azure Blob
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 7/30/2019
ms.author: dsindona
ms.openlocfilehash: 062252b007e22fcd2644c8b647fc0ecc2f5938cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285253"
---
# <a name="lead-management-instructions-for-azure-blob"></a>Instruções de gestão de chumbo para Azure Blob

>[!Caution]
>A opção Azure Blob para processar os leads da sua oferta de mercado foi depreciada. Se tem atualmente uma oferta publicada com configuração de gestão de chumbo para o Azure Blob, já não está a receber pistas de clientes. Por favor, atualize a sua configuração de gestão de chumbo para qualquer uma das outras opções de gestão de chumbo. Conheça as outras opções na página de aterragem de [gestão de chumbo](./commercial-marketplace-get-customer-leads.md)."

Se o seu sistema de Gestão de Relacionamento com o Cliente (CRM) não for explicitamente suportado no Partner Center para receber os leads Azure Marketplace e AppSource, pode utilizar um Azure Blob para lidar com estes cabos. Em seguida, pode optar por exportar os dados e importá-lo para o seu sistema de CRM. As instruções deste artigo dar-lhe-ão através do processo de criação de uma conta de Armazenamento Azure, e de um Azure Blob sob essa conta. Além disso, pode criar um novo fluxo utilizando o Microsoft Flow para enviar uma notificação de e-mail quando a sua oferta receber um chumbo.


## <a name="how-to-configure-azure-blob"></a>Como configurar o Azure Blob

1. Se não tiver uma conta Azure, pode [criar uma conta de teste gratuita.](https://azure.microsoft.com/pricing/free-trial/)
1. Depois da sua conta Azure estar ativa, inscreva-se no [portal Azure.](https://portal.azure.com)
1. No portal Azure, crie uma conta de armazenamento utilizando o seguinte procedimento.  
    1. Selecione **+Criar um recurso** na barra de menu seletiva esquerda.  O **New** novo painel (lâmina) será exibido à direita.
    2. Selecione **Armazenamento** no painel **Novo.**  Uma lista **em destaque** é apresentada à direita.
    3. Selecione a **Conta de Armazenamento** para iniciar a criação de conta.  Siga as instruções do artigo [Criar uma conta](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)de armazenamento .

    ![Passos para criar uma conta de armazenamento Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    Para mais informações sobre contas de armazenamento, selecione [tutorial Quickstart](https://docs.microsoft.com/azure/storage/).  Para obter mais informações sobre os preços de armazenamento, consulte [os preços de armazenamento.](https://azure.microsoft.com/pricing/details/storage/)

4. Aguarde até que a sua conta de armazenamento seja disponibilizada, um processo que normalmente demora alguns minutos.  Em seguida, aceda à sua conta de armazenamento a partir da página **inicial** do portal Azure, selecionando **Todos os seus recursos** ou selecionando Todos os **recursos** da barra de menus de navegação esquerda do portal Azure.

    ![Aceda à sua conta de armazenamento Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-access.png)

5. A partir do painel da sua conta de armazenamento, selecione **teclas de acesso** e copie o valor de cadeia de *ligação* para a chave. Guarde este valor, uma vez que este é o valor de string de *conexão* de conta de armazenamento que você precisará fornecer no portal de publicação para receber pistas para a sua oferta de mercado.

     Um exemplo de uma picada de ligação é:

     ```sql
     DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
     ```

    ![Chave de armazenamento azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-keys-2.png)

6. Na sua página de conta de armazenamento, selecione **Blobs**.

   ![Chave de armazenamento azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/select-blobs.png)

7. Uma vez na página blobs, selecione o botão **+ Recipiente.**

8. Escreva um **nome** para o seu novo recipiente. O nome do contentor tem estar em minúsculas, tem de começar com uma letra ou um número e só pode incluir letras, números e o caráter de travessão (-). Para obter mais informações sobre nomeações de contentores e blobs, consulte [Nomear e referenciar contentores, bolhas e metadados.](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)

    Guarde este valor, uma vez que este é o valor do Nome do *Recipiente* que necessita de fornecer no portal editorial para receber pistas para a sua oferta de mercado.

9. Definir o nível de acesso público ao contentor como **Privado (sem acesso anónimo)**.

10. Selecione **OK** para criar o contentor.

    ![Novo Recipiente](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-blob"></a>Configure a sua oferta para enviar leva ao Azure Blob

Quando estiver pronto para configurar as informações de gestão de chumbo para a sua oferta no portal editorial, siga os passos abaixo:

1. Navegue na página de **configuração** da Oferta para a sua oferta.
2. Selecione **Ligar** sob a secção de Gestão de Chumbo.

    ![Oferta de Ligação](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-offer.png)

3. Na janela pop-up de detalhes da Ligação, selecione **Azure Blob** para o Destino Principal.

    ![Ligar Detalhes](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. Forneça o nome do **recipiente** e a cadeia de **ligação à conta** de armazenamento que obteve de seguir estas instruções.

    * Exemplo de nome do recipiente:`marketplaceleadcontainer`
    * Exemplo de linha `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net` ![de ligação de conta de armazenamento: Detalhe de ligação](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. Selecione **Guardar**.

    > [!NOTE]
    > Tem de terminar de configurar o resto da oferta e publicá-la antes de poder receber pistas para a oferta.


