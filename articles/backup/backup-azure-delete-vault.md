---
title: Excluir um cofre de serviços de recuperação Backup do Microsoft Azure
description: Este artigo descreve como excluir um cofre de serviços de recuperação Backup do Microsoft Azure.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: dacurwin
ms.openlocfilehash: a49449f799696ce6962afea6bdc212f658c660bd
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860364"
---
# <a name="delete-an-azure-backup-recovery-services-vault"></a>Excluir um cofre dos serviços de recuperação de backup do Azure

Este artigo descreve como excluir um cofre MARS (serviços de recuperação de [backup) do Microsoft Azure](backup-overview.md) . Ele contém instruções para remover dependências e, em seguida, excluir um cofre.


## <a name="before-you-start"></a>Antes de começar

Você não pode excluir um cofre dos serviços de recuperação que tem dependências, como servidores protegidos ou servidores de gerenciamento de backup associados a ele.

- Cofres que contêm dados de backup não podem ser excluídos (mesmo que você tenha interrompido a proteção, mas manteve os dados de backup).

- Se você excluir um cofre que contém dependências, a seguinte mensagem será exibida:

  ![Exclua o erro do cofre.](./media/backup-azure-delete-vault/error.png)

- Se você excluir um item protegido local de um portal que contém dependências, uma mensagem de aviso será exibida:

  ![Exclua o erro do servidor protegido.](./media/backup-azure-delete-vault/error-message.jpg)

  
Para excluir o cofre, escolha o cenário que corresponde à sua configuração e siga as etapas recomendadas:

