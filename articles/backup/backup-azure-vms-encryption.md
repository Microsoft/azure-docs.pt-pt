---
title: Back up e restaurar VMs Azure encriptados
description: Descreve como fazer backup e restaurar vMs Azure encriptados com o serviço de backup Azure.
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: a3976cc83f749b1abe00cef3f5bf867ffbc30ab6
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2020
ms.locfileid: "78206695"
---
# <a name="back-up-and-restore-encrypted-azure-vm"></a>Back up e restaurar Azure VM encriptado

Este artigo descreve como fazer backup e restaurar as máquinas virtuais Windows ou Linux Azure (VMs) com discos encriptados utilizando o serviço [de backup Azure.](backup-overview.md)

Se quiser saber mais sobre como o Azure Backup interage com os VMs Azure antes de começar, reveja estes recursos:

- [Reveja](backup-architecture.md#architecture-built-in-azure-vm-backup) a arquitetura de backup Azure VM.
- [Saiba mais sobre](backup-azure-vms-introduction.md) Backup Azure VM e a extensão de backup Azure.

## <a name="encryption-support"></a>Suporte de encriptação

O Azure Backup suporta a cópia de segurança dos VMs Azure que têm os seus discos OS/dados encriptados com encriptação de disco azure (ADE). O ADE utiliza o BitLocker para encriptação de VMs do Windows e a funcionalidade dm-criptografia para VMs Linux. A ADE integra-se com o Azure Key Vault para gerir chaves e segredos de encriptação de discos. As chaves de encriptação da chave do cofre (KEKs) podem ser usadas para adicionar uma camada adicional de segurança, encriptando segredos de encriptação antes de os escrever em Key Vault.

O Azure Backup pode fazer backup e restaurar os VMs Azure utilizando a ADE com e sem a aplicação Azure AD, conforme resumido na tabela seguinte.

**Tipo de disco da VM** | **ADE (BEK/dm-cripta)** | **ADE e KEK**
--- | --- | ---
**Não gerido** | Sim | Sim
**Gerido**  | Sim | Sim

- Saiba mais sobre [ADE,](../security/azure-security-disk-encryption-overview.md) [Key Vault](../key-vault/key-vault-overview.md)e [KEKs.](https://docs.microsoft.com/azure/virtual-machine-scale-sets/disk-encryption-key-vault#set-up-a-key-encryption-key-kek)
- Leia as [FAQ](../security/azure-security-disk-encryption-faq.md) para encriptação de disco Azure VM.

### <a name="limitations"></a>Limitações

- Pode fazer o back up e restaurar os VMs encriptados dentro da mesma subscrição e região.
- O Azure Backup suporta VMs encriptados utilizando chaves autónomas. Qualquer chave que faça parte de um certificado usado para encriptar um VM não é suportada atualmente.
- Pode fazer backup e restaurar VMs encriptados dentro da mesma subscrição e região que o cofre de backup de serviços de recuperação.
- VMs encriptados não podem ser recuperados ao nível de ficheiro/pasta. Precisa recuperar todo o VM para restaurar ficheiros e pastas.
- Ao restaurar um VM, não pode utilizar a opção [VM de substituição existente](backup-azure-arm-restore-vms.md#restore-options) para VMs encriptados. Esta opção só é suportada para discos geridos não encriptados.

## <a name="before-you-start"></a>Antes de começar

Antes de começar, faça o seguinte:

1. Certifique-se de que tem um ou mais VMs [Windows](../security/azure-security-disk-encryption-windows.md) ou [Linux](../virtual-machines/linux/disk-encryption-overview.md) com ADE ativado.
2. [Reveja a matriz de suporte](backup-support-matrix-iaas.md) para backup Azure VM
3. [Crie](backup-azure-arm-vms-prepare.md#create-a-vault) um cofre de backup dos Serviços de Recuperação se não tiver um.
4. Se ativar a encriptação para VMs que já estão ativadas para backup, basta fornecer permissões de backup para aceder ao Cofre chave para que as cópias de segurança possam continuar sem interrupções. [Saiba mais](#provide-permissions) sobre a atribuição destas permissões.

Além disso, há algumas coisas que talvez precises de fazer em algumas circunstâncias:

- **Instale o agente VM no VM**: Azure Backup apoia os VMs Azure instalando uma extensão ao agente Azure VM em funcionamento na máquina. Se o seu VM foi criado a partir de uma imagem de mercado Azure, o agente está instalado e em execução. Se criar um VM personalizado, ou migrar uma máquina no local, poderá ter de [instalar o agente manualmente](backup-azure-arm-vms-prepare.md#install-the-vm-agent).

## <a name="configure-a-backup-policy"></a>Configurar uma política de backup

1. Se ainda não criou um cofre de backup dos Serviços de Recuperação, siga [estas instruções](backup-azure-arm-vms-prepare.md#create-a-vault)
2. Abra o cofre no portal e selecione **Backup** na secção **Iniciar.**

    ![Lâmina de reserva](./media/backup-azure-vms-encryption/select-backup.png)

3. Na **meta de backup** > Ondeestá a sua carga **de trabalho?**
4. Em **O que quer fazer de volta?** Selecione máquina **Virtual** > **OK**.

      ![Lâmina de cenário](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

5. Na **política de backup** > Escolha a política de **backup**, selecione a política que pretende associar ao cofre. Em seguida, clique em **OK**.
    - Uma política de backup especifica quando os backups são recolhidos e quanto tempo são armazenados.
    - Os detalhes da política predefinida estão listados no menu pendente.

    ![Abrir o painel Cenário](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

6. Se não quiser usar a política de predefinição, selecione **Create New**e [crie uma política personalizada](backup-azure-arm-vms-prepare.md#create-a-custom-policy).

7. Escolha os VMs encriptados que pretende fazer de volta utilizando a política selecionada e selecione **OK**.

      ![Selecione VMs encriptados](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

8. Se estás a usar o Cofre de Chaves Azure, na página do cofre, vês uma mensagem de que o Azure Backup precisa de acesso apenas às chaves e segredos do Cofre de Chaves.

    - Se receber esta mensagem, não é necessária qualquer ação.

        ![Acesso OK](./media/backup-azure-vms-encryption/access-ok.png)

    - Se receber esta mensagem, tem de definir permissões conforme descrito no [procedimento abaixo](#provide-permissions).

        ![Aviso de acesso](./media/backup-azure-vms-encryption/access-warning.png)

9. Clique em **'Ativar backup'** para implementar a política de backup no cofre e ativar a cópia de segurança para os VMs selecionados.

## <a name="trigger-a-backup-job"></a>Desencadear um trabalho de reserva

O backup inicial será executado de acordo com o horário, mas você pode executá-lo imediatamente da seguinte forma:

1. No menu do cofre, clique em **itens de backup**.
2. Em **itens de backup,** clique em **Máquina Virtual Azure**.
3. Na lista **de Itens de Backup,** clique nas elipses (...).
4. Clique em **Backup agora**.
5. Em **Backup Now,** utilize o controlo do calendário para selecionar o último dia em que o ponto de recuperação deve ser mantido. Em seguida, clique em **OK**.
6. Monitorize as notificações do portal. Você pode monitorizar o progresso do trabalho no painel de abóbadas > **Backup Jobs** > **Em andamento**. Dependendo do tamanho da sua VM, a criação da cópia de segurança inicial poderá demorar algum tempo.

## <a name="provide-permissions"></a>Fornecer permissões

O Azure VM precisa de acesso apenas para apoiar as chaves e segredos, juntamente com os VMs associados.

- O seu Cofre Chave está associado ao inquilino da AD Azure da subscrição Azure. Se for **um utilizador membro,** o Azure Backup adquire acesso ao Cofre chave sem mais ações.
- Se for **um utilizador convidado,** deve fornecer permissões para que o Azure Backup aceda ao cofre da chave.

Para definir permissões:

1. No portal Azure, selecione **Todos os serviços**e procure **cofres chave.**
2. Selecione o cofre de chaves associado ao VM encriptado que está a apoiar.
3. Selecione **as políticas** de acesso > **Adicionar novas**.
4. **Selecione o principal,** e, em seguida, digite a **Gestão de Backup**.
5. Selecione O Serviço de **Gestão de Backup** > **Selecione**.

    ![Seleção de serviço de backup](./media/backup-azure-vms-encryption/select-backup-service.png)

6. Em **Adicionar a política de acesso** > **Configurar a partir do modelo (opcional)** , selecione **Backup Azure**.
    - As permissões necessárias são preenchidas para **permissões chave** e **permissões secretas**.
    - Se o seu VM estiver encriptado **usando apenas BEK**, remova a seleção de **permissões Chave,** uma vez que apenas necessita de permissões para segredos.

    ![Seleção de backup azure](./media/backup-azure-vms-encryption/select-backup-template.png)

7. Clique em **OK**. **O Serviço de Gestão de Backup** é adicionado às políticas de **acesso.**

    ![Políticas de acesso](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

8. Clique em **Guardar** para fornecer ao Azure Backup as permissões.

## <a name="restore-an-encrypted-vm"></a>Restaurar um VM encriptado

Restaura VMs encriptados da seguinte forma:

1. [Restaurar o disco VM](backup-azure-arm-restore-vms.md#restore-disks).
2. Recrie a instância da máquina virtual fazendo um dos seguintes:
    1. Utilize o modelo gerado durante a operação de restauro para personalizar as definições de VM e acionar a implementação do VM. [Saiba mais](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm).
    2. Crie um novo VM a partir dos discos restaurados utilizando o PowerShell. [Saiba mais](backup-azure-vms-automation.md#create-a-vm-from-restored-disks).
3. Para os VMs Linux, reinstale a extensão ADE para que os discos de dados estejam abertos e montados.

## <a name="next-steps"></a>Passos seguintes

Se tiver algum problema, reveja estes artigos:

- [Erros comuns](backup-azure-vms-troubleshoot.md) ao fazer backup e restaurar VMs Azure encriptados.
- Problemas de extensão de [agente/backup Azure VM.](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)
