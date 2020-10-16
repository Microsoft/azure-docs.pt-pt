---
title: Fazer o back up e restaurar vMs Azure encriptados
description: Descreve como fazer backup e restaurar VMs Azure encriptados com o serviço Azure Backup.
ms.topic: conceptual
ms.date: 08/18/2020
ms.openlocfilehash: 6ce0068203c91d9d2031ce2f8735cccf94172dd8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89014919"
---
# <a name="back-up-and-restore-encrypted-azure-virtual-machines"></a>Fazer o back up e restaurar máquinas virtuais Azure encriptadas

Este artigo descreve como fazer backup e restaurar máquinas virtuais (VMs) do Windows ou Linux Azure com discos encriptados utilizando o serviço [Azure Backup.](backup-overview.md) Para obter mais informações, consulte [a encriptação das cópias de segurança VM da Azure](backup-azure-vms-introduction.md#encryption-of-azure-vm-backups).

## <a name="encryption-using-platform-managed-keys"></a>Encriptação utilizando chaves geridas pela plataforma

Por predefinição, todos os discos dos seus VMs são automaticamente encriptados em repouso utilizando teclas geridas pela plataforma (PMK) que utilizam [encriptação do serviço de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-service-encryption). Pode fazer backup destes VMs utilizando o Azure Backup sem quaisquer ações específicas necessárias para suportar a encriptação na sua extremidade. Para obter mais informações sobre encriptação com chaves geridas pela plataforma, [consulte este artigo](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#platform-managed-keys).

![Discos encriptados](./media/backup-encryption/encrypted-disks.png)

## <a name="encryption-using-customer-managed-keys"></a>Encriptação com chaves geridas pelo cliente

Quando encripta discos com teclas geridas por medida (CMK), a chave utilizada para encriptar os discos é armazenada no Cofre da Chave Azure e é gerida por si. Encriptação do Serviço de Armazenamento (SSE) utilizando CMK difere da encriptação de encriptação do disco Azure (ADE). A ADE utiliza as ferramentas de encriptação do sistema operativo. A SSE encripta dados no serviço de armazenamento, permitindo-lhe utilizar qualquer SISTEMA ou imagens para os seus VMs. Para obter mais informações sobre encriptação de discos geridos com chaves geridas pelo cliente, consulte [este artigo](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys).

## <a name="encryption-support-using-ade"></a>Suporte de encriptação usando ADE

O Azure Backup suporta a cópia de segurança dos VMs Azure que têm os seus discos DE/S/Data encriptados com encriptação do disco Azure (ADE). O ADE utiliza o BitLocker para encriptação de VMs do Windows e a função dm-crypt para Os VMs Do Linux. O ADE integra-se com o Azure Key Vault para gerir chaves e segredos de encriptação de discos. As chaves de encriptação da chave do cofre do cofre (KEKs) podem ser usadas para adicionar uma camada adicional de segurança, encriptando segredos de encriptação antes de escrevê-las no Key Vault.

O Azure Backup pode fazer backup e restaurar VMs Azure usando ADE com e sem a aplicação AD AZure, como resumido na tabela seguinte.

**Tipo de disco da VM** | **ADE (BEK/dm-cripta)** | **ADE e KEK**
--- | --- | ---
**Não gerido** | Sim | Sim
**Gerido**  | Sim | Sim

- Saiba mais sobre [ADE,](../security/fundamentals/azure-disk-encryption-vms-vmss.md) [Key Vault](../key-vault/general/overview.md)e [KEKs](../virtual-machine-scale-sets/disk-encryption-key-vault.md#set-up-a-key-encryption-key-kek).
- Leia as [FAQ](../security/fundamentals/azure-disk-encryption-vms-vmss.md) para encriptação do disco Azure VM.

### <a name="limitations"></a>Limitações

- Pode fazer o back up e restaurar VMs encriptados dentro da mesma subscrição e região.
- O Azure Backup suporta VMs encriptados utilizando chaves autónomas. Qualquer chave que faça parte de um certificado usado para encriptar um VM não é suportado atualmente.
- Pode fazer backup e restaurar VMs encriptados dentro da mesma subscrição e região que o cofre de backup dos Serviços de Recuperação.
- Os VMs encriptados não podem ser recuperados ao nível do ficheiro/pasta. É necessário recuperar todo o VM para restaurar ficheiros e pastas.
- Ao restaurar um VM, não é possível utilizar a opção [VM de substituição existente](backup-azure-arm-restore-vms.md#restore-options) para VMs encriptados. Esta opção só é suportada para discos geridos não encriptados.

## <a name="before-you-start"></a>Antes de começar

Antes de começar, faça o seguinte:

1. Certifique-se de que tem um ou mais [VMs Windows](../virtual-machines/linux/disk-encryption-overview.md) ou [Linux](../virtual-machines/linux/disk-encryption-overview.md) com ADE ativados.
2. [Reveja a matriz](backup-support-matrix-iaas.md) de suporte para backup Azure VM
3. [Crie](backup-create-rs-vault.md) um cofre de reserva dos Serviços de Recuperação se não tiver um.
4. Se ativar a encriptação de VMs que já estão ativados para cópia de segurança, basta fornecer ao Backup permissões para aceder ao Cofre de Chaves para que as cópias de segurança possam continuar sem interrupções. [Saiba mais](#provide-permissions) sobre a atribuição destas permissões.

Além disso, há algumas coisas que pode precisar fazer em algumas circunstâncias:

- **Instale o agente VM no VM**: Azure Backup apoia os VMs Azure instalando uma extensão ao agente Azure VM em funcionamento na máquina. Se o seu VM foi criado a partir de uma imagem do Azure Marketplace, o agente está instalado e em execução. Se criar um VM personalizado, ou migrar uma máquina no local, poderá ter de [instalar o agente manualmente](backup-azure-arm-vms-prepare.md#install-the-vm-agent).

## <a name="configure-a-backup-policy"></a>Configure uma política de backup

1. Se ainda não criou um cofre de reserva dos Serviços de Recuperação, siga [estas instruções](backup-create-rs-vault.md).
1. Abra o cofre no portal e selecione **+Backup** na secção **'Visão Geral'.**

    ![Painel de reserva](./media/backup-azure-vms-encryption/select-backup.png)

1. Em **Backup goal**Onde está a sua carga de trabalho a  >  **correr?** **Azure**
1. Em O que pretende fazer **Virtual machine**para fazer **o back-up?** Em seguida, **selecione Backup**.

      ![Painel de cenário](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

1. Na **política de backup**Escolha a política de  >  **backup,** selecione a política que pretende associar ao cofre. Em seguida, selecione **OK**.
    - Uma política de backup especifica quando as cópias de segurança são tomadas e quanto tempo estão armazenadas.
    - Os detalhes da política predefinida estão listados no menu pendente.

    ![Escolha a política de backup](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

1. Se não quiser utilizar a política predefinitiva, **selecione Create New**e crie uma política [personalizada](backup-azure-arm-vms-prepare.md#create-a-custom-policy).

1. Em **Máquinas Virtuais**, selecione **Adicionar**.

    ![Adicionar máquinas virtuais](./media/backup-azure-vms-encryption/add-virtual-machines.png)

1. Escolha os VMs encriptados que pretende fazer de fazer com a política selecionada e selecione **OK**.

      ![Selecione VMs encriptados](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

1. Se estiver a usar o Cofre da Chave Azure, na página do cofre, verá uma mensagem de que o Azure Backup precisa de acesso apenas de leitura às chaves e segredos no Cofre de Chaves.

    - Se receber esta mensagem, não é necessária qualquer ação.

        ![Acesso OK](./media/backup-azure-vms-encryption/access-ok.png)

    - Se receber esta mensagem, tem de definir permissões conforme descrito no [procedimento abaixo](#provide-permissions).

        ![Aviso de acesso](./media/backup-azure-vms-encryption/access-warning.png)

1. Selecione **Ativar** a cópia de segurança para implementar a política de backup no cofre e ative a cópia de segurança para os VMs selecionados.

## <a name="trigger-a-backup-job"></a>Desencadear um trabalho de reserva

A cópia de segurança inicial será executada de acordo com o horário, mas pode executá-la imediatamente da seguinte forma:

1. No menu do cofre, selecione **itens de reserva**.
2. Em **Itens de Cópia de Segurança**, selecione **Azure Virtual Machine**.
3. Na lista **de Itens de Cópia de Segurança,** selecione as elipses (...).
4. **Selecione Backup agora**.
5. Em **Backup Now,** utilize o controlo do calendário para selecionar o último dia em que o ponto de recuperação deve ser mantido. Em seguida, selecione **OK**.
6. Monitorize as notificações do portal. Pode monitorizar o progresso do trabalho no painel de segurança do cofre > **trabalhos de reserva**  >  **em curso**. Dependendo do tamanho da sua VM, a criação da cópia de segurança inicial poderá demorar algum tempo.

## <a name="provide-permissions"></a>Fornecer permissões

O Azure Backup precisa de acesso apenas de leitura para fazer cópias de segurança das chaves e segredos, juntamente com os VMs associados.

- O seu Cofre-Chave está associado ao inquilino Azure AD da assinatura Azure. Se for **um utilizador membro,** o Azure Backup adquire acesso ao Cofre de Chaves sem mais ações.
- Se for um **utilizador convidado,** tem de fornecer permissões para que o Azure Backup aceda ao cofre da chave.

Para definir permissões:

1. No portal Azure, selecione **Todos os serviços**e procure **cofres chave**.
1. Selecione o cofre de chaves associado ao VM encriptado que está a fazer.
1. Selecione **políticas de acesso**Adicionar Política de  >  **Acesso**.

    ![Adicionar política de acesso](./media/backup-azure-vms-encryption/add-access-policy.png)

1. In **Add access policy**  >  **Configure from template (opcional)**, selecione **Azure Backup**.
    - As permissões necessárias são preenchidas para **permissões chave** e **permissões secretas.**
    - Se o seu VM estiver encriptado apenas com **BEK,** remova a seleção para **permissões chave,** uma vez que só necessita de permissões para segredos.

    ![Seleção de backup Azure](./media/backup-azure-vms-encryption/select-backup-template.png)

1. Selecione **Adicionar**. **O Serviço de Gestão de Cópias** de Segurança é adicionado às **políticas de Acesso.**

    ![Políticas de acesso](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

1. **Selecione Guardar** para fornecer cópia de segurança do Azure com as permissões.

## <a name="restore-an-encrypted-vm"></a>Restaurar um VM encriptado

Os VMs encriptados só podem ser restaurados restaurando o disco VM, conforme explicado abaixo. **Substitua os** VM existentes e **o VM de restauro** não seja suportado.

Restaurar vMs encriptados da seguinte forma:

1. [Restaurar o disco VM](backup-azure-arm-restore-vms.md#restore-disks).
2. Recrie a instância da máquina virtual fazendo uma das seguintes:
    1. Utilize o modelo gerado durante a operação de restauro para personalizar as definições de VM e desencadear a implementação de VM. [Saiba mais](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm).
    2. Crie um novo VM a partir dos discos restaurados utilizando o PowerShell. [Saiba mais](backup-azure-vms-automation.md#create-a-vm-from-restored-disks).
3. Para os VMs Linux, reinstale a extensão ADE para que os discos de dados estejam abertos e montados.

## <a name="next-steps"></a>Passos seguintes

Se encontrar algum problema, reveja estes artigos:

- [Erros comuns](backup-azure-vms-troubleshoot.md) ao fazer o backup e restaurar VMs Azure encriptados.
- [Problemas de extensão de agente VM/backup da Azure.](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)
