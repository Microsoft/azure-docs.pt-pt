---
title: Tabela do Azure no programa comercial do Marketplace | Azure Marketplace
description: Configurar gerenciamento de Lead para blob do Azure
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 7/30/2019
ms.author: evansma
ms.openlocfilehash: c67855422808f5ec4c81242edcece4e447f2f44f
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69902367"
---
# <a name="lead-management-instructions-for-azure-blob"></a>Instruções de gerenciamento de Lead para blob do Azure

>[!Caution]
>A opção de blob do Azure para processar os leads da oferta do Marketplace foi preterida. Se, no momento, você tiver uma oferta publicada com a configuração de gerenciamento de leads para o blob do Azure, você não estará mais recebendo leads do cliente. Atualize sua configuração de gerenciamento de leads para qualquer uma das outras opções de gerenciamento de Lead. Saiba mais sobre as outras opções na [página inicial de gerenciamento](./commercial-marketplace-get-customer-leads.md)de leads.

Se o seu sistema de gerenciamento de relacionamento com o cliente (CRM) não tiver suporte explícito no Partner Center para receber leads do Azure Marketplace e do AppSource, você poderá usar um blob do Azure para lidar com esses leads. Você pode optar por exportar os dados e importá-los para o sistema CRM. As instruções neste artigo fornecerão o processo de criação de uma conta de armazenamento do Azure e um blob do Azure nessa conta. Além disso, você pode criar um novo fluxo usando Microsoft Flow para enviar uma notificação por email quando sua oferta receber um cliente potencial.


## <a name="how-to-configure-azure-blob"></a>Como configurar o blob do Azure

1. Se você não tiver uma conta do Azure, poderá [criar uma conta de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
1. Depois que sua conta do Azure estiver ativa, entre no [portal do Azure](https://portal.azure.com).
1. No portal do Azure, crie uma conta de armazenamento usando o procedimento a seguir.  
    1. Selecione **+ criar um recurso** na barra de menus à esquerda.  O **novo** painel (folha) será exibido à direita.
    2. Selecione **armazenamento** no painel **novo** .  Uma lista em **destaque** é exibida à direita.
    3. Selecione a **conta de armazenamento** para iniciar a criação da conta.  Siga as instruções no artigo [criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

    ![Etapas para criar uma conta de armazenamento do Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    Para obter mais informações sobre contas de armazenamento, selecione [tutorial de início rápido](https://docs.microsoft.com/azure/storage/).  Para obter mais informações sobre preços de armazenamento, consulte [preços de armazenamento](https://azure.microsoft.com/pricing/details/storage/).

4. Aguarde até que sua conta de armazenamento seja provisionada, um processo que normalmente leva alguns minutos.  Em seguida, acesse sua conta de armazenamento na **Home** page do portal do Azure selecionando **Ver todos os seus recursos** ou selecionando **todos os recursos** na menu de navegação à esquerda da portal do Azure.

    ![Acessar sua conta de armazenamento do Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-access.png)

5. No painel da sua conta de armazenamento, selecione **chaves de acesso** e copie o valor da *cadeia de conexão* para a chave. Salve esse valor, pois esse é o valor da *cadeia de conexão da conta de armazenamento* que você precisará fornecer no portal de publicação para receber clientes potenciais para sua oferta do Marketplace.

     Um exemplo de um Stinger de conexão é:

     ```sql
     DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
     ```

    ![Chave de armazenamento do Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-keys-2.png)

6. Na página da sua conta de armazenamento, selecione BLOBs.

   ![Chave de armazenamento do Azure](./media/commercial-marketplace-lead-management-instructions-azure-blob/select-blobs.png)

7. Uma vez na página BLOBs, selecione o botão **+ contêiner** .

8. Digite um **nome** para o novo contêiner. O nome do contentor tem estar em minúsculas, tem de começar com uma letra ou um número e só pode incluir letras, números e o caráter de travessão (-). Para obter mais informações sobre os nomes dos contentores e dos blobs, veja [Nomenclatura e referência para contentores, blobs e metadados](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

    Salve esse valor, pois esse é o valor do *nome do contêiner* que você precisa fornecer no portal de publicação para receber clientes potenciais para sua oferta do Marketplace.

9. Defina o nível de acesso público para o contêiner como **privado (sem acesso anônimo)** .

10. Selecione **OK** para criar o contentor.

    ![Novo contêiner](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-blob"></a>Configurar sua oferta para enviar clientes potenciais ao blob do Azure

Quando você estiver pronto para configurar as informações de gerenciamento de Lead para sua oferta no portal de publicação, siga as etapas abaixo:

1. Navegue até a página de **instalação da oferta** para sua oferta.
2. Selecione **conectar** na seção Gerenciamento de leads.

    ![Conectar oferta](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-offer.png)

3. Na janela pop-up detalhes da conexão, selecione **blob do Azure** para o destino do cliente potencial.

    ![Detalhes do Connect](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. Forneça o **nome do contêiner** e a **cadeia de conexão da conta de armazenamento** obtida seguindo estas instruções.

    * Exemplo de nome do contêiner:`marketplaceleadcontainer`
    * Exemplo de cadeia de conexão da conta de armazenamento: `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net`![Detalhes da conexão](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. Selecione **Guardar**.

    > [!NOTE]
    > Você deve concluir a configuração do restante da oferta e publicá-la antes de receber clientes potenciais para a oferta.


