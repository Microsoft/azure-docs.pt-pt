---
title: 'Cópia de segurança do Azure: Crie cofres dos serviços de recuperação'
description: Criar cofres de serviços de recuperação que armazena as cópias de segurança e os pontos de recuperação
services: backup
author: sogup
manager: vijayts
keywords: Cofre dos serviços de recuperação; Cópia de segurança VM do Azure; Restauro de VMS do Azure;
ms.service: backup
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: sogup
ms.openlocfilehash: b345f5e7f6f32d7fbb76d4f0227c8f4f925ff884
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66400300"
---
# <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

Um cofre dos serviços de recuperação é uma entidade que armazena as cópias de segurança e os pontos de recuperação criados ao longo do tempo. O Cofre dos serviços de recuperação também contém as políticas de cópia de segurança que estão associadas a máquinas virtuais protegidas.

Para criar um cofre dos Serviços de Recuperação:

1. Inicie sessão na sua subscrição no [portal do Azure](https://portal.azure.com/).

2. No menu da esquerda, selecione **todos os serviços**.

    ![Selecione todos os serviços](./media/backup-create-rs-vault/click-all-services.png)

3. Na **todos os serviços** caixa de diálogo, introduza **dos serviços de recuperação**. A lista de filtros de recursos, de acordo com os seus comentários. Na lista de recursos, selecione **cofres dos serviços de recuperação**.

    ![Introduza e selecione os cofres dos serviços de recuperação](./media/backup-create-rs-vault/all-services.png)

    É apresentada a lista de cofres de serviços de recuperação na subscrição.

4. Sobre o **cofres dos serviços de recuperação** dashboard, selecione **Add**.

    ![Adicionar um cofre dos serviços de recuperação](./media/backup-create-rs-vault/add-button-create-vault.png)

    O **cofre dos serviços de recuperação** é aberta a caixa de diálogo. Fornecer valores para o **Name**, **subscrição**, **grupo de recursos**, e **localização**.

    ![Configurar o Cofre dos serviços de recuperação](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Nome**: Introduza um nome amigável para identificar o cofre. O nome tem de ser exclusivo para a subscrição do Azure. Especifique um nome que tem, pelo menos, dois, mas não mais de 50 carateres. O nome deve começar com uma letra e consistir apenas de letras, números e hífenes.
   - **Subscrição**: Escolha a subscrição a utilizar. Se for um membro de apenas uma subscrição, verá esse nome. Se não tiver a certeza de que subscrição utilizar, utilize a assinatura padrão (sugerida). Existem várias opções apenas se for seu trabalho ou a conta da instituição de ensino está associada a mais do que uma subscrição do Azure.
   - **Grupo de recursos**: Utilizar um grupo de recursos existente ou criar um novo. Para ver a lista de grupos de recursos disponíveis na sua subscrição, selecione **utilizar existente**e, em seguida, selecione um recurso a partir da caixa de lista pendente. Para criar um novo grupo de recursos, selecione **criar novo** e introduza o nome. Para obter informações completas sobre grupos de recursos, consulte [descrição geral do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
   - **Localização**: Selecione a região geográfica do cofre. Para criar um cofre para proteger máquinas virtuais, o Cofre **tem** ser na mesma região que as máquinas virtuais.

      > [!IMPORTANT]
      > Se não tiver a certeza de que a localização da sua VM, feche a caixa de diálogo. Vá para a lista de máquinas virtuais no portal. Se tiver máquinas virtuais em várias regiões, crie um cofre dos serviços de recuperação em cada região. Crie o Cofre na primeira localização antes de criar o cofre para outra localização. Não é necessário especificar contas de armazenamento para armazenar os dados de cópia de segurança. O Cofre dos serviços de recuperação e o serviço de cópia de segurança do Azure processam que automaticamente.
      >
      >

5. Quando estiver pronto para criar o Cofre dos serviços de recuperação, selecione **criar**.

    ![Criar o Cofre dos serviços de recuperação](./media/backup-create-rs-vault/click-create-button.png)

    Pode demorar algum tempo para criar o Cofre dos serviços de recuperação. Monitorize as notificações de estado na **notificações** área no canto superior direito do portal. Depois do cofre for criado, é visível na lista de cofres dos serviços de recuperação. Se não vir o cofre, selecione **atualizar**.

     ![Atualizar a lista de cofres de cópia de segurança](./media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-redundancy"></a>Redundância de armazenamento do conjunto

Armazenamento para o Cofre lida automaticamente com o Azure Backup. Tem de especificar a forma como os que o armazenamento é replicado.

1. No painel **Cofres dos Serviços de Recuperação**, clique em novo cofre. Sob o **configurações** secção, clique em **propriedades**.
2. Na **propriedades**, em **configuração de cópia de segurança**, clique em **atualização**.

3. Selecione o tipo de replicação de armazenamento e clique em **guardar**.

     ![Definir a configuração de armazenamento do novo cofre](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

   - Recomendamos que se estiver a utilizar o Azure como um ponto final de armazenamento de cópia de segurança primário, continuar a utilizar a predefinição **georredundante** definição.
   - Se não utilizar o Azure como um ponto final do armazenamento de cópia de segurança primário, escolha **Localmente redundante**, o que reduz os custos de armazenamento do Azure.
   - Saiba mais sobre [geo](../storage/common/storage-redundancy-grs.md) e [local](../storage/common/storage-redundancy-lrs.md) redundância.

> [!NOTE]
> Assim que escolher o **tipo de replicação de armazenamento** partir **georredundante** para **localmente redundante**, não é possível reverter novamente.

## <a name="next-steps"></a>Passos Seguintes

[Saiba mais sobre](backup-azure-recovery-services-vault-overview.md) cofres dos serviços de recuperação.
[Saiba mais sobre](backup-azure-delete-vault.md) cofres dos serviços de recuperação de eliminar.