Cenário | Etapas para remover dependências para excluir o cofre |
-- | --
Tenho arquivos e pastas locais protegidos usando o agente de backup do Azure, fazendo backup no Azure | Execute as etapas em [excluir itens de backup do console de gerenciamento do Mars](#delete-backup-items-from-the-mars-management-console)
Tenho computadores locais protegidos usando o MABS (Backup do Microsoft Azure Server) ou o DPM (System Center Data Protection Manager) para o Azure | Execute as etapas em [excluir itens de backup do console de gerenciamento do mAbs](#delete-backup-items-from-the-mabs-management-console)
Eu protegi itens na nuvem (por exemplo, uma máquina virtual laaS ou um compartilhamento de arquivos do Azure)  | Execute as etapas em [excluir itens protegidos na nuvem](#delete-protected-items-in-the-cloud)
Eu protegi itens no local e na nuvem | Execute as etapas em todas as seções a seguir, na seguinte ordem: <br> 1. [Excluir itens protegidos na nuvem](#delete-protected-items-in-the-cloud)<br> 2. [Excluir itens de backup do console de gerenciamento do MARS](#delete-backup-items-from-the-mars-management-console) <br> 3. [Excluir itens de backup do console de gerenciamento do MABS](#delete-backup-items-from-the-mabs-management-console)
Não tenho itens protegidos no local ou na nuvem; no entanto, ainda estou recebendo o erro de exclusão do cofre | Execute as etapas em [excluir o cofre dos serviços de recuperação usando Azure Resource Manager](#delete-the-recovery-services-vault-by-using-azure-resource-manager)


## <a name="delete-protected-items-in-the-cloud"></a>Excluir itens protegidos na nuvem

Primeiro, leia a seção **[antes de começar](#before-you-start)** para entender as dependências e o processo de exclusão de cofre.

Para interromper a proteção e excluir os dados de backup, execute as seguintes etapas:

1. No portal, vá para **cofre dos serviços de recuperação**e vá para **itens de backup**. Em seguida, escolha os itens protegidos na nuvem (por exemplo, máquinas virtuais do Azure, armazenamento do Azure [serviço de arquivos do Azure] ou SQL Server em máquinas virtuais do Azure).

    ![Selecione o tipo de backup.](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. Clique com o botão direito do mouse para selecionar o item de backup. Dependendo se o item de backup está protegido ou não, o menu exibe o painel **parar backup** ou o painel **excluir dados de backup** .

    - Se o painel **parar backup** for exibido, selecione **excluir dados de backup** no menu suspenso. Insira o nome do item de backup (esse campo diferencia maiúsculas de minúsculas) e, em seguida, selecione um motivo no menu suspenso. Insira seus comentários, se houver. Em seguida, selecione **parar backup**.

        ![O painel parar backup.](./media/backup-azure-delete-vault/stop-backup-item.png)

    - Se o painel **excluir dados de backup** for exibido, insira o nome do item de backup (esse campo diferencia maiúsculas de minúsculas) e, em seguida, selecione um motivo no menu suspenso. Insira seus comentários, se houver. Em seguida, selecione **excluir**. 

         ![O painel excluir dados de backup.](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. Verifique o ícone de **notificação** : ![O ícone de notificação.](./media/backup-azure-delete-vault/messages.png) Depois que o processo for concluído, o serviço exibirá a seguinte mensagem: Parando *backup e excluindo dados de backup para "* Item de backup *"* . *Operação concluída com êxito*.
6. Selecione **Atualizar** no menu **itens de backup** para verificar se o item de backup foi excluído.

      ![A página excluir itens de backup.](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>Excluir itens protegidos localmente

Primeiro, leia a seção **[antes de começar](#before-you-start)** para entender as dependências e o processo de exclusão de cofre.

1. No menu do painel do cofre, selecione **infraestrutura de backup**.
2. Dependendo do seu cenário local, escolha uma das seguintes opções:

      - Para MARS, selecione **servidores protegidos** e o **agente de backup do Azure**. Em seguida, selecione o servidor que você deseja excluir. 

        ![Para MARS, selecione seu cofre para abrir seu painel.](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - Para MABS ou DPM, selecione **backup servidores de gerenciamento**. Em seguida, selecione o servidor que você deseja excluir. 


          ![Para MABS, selecione seu cofre para abrir seu painel.](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. O painel **excluir** é exibido com uma mensagem de aviso.

     ![O painel excluir.](./media/backup-azure-delete-vault/delete-protected-server.png)

     Examine a mensagem de aviso e as instruções na caixa de seleção consentimento.
    > [!NOTE]
    >- Se o servidor protegido for sincronizado com os serviços do Azure e os itens de backup existirem, a caixa de seleção consentimento exibirá o número de itens de backup dependentes e o link para exibir os itens de backup.
    >- Se o servidor protegido não estiver sincronizado com os serviços do Azure e os itens de backup existirem, a caixa de seleção de consentimento exibirá apenas o número de itens de backup.
    >- Se não houver nenhum item de backup, a caixa de seleção de consentimento solicitará a exclusão.

4. Marque a caixa de seleção consentimento e, em seguida, selecione **excluir**.


5. Verifique o ícone ![de notificação excluir dados](./media/backup-azure-delete-vault/messages.png)de backup. Depois que a operação for concluída, o serviço exibirá a mensagem: *Parando backup e excluindo dados de backup para "item de backup".* *Operação concluída com êxito*.
6. Selecione **Atualizar** no menu **itens de backup** para verificar se o item de backup foi excluído.

Após a conclusão desse processo, você pode excluir os itens de backup do console de gerenciamento:
    
   - [Excluir itens de backup do console de gerenciamento do MARS](#delete-backup-items-from-the-mars-management-console)
    - [Excluir itens de backup do console de gerenciamento do MABS](#delete-backup-items-from-the-mabs-management-console)


### <a name="delete-backup-items-from-the-mars-management-console"></a>Excluir itens de backup do console de gerenciamento do MARS

1. Abra o console de gerenciamento do MARS, vá para o painel **ações** e selecione **agendar backup**.
2. Na página **modificar ou parar um backup agendado** , selecione **parar de usar este agendamento de backup e excluir todos os backups armazenados**. Em seguida, selecione **Seguinte**.

    ![Modificar ou parar um backup agendado.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Na página **parar um backup agendado** , selecione **concluir**.

    ![Interromper um backup agendado.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. Você será solicitado a inserir um PIN de segurança (número de identificação pessoal), que deve ser gerado manualmente. Para fazer isso, primeiro entre no portal do Azure.
5. Vá para **serviços de recuperação** > **configurações** > do cofre**Propriedades**.
6. Em **PIN de segurança**, selecione **gerar**. Copie este PIN. O PIN é válido por apenas cinco minutos.
7. No console de gerenciamento, Cole o PIN e selecione **OK**.

    ![Gerar um PIN de segurança.](./media/backup-azure-delete-vault/security-pin.png)

8. Na página **Modificar progresso do backup** , a seguinte mensagem é exibida: *Os dados de backup excluídos serão mantidos por 14 dias. Após esse período, os dados de backup serão excluídos permanentemente.*  

    ![Exclua a infraestrutura de backup.](./media/backup-azure-delete-vault/deleted-backup-data.png)

Depois de excluir os itens de backup locais, siga as próximas etapas do Portal.

### <a name="delete-backup-items-from-the-mabs-management-console"></a>Excluir itens de backup do console de gerenciamento do MABS

Há dois métodos que você pode usar para excluir itens de backup do console de gerenciamento do MABS.

#### <a name="method-1"></a>Método 1
Para interromper a proteção e excluir os dados de backup, execute as seguintes etapas:

1.  Abra o Console do Administrador do DPM e, em seguida, selecione **proteção** na barra de navegação.
2.  No painel de exibição, selecione o membro do grupo de proteção que você deseja remover. Clique com o botão direito do mouse para selecionar a opção **parar proteção de membros do grupo** .
3.  Na caixa de diálogo **parar proteção** , selecione **excluir dados protegidos**e marque a caixa de seleção **Excluir armazenamento online** . Em seguida, selecione **parar proteção**.

    ![Selecione excluir dados protegidos no painel parar proteção.](./media/backup-azure-delete-vault/delete-storage-online.png)

    O status do membro protegido é alterado para *réplica inativa disponível*.

4. Clique com o botão direito do mouse no grupo de proteção inativo e selecione **remover proteção inativa**.

    ![Remova a proteção inativa.](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. Na janela **excluir proteção inativa** , marque a caixa de seleção **Excluir armazenamento online** e selecione **OK**.

    ![Exclua o armazenamento online.](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

#### <a name="method-2"></a>Método 2
Abra o console de **Gerenciamento do mAbs** . Em **selecionar método de proteção de dados**, desmarque a caixa de seleção **desejo proteção online** .

  ![Selecione o método de proteção de dados.](./media/backup-azure-delete-vault/data-protection-method.png)

Depois de excluir os itens de backup locais, siga as próximas etapas do Portal.


## <a name="delete-the-recovery-services-vault"></a>Eliminar o cofre dos Serviços de Recuperação

1. Quando todas as dependências tiverem sido removidas, role até o painel **Essentials** no menu do cofre.
2. Verifique se não há itens de backup, servidores de gerenciamento de backup ou itens replicados listados. Se os itens ainda aparecerem no cofre, consulte a seção [antes de começar](#before-you-start) .

3. Quando não houver mais itens no cofre, selecione **excluir** no painel do cofre.

    ![Selecione Excluir no painel do cofre.](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Selecione **Sim** para verificar se deseja excluir o cofre. O cofre é excluído. O portal retorna para o **novo** menu de serviço.

## <a name="delete-the-recovery-services-vault-by-using-powershell"></a>Excluir o cofre dos serviços de recuperação usando o PowerShell

Primeiro, leia a seção **[antes de começar](#before-you-start)** para entender as dependências e o processo de exclusão de cofre.

Para interromper a proteção e excluir os dados de backup:

- Se você estiver usando o SQL no backup de VMs do Azure e tiver habilitado a proteção automática para instâncias do SQL, primeiro desabilite a proteção automática.

    ```PowerShell
        Disable-AzRecoveryServicesBackupAutoProtection 
           [-InputItem] <ProtectableItemBase> 
           [-BackupManagementType] <BackupManagementType> 
           [-WorkloadType] <WorkloadType> 
           [-PassThru] 
           [-VaultId <String>] 
           [-DefaultProfile <IAzureContextContainer>] 
           [-WhatIf] 
           [-Confirm] 
           [<CommonParameters>] 
    ```

  [Saiba mais](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection?view=azps-2.6.0) sobre como desabilitar a proteção para um item protegido pelo backup do Azure 

- Interromper a proteção e excluir dados de todos os itens protegidos por backup na nuvem (por exemplo, VM laaS, compartilhamento de arquivos do Azure, etc.):

    ```PowerShell
       Disable-AzRecoveryServicesBackupProtection 
       [-Item] <ItemBase> 
       [-RemoveRecoveryPoints] 
       [-Force] 
       [-VaultId <String>] 
       [-DefaultProfile <IAzureContextContainer>] 
       [-WhatIf] 
       [-Confirm] 
       [<CommonParameters>] 
    ```
    [Saiba mais](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-2.6.0&viewFallbackFrom=azps-2.5.0) sobre desabilitar a proteção para um item protegido por backup. 

- Para arquivos e pastas locais protegidos usando o agente de backup do Azure (MARS) fazendo backup no Azure, use o seguinte comando do PowerShell para excluir os dados de backup de cada módulo MARS PowerShell:

    ```
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Poste o seguinte prompt apareceria:
     
    *Backup do Microsoft Azure tem certeza de que deseja remover esta política de backup? Os dados de backup excluídos serão mantidos por 14 dias. Após esse período, os dados de backup serão excluídos permanentemente. <br/> [Y] Sim [A] Sim para todos [N] não [L] não para todos [S] suspender [?] Ajuda (o padrão é "Y"):*


- Para computadores locais protegidos usando o MABS (Backup do Microsoft Azure Server) ou o DPM no Azure (System Center Data Protection Manager), use o comando a seguir para excluir os dados de backup no Azure.

    ```
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin> 
    ```

    Poste o seguinte prompt apareceria: 
         
   *Backup do Microsoft Azure tem certeza de que deseja remover esta política de backup? Os dados de backup excluídos serão mantidos por 14 dias. Após esse período, os dados de backup serão excluídos permanentemente. <br/> [Y] Sim [A] Sim para todos [N] não [L] não para todos [S] suspender [?] Ajuda (o padrão é "Y"):*

Depois de excluir os dados de backup, cancele o registro de todos os contêineres locais e servidores de gerenciamento. 

- Para arquivos e pastas locais protegidos usando o MARS (agente de backup do Azure) fazendo backup no Azure:

    ```PowerShell
    Unregister-AzRecoveryServicesBackupContainer 
              [-Container] <ContainerBase> 
              [-PassThru] 
              [-VaultId <String>] 
              [-DefaultProfile <IAzureContextContainer>] 
              [-WhatIf] 
              [-Confirm] 
              [<CommonParameters>] 
    ```
    [Saiba mais](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0) sobre o cancelamento do registro de um Windows Server ou outro contêiner do cofre. 

- Para computadores locais protegidos usando o MABS (servidor de Backup do Microsoft Azure) ou o DPM para o Azure (proteção de dados do System Center, gerencie:

    ```PowerShell
        Unregister-AzRecoveryServicesBackupManagementServer
          [-AzureRmBackupManagementServer] <BackupEngineBase>
          [-PassThru]
          [-VaultId <String>]
          [-DefaultProfile <IAzureContextContainer>]
          [-WhatIf]
          [-Confirm]
          [<CommonParameters>]
    ```

    [Saiba mais](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0) sobre o cancelamento do registro de um contêiner de gerenciamento de backup do cofre.

Depois de excluir permanentemente os dados de backup e cancelar o registro de todos os contêineres, continue para excluir o cofre. 

Para excluir um cofre dos serviços de recuperação: 

   ```PowerShell
       Remove-AzRecoveryServicesVault 
      -Vault <ARSVault> 
      [-DefaultProfile <IAzureContextContainer>] 
      [-WhatIf] 
      [-Confirm] 
      [<CommonParameters>]        
   ```

[Saiba mais](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault) sobre como excluir um cofre dos serviços de recuperação. 

## <a name="delete-the-recovery-services-vault-by-using-cli"></a>Excluir o cofre dos serviços de recuperação usando a CLI

Primeiro, leia a seção **[antes de começar](#before-you-start)** para entender as dependências e o processo de exclusão de cofre.

> [!NOTE]
> Atualmente, a CLI do backup do Azure dá suporte ao gerenciamento apenas de backups de VM do Azure, portanto, o seguinte comando para excluir o cofre funciona apenas se o cofre contiver backups de VM do Azure. Você não poderá excluir um cofre usando a CLI de backup do Azure, se o cofre contiver qualquer item de backup do tipo diferente de VMs do Azure. 

Para excluir o cofre de serviços de recuperação existente, execute o seguinte: 

- Para interromper a proteção e excluir os dados de backup 

    ```CLI
    az backup protection disable --container-name 
                             --item-name 
                             [--delete-backup-data {false, true}] 
                             [--ids] 
                             [--resource-group] 
                             [--subscription] 
                             [--vault-name] 
                             [--yes] 
    ```

    Para obter mais informações, consulte este [artigo](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable.). 

- Excluir um cofre de serviços de recuperação existente: 

    ```CLI
    az backup vault delete [--force] 
                       [--ids] 
                       [--name] 
                       [--resource-group] 
                       [--subscription] 
                       [--yes] 
    ```

    Para obter mais informações, consulte este [artigo](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest) 

## <a name="delete-the-recovery-services-vault-by-using-azure-resource-manager"></a>Excluir o cofre dos serviços de recuperação usando Azure Resource Manager

Esta opção para excluir o cofre dos serviços de recuperação é recomendada somente se todas as dependências forem removidas e você ainda estiver obtendo o *erro de exclusão do cofre*. Tente uma ou todas as seguintes dicas:

- No painel **Essentials** no menu do cofre, verifique se não há itens de backup, servidores de gerenciamento de backup ou itens replicados listados. Se houver itens de backup, consulte a seção [antes de começar](#before-you-start) .
- Tente [excluir o cofre do portal](#delete-the-recovery-services-vault) novamente.
- Se todas as dependências forem removidas e você ainda estiver recebendo o *erro de exclusão do cofre*, use a ferramenta ARMClient para executar as etapas a seguir (após a observação).

1. Acesse [Chocolatey.org](https://chocolatey.org/) para baixar e instalar o Chocolatey. Em seguida, instale o ARMClient executando o seguinte comando:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Entre em sua conta do Azure e, em seguida, execute o seguinte comando:

    `ARMClient.exe login [environment name]`

3. Na portal do Azure, reúna a ID da assinatura e o nome do grupo de recursos para o cofre que você deseja excluir.

Para obter mais informações sobre o comando ARMClient, consulte [ARMCLIENT README](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-the-azure-resource-manager-client-to-delete-a-recovery-services-vault"></a>Usar o cliente Azure Resource Manager para excluir um cofre dos serviços de recuperação

1. Execute o comando a seguir usando sua ID de assinatura, o nome do grupo de recursos e o nome do cofre. Se você não tiver nenhuma dependência, o cofre será excluído quando você executar o seguinte comando:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
2. Se o cofre não estiver vazio, você receberá a seguinte mensagem de erro: *O cofre não pode ser excluído, pois há recursos existentes neste cofre.* Para remover um contêiner ou item protegido em um cofre, execute o seguinte comando:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. No portal do Azure, verifique se o cofre foi excluído.


## <a name="next-steps"></a>Passos seguintes

[Saiba mais sobre cofres dos serviços de recuperação](backup-azure-recovery-services-vault-overview.md)<br/>
[Saiba mais sobre como monitorar e gerenciar cofres de serviços de recuperação](backup-azure-manage-windows-server.md)
