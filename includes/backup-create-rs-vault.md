---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: backup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: include
ms.date: 10/18/2018
ms.author: dacurwin
ms.custom: include file
ms.openlocfilehash: c111752dcca03781dfb2a11fb100dafc4bad90f7
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2020
ms.locfileid: "91401115"
---
## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

Um cofre dos Serviços de Recuperação é uma entidade de gestão que armazena pontos de recuperação criados ao longo do tempo e disponibiliza uma interface para realizar operações relacionadas com cópia de segurança. Estas incluem criar cópias de segurança a pedido, realizar restauros e criar políticas de cópia de segurança.

Para criar um cofre dos Serviços de Recuperação, siga estes passos.

1. Inicie sessão na sua subscrição no [portal do Azure](https://portal.azure.com/).

1. No menu esquerdo, selecione **Todos os serviços**.

    ![Selecionar Todos os serviços](./media/backup-create-rs-vault/click-all-services.png)

1. Na caixa de diálogo **Todos os serviços**, introduza *Serviços de Recuperação*. A lista de recursos é filtrada de acordo com a sua entrada. Na lista de recursos, selecione **Cofres dos Serviços de Recuperação**.

    ![Introduzir e escolher cofres dos Serviços de Recuperação](./media/backup-create-rs-vault/all-services.png)

    É apresentada a lista dos cofres dos Serviços de Recuperação na subscrição.

1. No dashboard **Cofres dos Serviços de Recuperação**, selecione **Adicionar**.

    ![Adicionar um cofre dos Serviços de Recuperação](./media/backup-create-rs-vault/add-button-create-vault.png)

    A caixa de diálogo **Cofre dos Serviços de Recuperação** abre-se. Introduza os valores para o **Nome**, a **Subscrição**, o **Grupo de recursos** e a **Localização**.

    ![Configurar o cofre dos Serviços de Recuperação](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Name** (Nome): Introduza um nome amigável para identificar o cofre. O nome tem de ser exclusivo para a subscrição do Azure. Especifique um nome com um mínimo de 2 carateres e um máximo de 50 carateres. O nome tem de começar com uma letra e ser composto apenas por letras, números e hífenes.
   - **Subscrição**: Escolha a subscrição a utilizar. Se for membro de apenas uma subscrição, vai ver esse nome. Se não tiver a certeza de que subscrição utilizar, utilize a subscrição predefinida (sugerida). Terá várias escolhas apenas se a sua conta escolar ou profissional estiver associada a mais do que uma subscrição do Azure.
   - **Grupo de recursos**: Utilize um grupo de recursos existente ou crie um novo. Para ver a lista de grupos de recursos disponíveis na sua subscrição, selecione **Utilizar existente** e, em seguida, selecione um recurso da lista pendente. Para criar um novo grupo de recursos, selecione **Criar novo** e introduza o nome. Para mais informações sobre grupos de recursos, veja [Descrição Geral do Azure Resource Manager](../articles/azure-resource-manager/management/overview.md).
   - **Localização**: Selecione a região geográfica do cofre. Para criar um cofre para proteger qualquer origem de dados, o cofre *tem* de estar na mesma região que a origem de dados.

      > [!IMPORTANT]
      > Se não tiver a certeza da localização da origem de dados, feche a caixa de diálogo. Aceda à lista dos seus recursos no portal. Se tiver origens de dados em várias regiões, crie um cofre dos Serviços de Recuperação para cada uma. Crie o cofre na primeira localização antes de o criar para outra localização. Não é necessário especificar contas de armazenamento para armazenar os dados da cópia de segurança. O cofre dos Serviços de Recuperação e o Azure Backup gerem esse processo automaticamente.
      >
      >

1. Depois de introduzir os valores, selecione **Rever + criar**.

    ![Screenshot que mostra o botão Review + criar no processo de abóbada criar um processo de abóbada de Serviços de Recuperação.](./media/backup-create-rs-vault/review-and-create.png)

1. Quando estiver pronto para criar o cofre dos Serviços de Recuperação, selecione **Criar**.

    ![Criar o cofre dos Serviços de Recuperação](./media/backup-create-rs-vault/click-create-button.png)

    Pode demorar algum tempo a criar o cofre dos Serviços de Recuperação. Monitorize as notificações de estado na área **Notificações** no canto superior direito do portal. Depois de o cofre ser criado, é visível na lista de cofres dos Serviços de Recuperação. Se não vir o seu cofre, selecione **Atualizar**.

     ![Atualizar a lista de cofres de cópia de segurança](./media/backup-create-rs-vault/refresh-button.png)

>[!IMPORTANT]
> É altamente recomendável rever as predefinições do **Tipo de Replicação de Armazenamento** e as **Definições de segurança** antes de configurar as cópias de segurança no cofre. Para obter mais informações, veja a seção [Definir a Redundância de armazenamento](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy).
