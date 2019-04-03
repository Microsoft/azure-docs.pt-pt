---
title: Criar cópias de segurança e restaurar VMs encriptadas do Azure com os serviços de cópia de segurança do Azure
description: Este artigo fala sobre a experiência de cópia de segurança e restauro para VMs encriptadas com o Azure Disk Encryption.
services: backup
author: geetha
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 7/10/2018
ms.author: geetha
ms.openlocfilehash: 28126df0dfd9a03e93a76fa5071331603c4819a4
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2019
ms.locfileid: "58851016"
---
# <a name="back-up-and-restore-encrypted-virtual-machines-with-azure-backup"></a>Criar cópias de segurança e restaurar máquinas virtuais encriptadas com o Azure Backup
Este artigo fala sobre os passos para criar cópias de segurança e restaurar máquinas virtuais (VMs) com o Azure Backup. Também fornece detalhes sobre os cenários suportados, pré-requisitos e passos de resolução de problemas para casos de erro.

## <a name="supported-scenarios"></a>Cenários suportados

 Cópia de segurança e restauro de VMs encriptadas só é suportada para VMs que utilizam o modelo de implementação Azure Resource Manager. Não é suportada para VMs que utilizam o modelo de implementação clássica. Cópia de segurança e restauro de VMs encriptadas é suportada para Windows e VMs do Linux que utilizam o Azure Disk Encryption. Encriptação de disco utiliza a funcionalidade de BitLocker padrão da indústria do Windows e a funcionalidade de dm-crypt do Linux para fornecer encriptação de discos. A tabela seguinte mostra o tipo de encriptação e suporte para VMs.

   |  | BEK + KEK VMs | VMs só BEK |
   | --- | --- | --- |
   | **Em VMs**  | Sim | Sim  |
   | **VMs geridas**  | Sim | Sim  |

   > [!NOTE]
   > O Azure Backup suporta VMs encriptadas com chaves autónomo. Qualquer chave que é uma parte de um certificado utilizado para encriptar uma VM não é suportada atualmente.
   >

## <a name="prerequisites"></a>Pré-requisitos
* A VM foi encriptada utilizando [do Azure Disk Encryption](../security/azure-security-disk-encryption.md).

* Foi criado um cofre dos serviços de recuperação e a replicação de armazenamento foi definida ao seguir os passos em [preparar o ambiente para cópia de segurança](backup-azure-arm-vms-prepare.md).

* Cópia de segurança foi indicada permissões para aceder um cofre de chaves que contêm chaves e segredos para VMs encriptadas.

## <a name="back-up-an-encrypted-vm"></a>Fazer uma cópia de segurança de uma VM encriptada
Utilize os seguintes passos para definir um objetivo de cópia de segurança, definir uma política, configurar os itens e acionar uma cópia de segurança.

