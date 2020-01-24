---
title: Criar cofres dos serviços de recuperação
description: Neste artigo, saiba como criar cofres dos serviços de recuperação que armazenam os backups e os pontos de recuperação.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 682bf26c1485bd9e2ae288a6947e9ad573463069
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705467"
---
# <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

Um cofre dos serviços de recuperação é uma entidade que armazena os backups e os pontos de recuperação criados ao longo do tempo. O cofre dos serviços de recuperação também contém as políticas de backup associadas às máquinas virtuais protegidas.

Para criar um cofre dos Serviços de Recuperação:

1. Entre em sua assinatura no [portal do Azure](https://portal.azure.com/).

2. No menu à esquerda, selecione **todos os serviços**.

    ![selecionar todos os serviços](./media/backup-create-rs-vault/click-all-services.png)

3. Na caixa de diálogo **todos os serviços** , insira **serviços de recuperação**. A lista de recursos filtra de acordo com sua entrada. Na lista de recursos, selecione **cofres dos serviços de recuperação**.

    ![Insira e escolha os cofres dos serviços de recuperação](./media/backup-create-rs-vault/all-services.png)

    A lista de cofres dos serviços de recuperação na assinatura é exibida.

4. No painel **cofres dos serviços de recuperação** , selecione **Adicionar**.

    ![Adicionar um cofre dos serviços de recuperação](./media/backup-create-rs-vault/add-button-create-vault.png)

    A caixa de diálogo **cofre dos serviços de recuperação** é aberta. Forneça valores para o **nome**, a **assinatura**, o **grupo de recursos**e o **local**.

    ![Configurar o cofre dos serviços de recuperação](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Nome**: Insira um nome amigável para identificar o cofre. O nome deve ser exclusivo para a assinatura do Azure. Especifique um nome que tenha pelo menos dois, mas não mais de 50 caracteres. O nome deve começar com uma letra e consistir apenas em letras, números e hifens.
   - **Assinatura**: escolha a assinatura a ser usada. Se você for membro de apenas uma assinatura, verá esse nome. Se você não tiver certeza de qual assinatura usar, use a assinatura padrão (sugerida). Há várias opções somente se sua conta corporativa ou de estudante estiver associada a mais de uma assinatura do Azure.
   - **Grupo de recursos**: Use um grupo de recursos existente ou crie um novo. Para ver a lista de grupos de recursos disponíveis em sua assinatura, selecione **usar existente**e, em seguida, selecione um recurso na caixa de listagem suspensa. Para criar um novo grupo de recursos, selecione **criar novo** e insira o nome. Para obter informações completas sobre grupos de recursos, consulte [Azure Resource Manager visão geral](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
   - **Local**: selecione a região geográfica para o cofre. Para criar um cofre para proteger as máquinas virtuais, o cofre **deve** estar na mesma região que as máquinas virtuais.

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

## <a name="set-cross-region-restore"></a>Definir restauração entre regiões

Como uma das opções de restauração, a CRR (restauração entre regiões) permite que você restaure as VMs do Azure em uma região secundária, que é uma [região emparelhada do Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Essa opção permite que você:

- realizar análises quando houver um requisito de auditoria ou de conformidade
- Restaure a VM ou seu disco se houver um desastre na região primária.

Para escolher esse recurso, selecione **habilitar restauração entre regiões** na folha **configuração de backup** .

Para esse processo, há implicações de preço como ele está no nível de armazenamento.

>[!NOTE]
>Antes de começar:
>
>- Examine a [matriz de suporte](backup-support-matrix.md#cross-region-restore) para obter uma lista de tipos e regiões gerenciados com suporte.
>- O recurso de CRR (restauração entre regiões) está disponível no momento apenas na região WCUS.
>- A CRR é um recurso de consentimento de nível de cofre para qualquer cofre GRS (desativado por padrão).
>- Utilize o seguinte comando para embarcar na sua subscrição para esta funcionalidade:<br>
>  `Register-AzProviderFeature -FeatureName CrossRegionRestore -ProviderNamespace Microsoft.RecoveryServices`
>- Se você estiver integrado a esse recurso durante a visualização limitada pública, o email de aprovação de revisão incluirá detalhes da política de preços.
>- Depois de aceitar, pode levar até 48 horas para que os itens de backup estejam disponíveis em regiões secundárias.
>- Atualmente, o CRR tem suporte apenas para o tipo de gerenciamento de backup VM do Azure do ARM (a VM clássica do Azure não terá suporte).  Quando tipos de gerenciamento adicionais dão suporte à CRR, eles serão registrados **automaticamente** .

### <a name="configure-cross-region-restore"></a>Configurar a restauração entre regiões

Um cofre criado com redundância GRS inclui a opção de configurar o recurso de restauração entre regiões. Cada cofre do GRS terá um banner, que será vinculado à documentação. Para configurar a CRR para o cofre, vá para a folha configuração de backup, que contém a opção para habilitar esse recurso.

 ![Faixa de configuração de backup](./media/backup-azure-arm-restore-vms/banner.png)

1. No portal, acesse cofre dos serviços de recuperação > Configurações > Propriedades.
2. Clique em **habilitar a restauração entre regiões neste cofre** para habilitar a funcionalidade.

   ![Antes de clicar em habilitar a restauração entre regiões neste cofre](./media/backup-azure-arm-restore-vms/backup-configuration1.png)

   ![Depois de clicar em habilitar a restauração entre regiões neste cofre](./media/backup-azure-arm-restore-vms/backup-configuration2.png)

Saiba como [Exibir itens de backup na região secundária](backup-azure-arm-restore-vms.md#view-backup-items-in-secondary-region).

Saiba como [restaurar na região secundária](backup-azure-arm-restore-vms.md#restore-in-secondary-region).

Saiba como [monitorar trabalhos de restauração de região secundária](backup-azure-arm-restore-vms.md#monitoring-secondary-region-restore-jobs).

## <a name="modifying-default-settings"></a>Modificando configurações padrão

É altamente recomendável revisar as configurações padrão de **tipo de replicação de armazenamento** e configurações de **segurança** antes de configurar backups no cofre.

- O **tipo de replicação de armazenamento** por padrão é definido como com **redundância geográfica**. Depois de configurar o backup, a opção para modificar é desabilitada. Siga estas [etapas](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy) para examinar e modificar as configurações.

- A **exclusão reversível** por padrão é **habilitada** em cofres recém-criados para proteger dados de backup de exclusões acidentais ou mal-intencionadas. Siga estas [etapas](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#disabling-soft-delete) para examinar e modificar as configurações.

## <a name="next-steps"></a>Passos seguintes

[Saiba mais](backup-azure-recovery-services-vault-overview.md) Cofres dos serviços de recuperação.
[Saiba mais](backup-azure-delete-vault.md) Excluir cofres dos serviços de recuperação.
