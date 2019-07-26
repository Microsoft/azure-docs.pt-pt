---
title: 'Backup do Azure: Criar cofres dos serviços de recuperação'
description: Criando cofres dos serviços de recuperação que armazenam os backups e os pontos de recuperação
author: sogup
manager: vijayts
keywords: Cofre dos serviços de recuperação; Backup de VM do Azure; Restauração de VM do Azure;
ms.service: backup
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: sogup
ms.openlocfilehash: 95ffe6aa0f65690e2df71c94b87d3e9f1ac7f7e5
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465174"
---
# <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

Um cofre dos serviços de recuperação é uma entidade que armazena os backups e os pontos de recuperação criados ao longo do tempo. O cofre dos serviços de recuperação também contém as políticas de backup associadas às máquinas virtuais protegidas.

Para criar um cofre dos Serviços de Recuperação:

1. Entre em sua assinatura no [portal do Azure](https://portal.azure.com/).

2. No menu à esquerda, selecione **todos os serviços**.

    ![Selecionar todos os serviços](./media/backup-create-rs-vault/click-all-services.png)

3. Na caixa de diálogo **todos os serviços** , insira **serviços de recuperação**. A lista de recursos filtra de acordo com sua entrada. Na lista de recursos, selecione **cofres dos serviços de recuperação**.

    ![Insira e escolha os cofres dos serviços de recuperação](./media/backup-create-rs-vault/all-services.png)

    A lista de cofres dos serviços de recuperação na assinatura é exibida.

4. No painel **cofres dos serviços de recuperação** , selecione **Adicionar**.

    ![Adicionar um cofre dos serviços de recuperação](./media/backup-create-rs-vault/add-button-create-vault.png)

    A caixa de diálogo **cofre dos serviços de recuperação** é aberta. Forneça valores para o **nome**, a **assinatura**, o **grupo de recursos**e o **local**.

    ![Configurar o cofre dos serviços de recuperação](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Nome**: Insira um nome amigável para identificar o cofre. O nome deve ser exclusivo para a assinatura do Azure. Especifique um nome que tenha pelo menos dois, mas não mais de 50 caracteres. O nome deve começar com uma letra e consistir apenas em letras, números e hifens.
   - **Assinatura**: Escolha a assinatura a ser usada. Se você for membro de apenas uma assinatura, verá esse nome. Se você não tiver certeza de qual assinatura usar, use a assinatura padrão (sugerida). Há várias opções somente se sua conta corporativa ou de estudante estiver associada a mais de uma assinatura do Azure.
   - **Grupo de recursos**: Use um grupo de recursos existente ou crie um novo. Para ver a lista de grupos de recursos disponíveis em sua assinatura, selecione **usar existente**e, em seguida, selecione um recurso na caixa de listagem suspensa. Para criar um novo grupo de recursos, selecione **criar novo** e insira o nome. Para obter informações completas sobre grupos de recursos, consulte [Azure Resource Manager visão geral](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
   - **Local**: Selecione a região geográfica para o cofre. Para criar um cofre para proteger as máquinas virtuais, o cofre **deve** estar na mesma região que as máquinas virtuais.

      > [!IMPORTANT]
      > Se você não tiver certeza do local da VM, feche a caixa de diálogo. Vá para a lista de máquinas virtuais no Portal. Se você tiver máquinas virtuais em várias regiões, crie um cofre de serviços de recuperação em cada região. Crie o cofre no primeiro local antes de criar o cofre para outro local. Não é necessário especificar contas de armazenamento para armazenar os dados de backup. O cofre dos serviços de recuperação e o serviço de backup do Azure lidam com isso automaticamente.
      >
      >

5. Quando estiver pronto para criar o cofre dos serviços de recuperação, selecione **criar**.

    ![Criar o cofre dos serviços de recuperação](./media/backup-create-rs-vault/click-create-button.png)

    Pode levar algum tempo para criar o cofre dos serviços de recuperação. Monitore as notificações de status na área de **notificações** no canto superior direito do Portal. Depois que o cofre for criado, ele ficará visível na lista de cofres dos serviços de recuperação. Se você não vir seu cofre, selecione **Atualizar**.

     ![Atualizar a lista de cofres de backup](./media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-redundancy"></a>Definir redundância de armazenamento

O backup do Azure manipula automaticamente o armazenamento para o cofre. Você precisa especificar como esse armazenamento é replicado.

1. No painel **Cofres dos Serviços de Recuperação**, clique em novo cofre. Na seção **configurações** , clique em **Propriedades**.
2. Em **Propriedades**, em **configuração de backup**, clique em **Atualizar**.

3. Selecione o tipo de replicação de armazenamento e clique em **salvar**.

     ![Definir a configuração de armazenamento do novo cofre](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

   - Recomendamos que, se você estiver usando o Azure como um ponto de extremidade de armazenamento de backup primário, continue a usar a configuração padrão **com redundância geográfica** .
   - Se não utilizar o Azure como um ponto final do armazenamento de cópia de segurança primário, escolha **Localmente redundante**, o que reduz os custos de armazenamento do Azure.
   - Saiba mais sobre a redundância [geográfica](../storage/common/storage-redundancy-grs.md) e [local](../storage/common/storage-redundancy-lrs.md) .

> [!NOTE]
> A alteração do **tipo de replicação de armazenamento** (com redundância local/geograficamente) para um cofre dos serviços de recuperação deve ser feita antes de configurar backups no cofre. Depois de configurar o backup, a opção para modificar é desabilitada e você não pode alterar o **tipo de replicação de armazenamento**. 

## <a name="next-steps"></a>Passos Seguintes

[Saiba mais](backup-azure-recovery-services-vault-overview.md) Cofres dos serviços de recuperação.
[Saiba mais](backup-azure-delete-vault.md) Excluir cofres dos serviços de recuperação.
