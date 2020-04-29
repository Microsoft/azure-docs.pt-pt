---
title: Criar cofres de serviços de recuperação
description: Neste artigo, aprenda a criar cofres de Serviços de Recuperação que armazenem os backups e pontos de recuperação.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 439f102e8f13bff63ab388be8f10df07ab2dc7d2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80672853"
---
# <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

Um cofre de Serviços de Recuperação é uma entidade que armazena os backups e pontos de recuperação criados ao longo do tempo. O cofre dos Serviços de Recuperação também contém as políticas de backup que estão associadas às máquinas virtuais protegidas.

Para criar um cofre dos Serviços de Recuperação:

1. Inscreva-se na sua subscrição no [portal Azure.](https://portal.azure.com/)

2. No menu esquerdo, selecione **Todos os serviços.**

    ![Selecione Todos os serviços](./media/backup-create-rs-vault/click-all-services.png)

3. Na caixa de diálogo **de todos os serviços,** insira Serviços de **Recuperação.** A lista de filtros de recursos de acordo com a sua entrada. Na lista de recursos, selecione cofres de Serviços de **Recuperação.**

    ![Insira e escolha cofres de Serviços de Recuperação](./media/backup-create-rs-vault/all-services.png)

    A lista de cofres dos Serviços de Recuperação na subscrição aparece.

4. No painel de **abóbadas** dos Serviços de Recuperação, selecione **Adicionar**.

    ![Adicione um cofre de Serviços de Recuperação](./media/backup-create-rs-vault/add-button-create-vault.png)

    A caixa de diálogo do **cofre dos Serviços de Recuperação** abre. Forneça valores para o **nome,** **subscrição,** **grupo de recursos**e **localização.**

    ![Configure o cofre dos Serviços de Recuperação](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Nome**: Introduza um nome amigável para identificar o cofre. O nome deve ser exclusivo da subscrição azure. Especifique um nome que tenha pelo menos dois, mas não mais de 50 caracteres. O nome deve começar com uma letra e consistir apenas em letras, números e hífenes.
   - **Subscrição**: Escolha a subscrição para utilizar. Se for membro de apenas uma subscrição, verá esse nome. Se não tiver a certeza de qual subscrição utilizar, utilize a subscrição predefinida (sugerida). Existem várias opções apenas se o seu trabalho ou conta escolar estiver associado a mais de uma subscrição Azure.
   - **Grupo de recursos**: Utilize um grupo de recursos existente ou crie um novo. Para ver a lista de grupos de recursos disponíveis na sua subscrição, selecione **Use existente**, e, em seguida, selecione um recurso da caixa de lista saltada. Para criar um novo grupo de recursos, selecione **Criar novo** e insira o nome. Para obter informações completas sobre grupos de recursos, consulte a [visão geral do Gestor de Recursos do Azure.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)
   - **Localização**: Selecione a região geográfica para o cofre. Para criar um cofre para proteger máquinas virtuais, o cofre **deve** estar na mesma região que as máquinas virtuais.

      > [!IMPORTANT]
      > Se não tiver a certeza da localização do seu VM, feche a caixa de diálogo. Vá à lista de máquinas virtuais no portal. Se tiver máquinas virtuais em várias regiões, crie um cofre de Serviços de Recuperação em cada região. Crie o cofre no primeiro local, antes de criar o cofre para outro local. Não há necessidade de especificar contas de armazenamento para armazenar os dados de reserva. O cofre dos Serviços de Recuperação e o cabo de serviço de backup Azure que automaticamente.
      >
      >

5. Quando estiver pronto para criar o cofre dos Serviços de Recuperação, selecione **Criar**.

    ![Criar o cofre dos Serviços de Recuperação](./media/backup-create-rs-vault/click-create-button.png)

    Pode demorar um pouco para criar o cofre dos Serviços de Recuperação. Monitorize as notificações de estado na área de **Notificações** no canto superior direito do portal. Depois do seu cofre ser criado, é visível na lista de cofres dos Serviços de Recuperação. Se não vir o seu cofre, selecione **Refresh**.

     ![Atualizar a lista de cofres de reserva](./media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-redundancy"></a>Definir redundância de armazenamento

A Azure Backup trata automaticamente do armazenamento do cofre. Tens de especificar como esse armazenamento é replicado.

1. No painel **Cofres dos Serviços de Recuperação**, clique em novo cofre. Na secção **Definições,** clique em **Propriedades**.
2. Em **Propriedades**, sob **configuração de backup,** clique em **Atualizar**.

3. Selecione o tipo de replicação de armazenamento e clique em **Guardar**.

     ![Definir a configuração de armazenamento do novo cofre](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

   - Recomendamos que, se estiver a usar o Azure como ponto final de armazenamento de reserva primário, continue a utilizar a definição **geo-redundante** padrão.
   - Se não utilizar o Azure como um ponto final do armazenamento de cópia de segurança primário, escolha **Localmente redundante**, o que reduz os custos de armazenamento do Azure.
   - Saiba mais sobre [geo e](../storage/common/storage-redundancy-grs.md) redundância [local.](../storage/common/storage-redundancy-lrs.md)

> [!NOTE]
> Alterar o tipo de **replicação** de armazenamento (localmente redundante/ Geo-redundante) para um cofre de serviços de recuperação tem de ser feito antes de configurar cópias de segurança no cofre. Uma vez configurada a cópia de segurança, a opção de modificar é desativada e não pode alterar o tipo de **Replicação**de Armazenamento .

## <a name="set-cross-region-restore"></a>Definir a região transversal restaurar

Como uma das opções de restauro, cross region Restore (CRR) permite restaurar Os VMs Azure em uma região secundária, que é uma [região de pares Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Esta opção permite-lhe:

- realizar exercícios quando há uma auditoria ou requisito de conformidade
- restaurar o VM ou o seu disco se houver um desastre na região primária.

Para escolher esta funcionalidade, **selecione Enable Cross Region Restaurar** a partir da lâmina de **configuração de backup.**

Para este processo, existem implicações de preços como está ao nível do armazenamento.

>[!NOTE]
>Antes de começar:
>
>- Reveja a [matriz](backup-support-matrix.md#cross-region-restore) de suporte para uma lista de tipos e regiões geridos apoiados.
>- A funcionalidade Cross Region Restore (CRR) é agora pré-visualizada em todas as regiões públicas do Azure.
>- CRR é uma função de opt-in de nível de cofre para qualquer cofre GRS (desligado por padrão).
>- Utilize o seguinte comando para embarcar na sua subscrição para esta funcionalidade:<br>
>  `Register-AzProviderFeature -FeatureName CrossRegionRestore -ProviderNamespace Microsoft.RecoveryServices`
>- Se estiver a bordo desta funcionalidade durante a pré-visualização limitada do público, o e-mail de aprovação de revisão incluirá detalhes da política de preços.
>- Após a adpção, pode demorar até 48 horas para que os itens de reserva estejam disponíveis nas regiões secundárias.
>- Atualmente, o CRR é suportado apenas para o Tipo de Gestão de Backup - ARM Azure VM (o clássico Azure VM não será suportado).  Quando os tipos de gestão adicionais suportam o CRR, então serão **automaticamente** matriculados.

### <a name="configure-cross-region-restore"></a>Configurar restauro da região transversal

Um cofre criado com redundância GRS inclui a opção de configurar a funcionalidade Cross Region Restore. Cada cofre GRS terá um banner, que se ligará à documentação. Para configurar o CRR para o cofre, vá à lâmina de configuração de backup, que contém a opção de ativar esta funcionalidade.

 ![Banner de configuração de backup](./media/backup-azure-arm-restore-vms/banner.png)

1. A partir do portal, vá ao cofre de serviços de recuperação > Definições > Propriedades.
2. Clique em ativar a **região transversal Restaurar neste cofre** para ativar a funcionalidade.

   ![Antes de clicar em Enable Cross Region restaurar neste cofre](./media/backup-azure-arm-restore-vms/backup-configuration1.png)

   ![Depois de clicar em Enable Cross Region restaurar neste cofre](./media/backup-azure-arm-restore-vms/backup-configuration2.png)

Saiba [como ver itens de backup na região secundária](backup-azure-arm-restore-vms.md#view-backup-items-in-secondary-region).

Aprenda a [restaurar na região secundária.](backup-azure-arm-restore-vms.md#restore-in-secondary-region)

Saiba como monitorizar a [região secundária restaurar postos](backup-azure-arm-restore-vms.md#monitoring-secondary-region-restore-jobs)de trabalho.

## <a name="modifying-default-settings"></a>Modificação das definições predefinidas

Recomendamos vivamente que reveja as definições predefinidas para o tipo de **replicação** de armazenamento e **as definições** de segurança antes de configurar as cópias de segurança no cofre.

- **O tipo** de replicação de armazenamento por padrão está definido para **Geo-redundante**. Uma vez configurado a cópia de segurança, a opção de modificar é desativada. Siga estes [passos](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy) para rever e modificar as definições.

- **O soft delete** por padrão é **ativado** em cofres recém-criados para proteger os dados de backup de eliminações acidentais ou maliciosas. Siga estes [passos](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#disabling-soft-delete) para rever e modificar as definições.

## <a name="next-steps"></a>Passos seguintes

[Saiba mais sobre](backup-azure-recovery-services-vault-overview.md) Cofres dos Serviços de Recuperação.
[Saiba mais sobre](backup-azure-delete-vault.md) Eliminar cofres dos Serviços de Recuperação.
