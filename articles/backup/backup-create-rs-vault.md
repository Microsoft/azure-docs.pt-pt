---
title: Criar cofres dos Serviços de Recuperação
description: Neste artigo, aprenda a criar cofres dos Serviços de Recuperação que armazenam os backups e pontos de recuperação.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: ac30a57dcbceb4616961c9d100e6ac8639ef2172
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85391065"
---
# <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

Um cofre dos Serviços de Recuperação é uma entidade que armazena os backups e pontos de recuperação criados ao longo do tempo. O cofre dos Serviços de Recuperação também contém as políticas de backup que estão associadas às máquinas virtuais protegidas.

Para criar um cofre dos Serviços de Recuperação:

1. Inscreva-se na sua subscrição no [portal Azure.](https://portal.azure.com/)

2. No menu à esquerda, selecione **Todos os serviços**.

    ![Selecione Todos os serviços](./media/backup-create-rs-vault/click-all-services.png)

3. Na caixa de diálogo **de todos os serviços,** insira **os Serviços de Recuperação.** A lista de filtros de recursos de acordo com a sua entrada. Na lista de recursos, selecione **Cofres dos Serviços de Recuperação.**

    ![Insira e escolha cofres dos Serviços de Recuperação](./media/backup-create-rs-vault/all-services.png)

    A lista de cofres dos Serviços de Recuperação na subscrição aparece.

4. No painel de **cofres dos Serviços de Recuperação,** selecione **Add**.

    ![Adicione um cofre dos Serviços de Recuperação](./media/backup-create-rs-vault/add-button-create-vault.png)

    A caixa de diálogo do **cofre dos Serviços de Recuperação** abre. Fornecer valores para o **Nome,** **Assinatura,** **Grupo de Recursos**e **Localização.**

    ![Configure o cofre dos Serviços de Recuperação](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Nome**: Introduza um nome amigável para identificar o cofre. O nome deve ser exclusivo da assinatura Azure. Especifique um nome que tenha pelo menos dois, mas não mais de 50 caracteres. O nome deve começar com uma letra e consistir apenas em letras, números e hífenes.
   - **Assinatura**: Escolha a subscrição para utilizar. Se for membro de apenas uma subscrição, verá esse nome. Se não tiver a certeza de qual subscrição utilizar, utilize a subscrição padrão (sugerida). Só existem múltiplas escolhas se o seu trabalho ou conta escolar estiver associado a mais de uma subscrição do Azure.
   - **Grupo de recursos**: Utilize um grupo de recursos existente ou crie um novo. Para ver a lista de grupos de recursos disponíveis na sua subscrição, selecione **Use existente**e, em seguida, selecione um recurso da caixa de listas drop-down. Para criar um novo grupo de recursos, selecione **Criar novo** e insira o nome. Para obter informações completas sobre grupos de recursos, consulte [a visão geral do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
   - **Localização**: Selecione a região geográfica para o cofre. Para criar um cofre para proteger máquinas virtuais, o cofre **deve** estar na mesma região que as máquinas virtuais.

      > [!IMPORTANT]
      > Se não tiver a certeza da localização do seu VM, feche a caixa de diálogo. Vá à lista de máquinas virtuais no portal. Se tiver máquinas virtuais em várias regiões, crie um cofre dos Serviços de Recuperação em cada região. Crie o cofre no primeiro local, antes de criar o cofre para outro local. Não há necessidade de especificar contas de armazenamento para armazenar os dados de reserva. O cofre dos Serviços de Recuperação e o serviço de backup Azure tratam-no automaticamente.
      >
      >

5. Quando estiver pronto para criar o cofre dos Serviços de Recuperação, selecione **Criar**.

    ![Criar o cofre dos Serviços de Recuperação](./media/backup-create-rs-vault/click-create-button.png)

    Pode levar algum tempo a criar o cofre dos Serviços de Recuperação. Monitorize as notificações de estado na área **de Notificações** no canto superior direito do portal. Depois do seu cofre ser criado, está visível na lista de cofres dos Serviços de Recuperação. Se não vir o seu cofre, selecione **Refresh**.

     ![Refresque a lista de cofres de reserva](./media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-redundancy"></a>Definir redundância de armazenamento

O Azure Backup manuseia automaticamente o armazenamento para o cofre. Tem de especificar como é que o armazenamento é replicado.

>[!NOTE]
>As definições de replicação de armazenamento para o cofre não são relevantes para a cópia de segurança da partilha de ficheiros Azure, uma vez que a solução atual é baseada em instantâneos e não há dados transferidos para o cofre. As imagens são armazenadas na mesma conta de armazenamento que a parte de ficheiros com reserva.

1. No painel **Cofres dos Serviços de Recuperação**, clique em novo cofre. Na secção **Definições,** clique em **Propriedades**.
2. Em **Propriedades**, em **Configuração de Cópia de Segurança,** clique em **Atualização**.

3. Selecione o tipo de replicação de armazenamento e clique em **Guardar**.

     ![Definir a configuração de armazenamento do novo cofre](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

   - Recomendamos que, se estiver a utilizar o Azure como ponto final de armazenamento de backup primário, continue a utilizar a definição **geo-redundante** padrão.
   - Se não utilizar o Azure como um ponto final do armazenamento de cópia de segurança primário, escolha **Localmente redundante**, o que reduz os custos de armazenamento do Azure.
   - Saiba mais sobre [a redundância geo](../storage/common/storage-redundancy-grs.md) e [local.](../storage/common/storage-redundancy-lrs.md)

> [!NOTE]
> Alterar o tipo de **replicação** de armazenamento (localmente redundante/ geo-redundante) para um cofre de serviços de recuperação tem de ser feito antes de configurar cópias de segurança no cofre. Uma vez configurada cópia de segurança, a opção para modificar é desativada e não pode alterar o **tipo de replicação de armazenamento**.

## <a name="set-cross-region-restore"></a>Definir restauro da região de cruz

Como uma das opções de restauro, o Cross Region Restore (CRR) permite restaurar os VMs Azure numa região secundária, que é uma [região emparelhada Azure.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) Esta opção permite-lhe:

- realizar exercícios quando há uma auditoria ou requisito de conformidade
- restaurar o VM ou o seu disco se houver um desastre na região primária.

Para escolher esta função, selecione **Ative Cross Region Restore** a partir da lâmina de **configuração de backup.**

Para este processo, existem implicações nos preços tal como está ao nível do armazenamento.

>[!NOTE]
>Antes de começar:
>
>- Reveja a matriz de [apoio](backup-support-matrix.md#cross-region-restore) para uma lista de tipos e regiões geridos apoiados.
>- A funcionalidade Cross Region Restore (CRR) está agora pré-visualizada em todas as regiões públicas do Azure.
>- CRR é uma função de opt-in de nível de abóbada para qualquer cofre GRS (desligado por defeito).
>- Depois de optar, pode levar até 48 horas para que os itens de reserva estejam disponíveis em regiões secundárias.
>- Atualmente, a CRR é suportada apenas para o Tipo de Gestão de Backup - ARM Azure VM (o clássico Azure VM não será suportado).  Quando os tipos de gestão adicionais suportam o CRR, então serão **automaticamente** matriculados.
>- A Cross Region Restore não pode ser revertida para GRS ou LRS uma vez que a proteção é iniciada pela primeira vez. 

### <a name="configure-cross-region-restore"></a>Conigure Região Cruzada Restaurar

Um cofre criado com redundância GRS inclui a opção de configurar a funcionalidade Cross Region Restore. Cada cofre GRS terá um banner, que ligará à documentação. Para configurar o CRR para o cofre, vá à lâmina de configuração de reserva, que contém a opção para ativar esta função.

 ![Banner de configuração de backup](./media/backup-azure-arm-restore-vms/banner.png)

1. A partir do portal, vá para o cofre dos Serviços de Recuperação > Definições > Propriedades.
2. Clique **em Ativar a Região Cruzada Restaurar neste cofre** para ativar a funcionalidade.

   ![Antes de clicar em Enable Cross Region restaurar neste cofre](./media/backup-azure-arm-restore-vms/backup-configuration1.png)

   ![Depois de clicar em Enable Cross Region restaurar neste cofre](./media/backup-azure-arm-restore-vms/backup-configuration2.png)

Saiba como [ver itens de backup na região secundária.](backup-azure-arm-restore-vms.md#view-backup-items-in-secondary-region)

Saiba como [restaurar na região secundária.](backup-azure-arm-restore-vms.md#restore-in-secondary-region)

Saiba como monitorizar a [região secundária restaurar os postos de trabalho.](backup-azure-arm-restore-vms.md#monitoring-secondary-region-restore-jobs)

## <a name="modifying-default-settings"></a>Modificar as definições predefinidos

Recomendamos vivamente que reveja as definições predefinidos para **o tipo de replicação de armazenamento** e as **definições de segurança** antes de configurar cópias de segurança no cofre.

- **O tipo de replicação de armazenamento** por padrão é definido para **Geo-redundante**. Uma vez configurada a cópia de segurança, a opção de modificação é desativada. Siga estes [passos](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy) para rever e modificar as definições.

- **A eliminação suave** por padrão está **ativada** em cofres recém-criados para proteger os dados de cópias de segurança de eliminações acidentais ou maliciosas. Siga estes [passos](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#enabling-and-disabling-soft-delete) para rever e modificar as definições.

## <a name="next-steps"></a>Passos seguintes

[Saiba mais](backup-azure-recovery-services-vault-overview.md) Cofres dos Serviços de Recuperação.
[Saiba mais](backup-azure-delete-vault.md) Apagar cofres dos Serviços de Recuperação.
