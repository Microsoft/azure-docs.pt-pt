---
title: Criar e configurar cofres dos Serviços de Recuperação
description: Neste artigo, aprenda a criar e configurar cofres dos Serviços de Recuperação que armazenam os backups e pontos de recuperação.
ms.topic: conceptual
ms.date: 05/30/2019
ms.custom: references_regions
ms.openlocfilehash: 244562efdc4c274a79ea27cdfa00dd51ae671fa4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87032957"
---
# <a name="create-and-configure-a-recovery-services-vault"></a>Criar e configurar um cofre dos Serviços de Recuperação

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="set-storage-redundancy"></a>Definir redundância de armazenamento

O Azure Backup manuseia automaticamente o armazenamento para o cofre. Tem de especificar como é que o armazenamento é replicado.

> [!NOTE]
> Alterar **o tipo de replicação de armazenamento** (localmente redundante/ geo-redundante) para um cofre de serviços de recuperação tem de ser feito antes de configurar cópias de segurança no cofre. Uma vez configurada cópia de segurança, a opção de modificar é desativada.
>
>- Se ainda não configurar a cópia de segurança, [siga estes passos](#set-storage-redundancy) para rever e modificar as definições.
>- Se já configuraram o backup e têm de passar de GRS para LRS, então [revejam estas soluções alternativas.](#how-to-change-from-grs-to-lrs-after-configuring-backup)

1. No painel **Cofres dos Serviços de Recuperação**, clique em novo cofre. Na secção **Definições,** clique em **Propriedades**.
1. Em **Propriedades**, em **Configuração de Cópia de Segurança,** clique em **Atualização**.

1. Selecione o tipo de replicação de armazenamento e clique em **Guardar**.

     ![Definir a configuração de armazenamento do novo cofre](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

   - Recomendamos que, se estiver a utilizar o Azure como ponto final de armazenamento de backup primário, continue a utilizar a definição **geo-redundante** padrão.
   - Se não utilizar o Azure como um ponto final do armazenamento de cópia de segurança primário, escolha **Localmente redundante**, o que reduz os custos de armazenamento do Azure.
   - Saiba mais sobre [a redundância geo](../storage/common/storage-redundancy.md) e [local.](../storage/common/storage-redundancy.md)

>[!NOTE]
>As definições de replicação de armazenamento para o cofre não são relevantes para a cópia de segurança da partilha de ficheiros Azure, uma vez que a solução atual é baseada em instantâneos e não há dados transferidos para o cofre. As imagens são armazenadas na mesma conta de armazenamento que a parte de ficheiros com reserva.

## <a name="set-cross-region-restore"></a>Definir restauro da região de cruz

Como uma das opções de restauro, o Cross Region Restore (CRR) permite restaurar os VMs Azure numa região secundária, que é uma [região emparelhada Azure.](../best-practices-availability-paired-regions.md) Esta opção permite-lhe:

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

- **O tipo de replicação de armazenamento** por padrão é definido para **Geo-redundante** (GRS). Uma vez configurada a cópia de segurança, a opção de modificação é desativada.
  - Se ainda não configurar a cópia de segurança, [siga estes passos](#set-storage-redundancy) para rever e modificar as definições.
  - Se já configuraram o backup e têm de passar de GRS para LRS, então [revejam estas soluções alternativas.](#how-to-change-from-grs-to-lrs-after-configuring-backup)

- **A eliminação suave** por padrão está **ativada** em cofres recém-criados para proteger os dados de cópias de segurança de eliminações acidentais ou maliciosas. [Siga estes passos](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) para rever e modificar as definições.

### <a name="how-to-change-from-grs-to-lrs-after-configuring-backup"></a>Como mudar de GRS para LRS após configurar backup

Antes de decidir passar de GRS para armazenamento localmente redundante (LRS), reveja as compensações entre custos mais baixos e maior durabilidade de dados que se adequam ao seu cenário. Se tens de passar de GRS para LRS, então tens duas opções. Dependem dos requisitos do seu negócio para reter os dados de backup:

- [Não precisa preservar dados anteriores de back-up](#dont-need-to-preserve-previous-backed-up-data)
- [Deve preservar dados anteriores de back-up](#must-preserve-previous-backed-up-data)

#### <a name="dont-need-to-preserve-previous-backed-up-data"></a>Não precisa preservar dados anteriores de back-up

Para proteger as cargas de trabalho num novo cofre LRS, a proteção atual e os dados terão de ser eliminados no cofre GRS e cópias de segurança configuradas novamente.

>[!WARNING]
>A seguinte operação é destrutiva e não pode ser desfeita. Todos os dados de backup e itens de backup associados ao servidor protegido serão permanentemente eliminados. Avance com cuidado.

Parar e eliminar a proteção atual no cofre GRS:

1. Desative a exclusão suave nas propriedades do cofre GRS. Siga [estes passos](backup-azure-security-feature-cloud.md#disabling-soft-delete-using-azure-portal) para desativar a eliminação suave.

1. Pare a proteção e elimine as cópias de segurança do cofre GRS existente. No menu do painel de instrumentos Vault, selecione **Itens de reserva**. Os itens listados aqui que precisam de ser transferidos para o cofre LRS devem ser removidos juntamente com os seus dados de reserva. Veja como [eliminar itens protegidos na nuvem](backup-azure-delete-vault.md#delete-protected-items-in-the-cloud) e [elimine itens protegidos no local](backup-azure-delete-vault.md#delete-protected-items-on-premises).

1. Se está a planear mover as ações de ficheiros AFS (Azure), os servidores SQL ou os servidores SAP HANA, então também terá de os desregistar. No menu do painel de instrumentos do cofre, selecione **Infraestrutura de backup**. Veja como [desregistritar o servidor SQL,](manage-monitor-sql-database-backup.md#unregister-a-sql-server-instance) [não registe uma conta de armazenamento associada às ações de ficheiros Azure,](manage-afs-backup.md#unregister-a-storage-account)e [não registe uma instância SAP HANA](sap-hana-db-manage.md#unregister-an-sap-hana-instance).

1. Assim que forem removidos do cofre GRS, continuem a configurar os backups para a sua carga de trabalho no novo cofre LRS.

#### <a name="must-preserve-previous-backed-up-data"></a>Deve preservar dados anteriores de back-up

Se precisar de manter os dados protegidos atuais no cofre GRS e continuar a proteção num novo cofre LRS, existem opções limitadas para algumas das cargas de trabalho:

- Para o MARS, pode [parar a proteção com os dados de retenção](backup-azure-manage-mars.md#stop-protecting-files-and-folder-backup) e registar o agente no novo cofre LRS.

  - O serviço de backup Azure continuará a reter todos os pontos de recuperação existentes do cofre GRS.
  - Terá de pagar para manter os pontos de recuperação no cofre da GRS.
  - Só poderá restaurar os dados de apoio para pontos de recuperação não percebidos no cofre grs.
  - Uma nova réplica inicial dos dados terá de ser criada no cofre LRS.

- Para um Azure VM, pode [parar a proteção com os dados de retenção](backup-azure-manage-vms.md#stop-protecting-a-vm) para o VM no cofre GRS, mover o VM para outro grupo de recursos e, em seguida, proteger o VM no cofre LRS. Consulte [orientação e limitações](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md) para mover um VM para outro grupo de recursos.

  Um VM só pode ser protegido num cofre de cada vez. No entanto, o VM do novo grupo de recursos pode ser protegido no cofre LRS, uma vez que é considerado um VM diferente.

  - O serviço de backup Azure irá manter os pontos de recuperação que foram apoiados no cofre GRS.
  - Terá de pagar para manter os pontos de recuperação no cofre GRS (ver [preços de backup do Azure](azure-backup-pricing.md) para mais detalhes).
  - Poderá restaurar o VM, se necessário, do cofre grs.
  - A primeira cópia de segurança no cofre LRS do VM no novo recurso será uma réplica inicial.


## <a name="next-steps"></a>Passos seguintes

[Saiba mais](backup-azure-recovery-services-vault-overview.md) Cofres dos Serviços de Recuperação.
[Saiba mais](backup-azure-delete-vault.md) Apagar cofres dos Serviços de Recuperação.
