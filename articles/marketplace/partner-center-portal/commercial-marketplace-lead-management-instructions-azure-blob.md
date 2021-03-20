---
title: Gestão de chumbo com armazenamento Azure Blob - Mercado comercial da Microsoft
description: Saiba como usar o Azure Blob para configurar os leads para o Microsoft AppSource e para o Azure Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 05/01/2020
ms.openlocfilehash: bd2f3d40b1aea1c0133f95e069ebfd527d30bd59
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94491108"
---
# <a name="use-azure-blob-storage-to-manage-commercial-marketplace-leads"></a>Use o armazenamento Azure Blob para gerir os leads de marketplace comercial

>[!Caution]
>O suporte comercial ao armazenamento da Azure Blob foi depreciado e já não é uma opção para processar os leads da sua oferta. Se atualmente tem uma oferta de marketplace comercial com gestão de chumbo configurada para a Azure Blob, deixará de receber os leads de clientes. Por favor, atualize a sua configuração de gestão de chumbo para qualquer uma das outras opções de gestão de chumbo. Saiba mais sobre as outras opções na página de aterragem de [gestão de chumbo](./commercial-marketplace-get-customer-leads.md)."

 Se o seu sistema de Gestão de Relacionamento com Clientes (CRM) não for suportado explicitamente no Partner Center para receber leads microsoft AppSource e Azure Marketplace, pode utilizar o armazenamento Azure Blob. Em seguida, pode optar por exportar os dados e importá-lo para o seu sistema crm. As instruções deste artigo dar-lhe-ão através do processo de criação de uma conta de Armazenamento Azure, e uma bolha nessa conta. Além disso, pode criar um novo fluxo utilizando o Power Automamate para enviar uma notificação por e-mail quando a sua oferta receber uma pista.

>[!NOTE]
>O conector Power Automamate utilizado nestas instruções requer uma subscrição paga da Power Automamate. Certifique-se de que o faz antes de seguir as instruções deste artigo.

## <a name="configure-azure-blob-storage"></a>Configure armazenamento Azure Blob

1. Se não tiver uma conta Azure, pode [criar uma conta de teste gratuita.](https://azure.microsoft.com/pricing/free-trial/)

2. Depois da sua conta Azure estar ativa, inscreva-se no [portal Azure](https://portal.azure.com).

3. No portal Azure, crie uma conta de armazenamento utilizando o seguinte procedimento.  
    1. Selecione **+Crie um recurso** na barra de menu esquerda.  O **painel novo** (lâmina) será apresentado à direita.
    2. Selecione **Armazenamento** no **painel Novo.**  Uma lista **em destaque** é apresentada à direita.
    3. Selecione a **Conta de Armazenamento** para iniciar a criação de conta.  Siga as instruções do artigo [Crie uma conta de armazenamento](../../storage/common/storage-account-create.md?tabs=azure-portal).

    ![Passos para criar uma conta de armazenamento Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    Para obter mais informações sobre contas de armazenamento, consulte este [Quickstart](../../storage/blobs/storage-quickstart-blobs-portal.md).  Para obter mais informações sobre os preços de armazenamento, consulte [os preços de armazenamento.](https://azure.microsoft.com/pricing/details/storage/)

4. Aguarde até que a sua conta de armazenamento seja a provisionada, um processo que normalmente demora alguns minutos.  Em seguida, aceda à sua conta de armazenamento a partir da página **inicial** do portal Azure selecionando **Ver todos os seus recursos** ou selecionando todos os **recursos** a partir da barra de menus de navegação esquerda do portal Azure.

    ![Aceda à sua conta de armazenamento Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-access.png)

5. A partir do painel de conta de armazenamento, selecione **as teclas de acesso** e copie o valor da cadeia De *ligação* para a tecla. Guarde este valor pois este é o valor *de cadeia de ligação de conta de armazenamento* que terá de fornecer no portal de publicação para receber leads para a sua oferta de mercado.

     Um exemplo de uma cadeia de ligação é:

     ```sql
     DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
     ```

    ![Screenshot que mostra a página "Teclas de acesso" com a caixa de texto "Connection string" realçada.](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-keys-2.png)

6. Na sua página de conta de armazenamento, selecione **Blobs**.

   ![Screenshot da página de conta de armazenamento Azure com Blobs selecionados](./media/commercial-marketplace-lead-management-instructions-azure-blob/select-blobs.png)

7. Uma vez na página blobs, selecione o botão **+ Recipiente.**

8. Escreva um **nome** para o seu novo recipiente. O nome do contentor tem estar em minúsculas, tem de começar com uma letra ou um número e só pode incluir letras, números e o caráter de travessão (-). Para obter mais informações sobre os nomes dos recipientes e blobs, consulte [o nome e os recipientes de referência, bolhas e metadados.](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)

    Guarde este valor pois este é o valor *do Nome do Contentor* que precisa de fornecer no portal de publicação para receber pistas para a sua oferta de mercado.

9. Desavista o nível de acesso público ao contentor como **Privado (sem acesso anónimo)**.

10. Selecione **OK** para criar o contentor.

    ![Novo Recipiente](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-azure-blob-storage"></a>Configure a sua oferta de envio leva ao armazenamento da Azure Blob

Quando estiver pronto para configurar as informações de gestão de chumbo para a sua oferta no portal de publicação, siga os passos abaixo:

1. Navegue na página **de configuração da Oferta** para a sua oferta.
2. Na secção **de comandos do Cliente,** selecione **Connect**.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-azure-blob/customer-leads.png" alt-text="Ligações ao cliente":::

3. Na janela pop-up de detalhes da Ligação, selecione **Azure Blob** para o Destino Principal.

    ![Conecte detalhes](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. Forneça o nome do **recipiente** e **o fio de ligação à conta** de armazenamento que obteve ao seguir estas instruções.

    * Exemplo do nome do recipiente: `marketplaceleadcontainer`
    * Exemplo de cadeia de ligação de conta de armazenamento: `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net` ![ Detalhe de conexão](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. Selecione **Guardar**.

    > [!NOTE]
    > Tem de configurar o resto da oferta e publicá-la antes de poder receber pistas para a oferta.