---
title: Criar cópias de segurança e restaurar VMs encriptadas do Azure com o Azure Backup
description: Descreve como criar cópias de segurança e restaurar VMs encriptadas do Azure com o serviço de cópia de segurança do Azure.
services: backup
author: geetha
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 4/3/2019
ms.author: geetha
ms.openlocfilehash: 893a22fb9f325625707869c8f6571d572b8f6b33
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61217045"
---
# <a name="back-up-and-restore-encrypted-azure-vm"></a>Criar cópias de segurança e restaurar a VM encriptada do Azure

Este artigo descreve como criar cópias de segurança e restaurar máquinas virtuais Windows ou Linux do Azure (VMs) com discos encriptados utilizando a [Azure Backup](backup-overview.md) serviço.

Se quiser obter mais informações sobre como o Azure Backup interage com as VMs do Azure antes de começar, reveja estes recursos:

- [Revisão](backup-architecture.md#architecture-direct-backup-of-azure-vms) a arquitetura de cópia de segurança de VM do Azure.
- [Saiba mais sobre](backup-azure-vms-introduction.md) cópia de segurança de VM do Azure e a extensão de cópia de segurança do Azure.

## <a name="encryption-support"></a>Suporte de encriptação

O Azure Backup suporta a cópia de segurança de VMs do Azure com os discos de SO/dados encriptados com encriptação de disco do Azure (ADE). ADE utiliza o BitLocker para a encriptação de VMs do Windows e a funcionalidade dm-crypt para VMs do Linux. ADE integra-se com o Azure Key Vault para gerir as chaves de encriptação de disco e segredos. Chaves de encriptação de chave do Key Vault (KEKs) pode ser utilizadas para adicionar uma camada adicional de segurança, criptografia de segredos de encriptação antes de escrevê-los para o Key Vault.

O Azure Backup pode criar cópias de segurança e restaurar VMs do Azure com ADE com e sem a aplicação do Azure AD, conforme resumido na tabela seguinte.

**Tipo de disco da VM** | **ADE (BEK/dm-crypt)** | **ADE e KEK**
--- | --- | ---
**Não gerido** | Sim | Sim
**Gerido**  | Sim | Sim

- Saiba mais sobre [ADE](../security/azure-security-disk-encryption-overview.md), [Key Vault](../key-vault/key-vault-overview.md), e [KEKs](https://blogs.msdn.microsoft.com/cclayton/2017/01/03/creating-a-key-encrypting-key-kek/).
- Leitura a [FAQ](../security/azure-security-disk-encryption-faq.md) para a encriptação de disco da VM do Azure.



### <a name="limitations"></a>Limitações

- Pode criar cópias de segurança e restaurar VMs encriptadas na mesma subscrição e região.
- O Azure Backup suporta VMs encriptadas com chaves autónomo. Qualquer chave que é uma parte de um certificado utilizado para encriptar uma VM não é atualmente suportada.
- Pode criar cópias de segurança e restaurar VMs encriptadas na mesma subscrição e região que o Cofre de cópia de segurança de serviços de recuperação.
- VMs encriptadas não não possível recuperar ao nível do ficheiro/pasta. Precisa recuperar toda a VM para restaurar ficheiros e pastas.
- Ao restaurar uma VM, não é possível utilizar o [substitua VM existente](backup-azure-arm-restore-vms.md#restore-options) opção para VMs encriptadas. Esta opção só é suportada para discos geridos não encriptados.




## <a name="before-you-start"></a>Antes de começar

Antes de começar, faça o seguinte:

1. Certifique-se de que tem uma ou mais [Windows](../security/azure-security-disk-encryption-windows.md) ou [Linux](../security/azure-security-disk-encryption-linux.md) VMs com ADE ativada.
2. [Reveja a matriz de suporte](backup-support-matrix-iaas.md) para cópia de segurança de VM do Azure
3. [Criar](backup-azure-arm-vms-prepare.md#create-a-vault) um cofre de cópia de segurança de serviços de recuperação se não tiver uma.
4. Se ativar a encriptação para as VMs que já foram ativados para cópia de segurança, basta fornecer cópias de segurança com permissões para aceder ao Cofre de chave, para que as cópias de segurança podem continuar sem interrupções. [Saiba mais](#provide-permissions) sobre a atribuição destas permissões.

Além disso, existem algumas coisas que poderá ter de fazer em algumas circunstâncias:

- **Instalar o agente da VM na VM**: O Azure Backup cria cópias de segurança de VMs do Azure ao instalar uma extensão para o agente de VM do Azure em execução na máquina. Se a VM foi criada a partir de uma imagem do Azure marketplace, o agente está instalado e em execução. Se criar uma VM personalizada ou migrar uma máquina no local, talvez seja preciso [instalar manualmente o agente](backup-azure-arm-vms-prepare.md#install-the-vm-agent).
- **Permitir explicitamente o acesso de saída**: Em geral, não precisa de permitir explicitamente o acesso de rede de saída para uma VM do Azure para que ele comunicar com o Azure Backup. No entanto, algumas VMs podem ocorrer problemas de ligação, que mostra a **ExtensionSnapshotFailedNoNetwork** Ocorreu um erro ao tentar ligar. Se isto acontecer, deve [permitir explicitamente o acesso de saída](backup-azure-arm-vms-prepare.md#explicitly-allow-outbound-access), por isso, a extensão de cópia de segurança do Azure pode comunicar com endereços IP públicos do Azure para o tráfego de cópia de segurança.



## <a name="configure-a-backup-policy"></a>Configurar uma política de cópia de segurança

1. Se ainda não criou um cofre de cópias de segurança de serviços de recuperação, siga [estas instruções](backup-azure-arm-vms-prepare.md#create-a-vault)
2. Abra o Cofre no portal e selecione **cópia de segurança** no **introdução** secção.

    ![Painel cópia de segurança](./media/backup-azure-vms-encryption/select-backup.png)

3. Na **objetivo de cópia de segurança** > **em que a sua carga de trabalho é executado?** selecione **Azure**.
4. Na **o que pretende criar cópias de segurança?** selecionar **Máquina Virtual** > **OK**.

      ![Painel cenário](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

5. Na **política de cópia de segurança** > **escolher política de cópia de segurança**, selecione a política que pretende associar ao cofre. Em seguida, clique em **OK**.
    - Uma política de cópia de segurança especifica quando os backups são feitos e quanto eles são armazenados.
    - Os detalhes da política predefinida estão listados no menu pendente.

    ![Abrir o painel Cenário](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

6. Se não pretender utilizar a política predefinida, selecione **criar novo**, e [criar uma política personalizada](backup-azure-arm-vms-prepare.md#create-a-custom-policy).


7. Selecione as VMs encriptadas que pretende criar cópias de segurança através da política de selecionar e selecione **OK**.

      ![Selecione as VMs encriptadas](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

8. Se estiver a utilizar o Azure Key Vault, na página do cofre, verá uma mensagem que a cópia de segurança do Azure necessita de acesso só de leitura para as chaves e segredos no Cofre de chaves.

    - Se receber esta mensagem, é necessária nenhuma ação.

        ![Acesso OK](./media/backup-azure-vms-encryption/access-ok.png)

    - Se receber esta mensagem, precisa definir as permissões conforme descrito no [procedimento abaixo](#provide-permissions).

        ![Aviso de acesso](./media/backup-azure-vms-encryption/access-warning.png)

9. Clique em **ativar cópia de segurança** para implementar a política de cópia de segurança no cofre e ativar a cópia de segurança para as VMs selecionadas.


## <a name="trigger-a-backup-job"></a>Acionar uma tarefa de cópia de segurança

A cópia de segurança inicial será executado em conformidade com o agendamento, mas pode executá-lo imediatamente, da seguinte forma:

1. No menu do cofre, clique em **itens de cópia de segurança**.
2. Na **itens de cópia de segurança** clique em **Máquina Virtual do Azure**.
3. Na **itens de cópia de segurança** lista, clique nas reticências (...).
4. Clique em **agora a cópia de segurança**.
5. Na **cópia de segurança agora**, usar o controle de calendário para selecionar o último dia em que o ponto de recuperação deve ser mantido. Em seguida, clique em **OK**.
6. Monitorize as notificações de portais. Pode monitorizar o progresso da tarefa no dashboard do cofre > **tarefas de cópia de segurança** > **em curso**. Dependendo do tamanho da sua VM, a criação da cópia de segurança inicial poderá demorar algum tempo.


## <a name="provide-permissions"></a>Fornecer permissões

VM do Azure tem acesso só de leitura para criar cópias de segurança as chaves e segredos, juntamente com as VMs associadas.

- Cofre de chaves está associado com o inquilino do Azure AD da subscrição do Azure. Se for um **utilizador de membro**, cópia de segurança do Azure adquire o acesso ao Key Vault, sem mais ações.
- Se for um **utilizador convidado**, tem de fornecer permissões para o Azure Backup aceder ao Cofre de chave.

Para definir permissões:

1. No portal do Azure, selecione **todos os serviços**e procure **cofres de chaves**.
2. Selecione o Cofre de chaves associado à VM encriptada está sendo feito backup.
3. Selecione **políticas de acesso** > **adicionar novo**.
4. Selecione **selecionar principal**e, em seguida, escreva **gestão de cópia de segurança**.
5. Selecione **serviço de gestão de cópia de segurança** > **selecione**.

    ![Seleção de serviço de cópia de segurança](./media/backup-azure-vms-encryption/select-backup-service.png)

6. Na **Adicionar política de acesso** > **configurar a partir de modelo (opcional)** , selecione **do Azure Backup**.
    - As permissões necessárias são prefilled para **permissões da chave** e **permissões secretas**.
    - Se a VM for encriptada com **BEK só**, remova a seleção para **permissões da chave** , uma vez que só precisa de permissões para segredos.

    ![Seleção de cópia de segurança do Azure](./media/backup-azure-vms-encryption/select-backup-template.png)

6. Clique em **OK**. **Serviço de gestão de cópia de segurança** é adicionado ao **políticas de acesso**.

    ![Políticas de acesso](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

7. Clique em **guardar** para fornecer a cópia de segurança do Azure com as permissões.

## <a name="restore-an-encrypted-vm"></a>Restaurar uma VM encriptada

Restaurar VMs encriptadas da seguinte forma:

1. [Restaurar o disco da VM](backup-azure-arm-restore-vms.md#restore-disks).
2. Em seguida, efetue um dos seguintes:
    - Utilize o modelo que é gerado durante a operação de restauro para personalizar as definições da VM e acionar a implementação da VM. [Saiba mais](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm).
    - Crie uma nova VM a partir de discos restaurados com o Powershell. [Saiba mais](backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

## <a name="next-steps"></a>Passos Seguintes

Caso se depare com quaisquer problemas, consulte

- [Erros comuns](backup-azure-vms-troubleshoot.md) quando o backup e restauração encriptados VMs do Azure.
- [Extensão de agente/cópia de segurança VM do Azure](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) problemas.
