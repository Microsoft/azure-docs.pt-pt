---
title: Criar e configurar cofres dos Serviços de Recuperação
description: Neste artigo, aprenda a criar e configurar cofres dos Serviços de Recuperação que armazenam os backups e pontos de recuperação. Saiba como usar o Cross Region Restore para restaurar numa região secundária.
ms.topic: conceptual
ms.date: 05/30/2019
ms.custom: references_regions
ms.openlocfilehash: 1a20cd2b1245febea5fd18a9f6fe6e7a7bb6f04b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101716759"
---
# <a name="create-and-configure-a-recovery-services-vault"></a>Criar e configurar um cofre dos Serviços de Recuperação

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="set-storage-redundancy"></a>Definir redundância de armazenamento

O Azure Backup manuseia automaticamente o armazenamento para o cofre. Tem de especificar como é que o armazenamento é replicado.

> [!NOTE]
> Alterar **o tipo de replicação de armazenamento** (localmente redundante/ geo-redundante) para um cofre dos Serviços de Recuperação tem de ser feito antes de configurar cópias de segurança no cofre. Uma vez configurada cópia de segurança, a opção de modificar é desativada.
>
>- Se ainda não configurar a cópia de segurança, [siga estes passos](#set-storage-redundancy) para rever e modificar as definições.
>- Se já configuraram o backup e têm de passar de GRS para LRS, então [revejam estas soluções alternativas.](#how-to-change-from-grs-to-lrs-after-configuring-backup)

1. Do painel de cofres dos **Serviços de Recuperação,** selecione o novo cofre. Na secção **Definições,** selecione  **Propriedades**.
1. Em **Propriedades**, em **Configuração de Cópia de Segurança**, selecione **Update**.

1. Selecione o tipo de replicação de armazenamento e **selecione Guardar**.

     ![Definir a configuração de armazenamento do novo cofre](./media/backup-create-rs-vault/recovery-services-vault-backup-configuration.png)

   - Recomendamos que, se estiver a utilizar o Azure como ponto final de armazenamento de backup primário, continue a utilizar a definição **geo-redundante** padrão.
   - Se não utilizar o Azure como um ponto final do armazenamento de cópia de segurança primário, escolha **Localmente redundante**, o que reduz os custos de armazenamento do Azure.
   - Saiba mais sobre [a redundância geo](../storage/common/storage-redundancy.md#geo-redundant-storage) e [local.](../storage/common/storage-redundancy.md#locally-redundant-storage)
   - Se necessitar de disponibilidade de dados sem tempo de inatividade numa região, garantindo residência de dados, então escolha [armazenamento redundante de zona](../storage/common/storage-redundancy.md#zone-redundant-storage).

>[!NOTE]
>As definições de replicação de armazenamento para o cofre não são relevantes para a cópia de segurança da partilha de ficheiros Azure, uma vez que a solução atual é baseada em instantâneos e não há dados transferidos para o cofre. As imagens são armazenadas na mesma conta de armazenamento que a parte de ficheiros com reserva.

## <a name="set-cross-region-restore"></a>Definir restauro da região de cruz

A opção de restauro **Cross Region Restore (CRR)** permite-lhe restaurar os dados numa região secundária, [azure emparelhada.](../best-practices-availability-paired-regions.md)

Suporta os seguintes recursos de dados:

- VMs Azure (disponibilidade geral)
- Bases de dados SQL hospedadas em VMs Azure (pré-visualização)
- Bases de dados SAP HANA hospedadas em VMs Azure (pré-visualização)

A utilização do Cross Region Restore permite-lhe:

- realizar exercícios quando há uma auditoria ou requisito de conformidade
- restaurar os dados se houver um desastre na região primária

Ao restaurar um VM, pode restaurar o VM ou o seu disco. Se estiver a restaurar as bases de dados SQL/SAP HANA acolhidas em VMs Azure, então pode restaurar bases de dados ou ficheiros.

Para escolher esta função, selecione **Enable Cross Region Restore** a partir do painel de **configuração de backup.**

Uma vez que este processo está ao nível de armazenamento, [existem implicações nos preços.](https://azure.microsoft.com/pricing/details/backup/)

>[!NOTE]
>Antes de começar:
>
>- Reveja a matriz de [apoio](backup-support-matrix.md#cross-region-restore) para uma lista de tipos e regiões geridos apoiados.
>- A funcionalidade Cross Region Restore (CRR) para VMs Azure está agora em disponibilidade geral em todas as regiões públicas do Azure.
>- As bases de dados Cross Region Restore para as bases de dados SQL e SAP HANA estão em pré-visualização em todas as regiões públicas do Azure.
>- CRR é uma função de opt-in de nível de abóbada para qualquer cofre GRS (desligado por defeito).
>- Depois de optar, pode levar até 48 horas para que os itens de reserva estejam disponíveis em regiões secundárias.
>- Atualmente, crr para VMs Azure é suportado apenas para Azure Resource Manger Azure VMs. Os VMs clássicos do Azure não serão suportados.  Quando tipos de gestão adicionais suportam CRR, então eles serão **automaticamente** matriculados.
>- O Cross Region Restore **não pode ser revertido** para GRS ou LRS uma vez que a proteção é iniciada pela primeira vez.
>- Atualmente, a [RPO](azure-backup-glossary.md#rpo-recovery-point-objective) da região secundária está a 12 horas da região primária, embora a replicação [de armazenamento geo-redundante de acesso de leitura (RA-GRS)](../storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) seja de 15 minutos.

### <a name="configure-cross-region-restore"></a>Conigure Região Cruzada Restaurar

Um cofre criado com redundância GRS inclui a opção de configurar a funcionalidade Cross Region Restore. Cada cofre GRS terá um banner, que ligará à documentação. Para configurar o CRR para o cofre, vá ao painel de configuração de backup, que contém a opção para ativar esta função.

 ![Banner de configuração de backup](./media/backup-azure-arm-restore-vms/banner.png)

1. A partir do portal, aceda ao cofre dos Serviços de Recuperação > **Propriedades** (em **Definições).**
1. Na **configuração de cópia de segurança**, selecione **Update**.
1. Selecione **Enable Cross Region Restore neste cofre** para ativar a funcionalidade.

   ![Permitir a restauração da Região Cruzada](./media/backup-azure-arm-restore-vms/backup-configuration.png)

Consulte estes artigos para obter mais informações sobre backup e restauro com CRR:

- [Restauro da Região Transversal para VMs Azure](backup-azure-arm-restore-vms.md#cross-region-restore)
- [Restauro da Região Transversal para bases de dados SQL](restore-sql-database-azure-vm.md#cross-region-restore)
- [Restauro da Região Transversal para bases de dados SAP HANA](sap-hana-db-restore.md#cross-region-restore)

## <a name="set-encryption-settings"></a>Definir definições de encriptação

Por predefinição, os dados no cofre dos Serviços de Recuperação são encriptados utilizando chaves geridas pela plataforma. Não são necessárias ações explícitas do seu lado para ativar esta encriptação, e aplica-se a todas as cargas de trabalho que estão a ser apoiadas até ao cofre dos Serviços de Recuperação.  Pode optar por trazer a sua própria chave para encriptar os dados de backup neste cofre. Isto é referido como chaves geridas pelo cliente. Se desejar encriptar dados de backup usando a sua própria chave, a chave de encriptação deve ser especificada antes de qualquer item ser protegido para este cofre. Uma vez que ativa a encriptação com a sua chave, não pode ser invertida.

### <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>Configurar um cofre para encriptar usando chaves geridas pelo cliente

Para configurar o seu cofre para encriptar com as chaves geridas pelo cliente, estes passos devem ser seguidos por esta ordem:

1. Ativar a identidade gerida para o cofre dos Serviços de Recuperação

1. Atribua permissões ao cofre para aceder à chave de encriptação no Cofre da Chave Azure

1. Permitir a eliminação suave e a proteção de purga no Cofre da Chave Azure

1. Atribua a chave de encriptação ao cofre dos Serviços de Recuperação

As instruções para cada um destes passos podem ser encontradas [neste artigo](encryption-at-rest-with-cmk.md#configuring-a-vault-to-encrypt-using-customer-managed-keys).

## <a name="modifying-default-settings"></a>Modificar as definições predefinidos

É altamente recomendável rever as predefinições do **Tipo de Replicação de Armazenamento** e as **Definições de segurança** antes de configurar as cópias de segurança no cofre.

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