### <a name="configure-backup"></a>Configurar a cópia de segurança
1. Se já tiver um cofre de serviços de recuperação aberto, avance para o passo seguinte. Se não tiver um cofre de serviços de recuperação aberto, mas está no portal do Azure, selecione **todos os serviços**.

   a. Na lista de recursos, escreva **Serviços de Recuperação**.

   b. À medida que começa a escrever, a lista filtra com base na sua entrada. Quando vir **cofres dos serviços de recuperação**, selecioná-lo.

      ![Cofre dos Serviços de Recuperação](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

    c. É apresentada a lista dos cofres dos Serviços de Recuperação. Selecione um cofre da lista.

     O dashboard do cofre selecionado é aberto.
1. Na lista de itens que é apresentado no cofre, selecione **cópia de segurança** para começar a fazer backup da VM encriptada.

      ![Painel cópia de segurança](./media/backup-azure-vms-encryption/select-backup.png)
1. Sobre o **cópia de segurança** mosaico, selecione **objetivo de cópia de segurança**.

      ![Painel cenário](./media/backup-azure-vms-encryption/select-backup-goal-one.png)
1. Sob **em que a sua carga de trabalho é executado?**, selecione **Azure**. Sob **o que pretende criar cópias de segurança?**, selecione **Máquina Virtual**. Em seguida, selecione **OK**.

   ![Abrir o painel Cenário](./media/backup-azure-vms-encryption/select-backup-goal-two.png)
1. Sob **escolher política de cópia de segurança**, selecione a política de cópia de segurança que pretende aplicar ao cofre. Em seguida, selecione **OK**.

      ![Selecionar política de cópia de segurança](./media/backup-azure-vms-encryption/setting-rs-backup-policy-new.png)

    Os detalhes da política predefinida são listados. Se quiser criar uma política, selecione **criar novo** na lista pendente. Depois de selecionar **OK**, a política de cópia de segurança é associada ao cofre.

1. Escolha as VMs encriptadas a associar à política especificada e selecione **OK**.

      ![Selecione as VMs encriptadas](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)
1. Esta página mostra uma mensagem sobre cofres de chaves associado para as VMs encriptadas que selecionou. Cópia de segurança requer acesso só de leitura para as chaves e segredos no Cofre de chaves. Utiliza estas permissões para criar cópias de segurança as chaves e segredos, juntamente com as VMs associadas.<br>
Se for um **utilizador de membro**, processo de ativar cópia de segurança será perfeitamente adquirir acesso ao Cofre de chaves para efetuar cópias de segurança de VMs encriptadas sem exigir qualquer intervenção do utilizador.

   ![Mensagem de VMs encriptada](./media/backup-azure-vms-encryption/member-user-encrypted-vm-warning-message.png)

   Para uma **utilizador convidado**, tem de fornecer permissões para o serviço de cópia de segurança para aceder ao Cofre de chaves para cópias de segurança trabalhar. Pode fornecer estas permissões ao seguir os passos mencionados na secção seguinte

   ![Mensagem de VMs encriptada](./media/backup-azure-vms-encryption/guest-user-encrypted-vm-warning-message.png)

    Agora que definiu todas as definições do cofre, selecione **ativar cópia de segurança** na parte inferior da página. **Ativar cópia de segurança** implementa a política no cofre e as VMs.

1. A próxima fase no processo de preparação está a instalar o agente da VM ou certificar-se de que o agente da VM está instalado. Para fazer o mesmo, siga os passos em [preparar o ambiente para cópia de segurança](backup-azure-arm-vms-prepare.md).

### <a name="trigger-a-backup-job"></a>Acionar uma tarefa de cópia de segurança
Siga os passos em [VMs de cópia de segurança do Azure para um cofre dos serviços de recuperação](backup-azure-arm-vms.md) para acionar uma tarefa de cópia de segurança.

### <a name="continue-backups-of-already-backed-up-vms-with-encryption-enabled"></a>Continuar as cópias de segurança de VMs de cópia de segurança já com encriptação ativada  
Se tiver VMs já a cópia de segurança num cofre dos serviços de recuperação que estão ativadas para a encriptação mais tarde, tem de dar permissões de volta até o acesso a chave do cofre para cópias de segurança continuar. Pode fornecer estas permissões ao seguir a [os passos na secção seguinte](#provide-permissions). Ou pode seguir os passos de PowerShell na secção "Ativar cópia de segurança" a [documentação do PowerShell](backup-azure-vms-automation.md).

## <a name="provide-permissions"></a>Fornecer permissões
Utilize os seguintes passos para fornecer permissões relevantes para o Azure Backup aceder ao Cofre de chaves e executar a cópia de segurança de VMs encriptadas.
1. Selecione **todos os serviços**e procure **cofres de chaves**.

    ![Cofres de chaves](./media/backup-azure-vms-encryption/search-key-vault.png)

1. Na lista de cofres de chaves, selecione o Cofre de chaves associado à VM encriptada que tem de ser efetuada a cópia de segurança.

     ![Seleção de Cofre de chaves](./media/backup-azure-vms-encryption/select-key-vault.png)

1. Selecione **políticas de acesso**e, em seguida, selecione **adicionar novo**.

    ![Adicionar nova](./media/backup-azure-vms-encryption/select-key-vault-access-policy.png)

1. Selecione **selecionar principal**e, em seguida, escreva **serviço de gestão de cópia de segurança** na caixa de pesquisa.

    ![Pesquisa de serviço de cópia de segurança](./media/backup-azure-vms-encryption/search-backup-service.png)

1. Selecione **serviço de gestão de cópia de segurança**e, em seguida, selecione **selecione**.

    ![Seleção de serviço de cópia de segurança](./media/backup-azure-vms-encryption/select-backup-service.png)

1. Sob **configurar a partir de modelo (opcional)**, selecione **cópia de segurança do Azure**. As permissões necessárias são prefilled para **permissões da chave** e **permissões secretas**. Se a VM é encriptada utilizando **BEK só**, apenas para segredos são necessárias permissões de, pelo que tem de remover a seleção para **permissões da chave**.

    ![Seleção de cópia de segurança do Azure](./media/backup-azure-vms-encryption/select-backup-template.png)

1. Selecione **OK**. Tenha em atenção que **serviço de gestão de cópia de segurança** é adicionado na **políticas de acesso**.

    ![Políticas de acesso](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

1. Selecione **guardar** para conceder as permissões necessárias para a cópia de segurança.

    ![Política de cópia de segurança de acesso](./media/backup-azure-vms-encryption/save-access-policy.png)

Depois de permissões com êxito são fornecidas, pode avançar com a ativação de cópia de segurança para VMs encriptadas.

## <a name="restore-an-encrypted-vm"></a>Restaurar uma VM encriptada
Restaurar o Azure suporta agora a cópia de segurança de [Azure encriptados VM sem do Azure AD](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-prerequisites-aad) além da oferta anterior de restauro, suporte para o Azure encriptados de VM com o Azure AD.<br>

Para restaurar uma VM encriptada, restaurar discos ao seguir os passos na secção "Restaurar discos de cópia de segurança" na [escolha uma configuração de restauro de VM](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration). Depois disso, pode utilizar uma das seguintes opções:

* Siga os passos no PowerShell [criar uma VM a partir de discos restaurados](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) para criar uma VM completa a partir de discos restaurados.
* Ou, [usar modelos para personalizar uma VM restaurada](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm) para criar VMs a partir de discos restaurados. Os modelos podem ser utilizados apenas para pontos de recuperação criados depois de 26 de Abril de 2017.

## <a name="troubleshooting-errors"></a>Resolução de erros
| Operação | Detalhes do erro | Resolução |
| --- | --- | --- |
|Cópia de segurança | Código do erro: UserErrorKeyVaultPermissionsNotConfigured<br><br>Mensagem de erro: O serviço de cópia de segurança do Azure não tem permissões suficientes para o Key Vault para cópia de segurança das máquinas virtuais encriptadas. | Cópia de segurança deve ser fornecida estas permissões ao seguir a [os passos na secção anterior](#provide-permissions). Ou pode seguir os passos de PowerShell na secção "Ativar a proteção" do artigo, [utilize o PowerShell para criar cópias de segurança e restaurar máquinas virtuais](backup-azure-vms-automation.md#enable-protection). |  
| Restauro | Não é possível restaurar esta VM encriptada porque não existe o Cofre de chaves associado a esta VM. |Criar um cofre de chaves utilizando [o que é o Azure Key Vault?](../key-vault/key-vault-overview.md). Ver [restaurar uma chave de Cofre de chaves e um segredo com o Azure Backup](backup-azure-restore-key-secret.md) para restaurar uma chave e um segredo, se eles não estão presentes. |
| Restauro | Código do erro: UserErrorKeyVaultKeyDoesNotExist<br><br> Mensagem de erro: Não é possível restaurar esta VM encriptada, uma vez que a chave associada esta VM não existe. |Ver [restaurar uma chave de Cofre de chaves e um segredo com o Azure Backup](backup-azure-restore-key-secret.md) para restaurar uma chave e um segredo, se eles não estão presentes. |
| Restauro | Código do erro: ProviderAuthorizationFailed/UserErrorProviderAuthorizationFailed<br><br>Mensagem de erro: O Serviço de Cópia de Segurança não tem autorização para aceder a recursos na sua subscrição. |Restaurar, conforme mencionado anteriormente, discos primeiro ao seguir os passos na secção "Restaurar discos de cópia de segurança" na [escolha uma configuração de restauro de VM](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration). Depois disso, utilize o PowerShell para [criar uma VM a partir de discos restaurados](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). |
