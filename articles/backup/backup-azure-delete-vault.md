---
title: Elimine um cofre dos Serviços de Recuperação do Microsoft Azure
description: Neste artigo, aprenda a remover dependências e, em seguida, apague um cofre dos Serviços de Recuperação de Backup Azure.
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 5fcf8004cd5792b30ec57537d5d8ab0bc085dfb3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82183760"
---
# <a name="delete-an-azure-backup-recovery-services-vault"></a>Elimine um cofre de serviços de recuperação de backup azure

Este artigo descreve como apagar um cofre dos Serviços de Recuperação de [Backup Azure.](backup-overview.md) Contém instruções para remover dependências e, em seguida, apagar um cofre.

## <a name="before-you-start"></a>Antes de começar

Não é possível eliminar um cofre de Serviços de Recuperação que tenha dependências, tais como servidores protegidos ou servidores de gestão de backup, associados a ele.

- Os cofres que contêm dados de backup não podem ser eliminados (mesmo que tenha parado a proteção, mas tenha retido os dados de backup).

- Se eliminar um cofre que contenha dependências, aparece a seguinte mensagem:

  ![Apague o erro do cofre.](./media/backup-azure-delete-vault/error.png)

- Se eliminar um item protegido no local de um portal que contém dependências, aparece uma mensagem de aviso:

  ![Elimine o erro do servidor protegido.](./media/backup-azure-delete-vault/error-message.jpg)

- Se os itens de backup estiverem em estado suave mente apagado abaixo aparece a mensagem de aviso e terá de esperar até que sejam permanentemente eliminados. Para mais informações, consulte este [artigo.](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud)

   ![Apague o erro do cofre.](./media/backup-azure-delete-vault/error-message-soft-delete.png)

- Cofres que tenham contas de armazenamento registadas não podem ser apagados. Para saber como desregistar a conta, consulte [Desregistar uma conta de armazenamento](manage-afs-backup.md#unregister-a-storage-account).
  
Para eliminar o cofre, escolha o cenário que corresponde à sua configuração e siga os passos recomendados:

Cenário | Passos para remover dependências para apagar cofre |
-- | --
Tenho ficheiros e pastas no local protegidos usando o agente de backup Azure, apoiando-se no Azure. | Execute os [passos](#delete-backup-items-from-the-mars-management-console) em Eliminar itens de backup da consola de gestão MARS
Tenho máquinas no local que estão protegidas usando MABS (Microsoft Azure Backup Server) ou DPM (System Center Data Protection Manager) para O Azure | Execute os passos em Eliminar itens de backup da consola de [gestão MABS](#delete-backup-items-from-the-mabs-management-console)
Tenho itens protegidos na nuvem (por exemplo, uma máquina virtual laaS ou uma partilha de Ficheiros Azure)  | Execute os [passos](#delete-protected-items-in-the-cloud) em Eliminar itens protegidos na nuvem
Tenho protegido itens tanto nas instalações como na nuvem | Efetuar os passos em todas as seguintes secções, na seguinte ordem: <br> 1. [Eliminar itens protegidos na nuvem](#delete-protected-items-in-the-cloud)<br> 2. Eliminar itens de [backup da consola de gestão MARS](#delete-backup-items-from-the-mars-management-console) <br> 3. Eliminar itens de [backup da consola De Gestão MABS](#delete-backup-items-from-the-mabs-management-console)
Não tenho nenhum artigo protegido no local ou na nuvem; no entanto, ainda estou recebendo o erro de eliminação do Cofre | Execute os passos em [Eliminar o cofre dos Serviços de Recuperação utilizando o Gestor](#delete-the-recovery-services-vault-by-using-azure-resource-manager) de Recursos Azure <br><br> Certifique-se de que não há contas de armazenamento registadas no cofre. Para saber como desregistar a conta, consulte [Desregistar uma conta de armazenamento](manage-afs-backup.md#unregister-a-storage-account).

## <a name="delete-protected-items-in-the-cloud"></a>Eliminar itens protegidos na nuvem

Primeiro, leia a secção **[Antes de começar](#before-you-start)** para entender as dependências e o processo de eliminação do cofre.

Para parar a proteção e eliminar os dados de backup, execute os seguintes passos:

1. A partir do portal, vá ao cofre dos Serviços de **Recuperação,** e depois vá para **itens de backup.** Em seguida, escolha os itens protegidos na nuvem (por exemplo, Máquinas Virtuais Azure, Armazenamento Azure [serviço De Ficheiros Azure], ou Servidor SQL em Máquinas Virtuais Azure).

    ![Selecione o tipo de cópia de segurança.](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. Clique à direita para selecionar o item de reserva. Dependendo se o item de reserva está protegido ou não, o menu exibe o painel stop **backup** ou o painel de dados de cópia de **segurança de eliminação.**

    - Se aparecer o painel stop **backup,** **selecione Eliminar dados** de backup do menu suspenso. Introduza o nome do item de reserva (este campo é sensível a casos) e, em seguida, selecione uma razão do menu suspenso. Insira os seus comentários, se tiver algum. Em seguida, selecione **parar a cópia de segurança**.

        ![O painel stop backup.](./media/backup-azure-delete-vault/stop-backup-item.png)

    - Se aparecer o painel de dados de cópia de **segurança eliminar,** introduza o nome do item de reserva (este campo é sensível a casos) e, em seguida, selecione uma razão do menu suspenso. Insira os seus comentários, se tiver algum. Em seguida, selecione **Delete**.

         ![O painel de dados de cópia de segurança eliminar.](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

3. Verifique o ícone ![ **notificação:** O ícone notificação.](./media/backup-azure-delete-vault/messages.png) Após o final do processo, o serviço apresenta a seguinte mensagem: Parar a *cópia de segurança e apagar dados*de backup para " Backup Item *"*. *Concluída com sucesso a operação*.
4. Selecione **Refresh** no menu **'Backup Itens',** para se certificar de que o item de reserva foi eliminado.

      ![A página eliminar itens de backup.](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>Eliminar artigos protegidos nas instalações

Primeiro, leia a secção **[Antes de começar](#before-you-start)** para entender as dependências e o processo de eliminação do cofre.

1. A partir do menu do painel de instrumentos do cofre, selecione **Backup Infrastructure**.
2. Dependendo do seu cenário no local, escolha uma das seguintes opções:

      - Para o MARS, selecione **Servidores Protegidos** e, em seguida, **Agente de Backup Azure**. Em seguida, selecione o servidor que pretende eliminar.

        ![Para o MARS, selecione o seu cofre para abrir o painel de instrumentos.](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - Para MABS ou DPM, selecione **Backup Management Servers**. Em seguida, selecione o servidor que pretende eliminar.

          ![Para mABS, selecione o seu cofre para abrir o painel de instrumentos.](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. O painel **Delete** aparece com uma mensagem de aviso.

     ![O painel de apagar.](./media/backup-azure-delete-vault/delete-protected-server.png)

     Reveja a mensagem de aviso e as instruções na caixa de verificação de consentimento.
    > [!NOTE]
    >
    >- Se o servidor protegido estiver sincronizado com os serviços Do Azure e os itens de backup existirem, a caixa de verificação de consentimento apresentará o número de itens de backup dependentes e o link para visualizar os itens de backup.
    >- Se o servidor protegido não estiver sincronizado com os serviços Do Azure e os itens de backup existirem, a caixa de verificação de consentimento apresentará apenas o número de itens de backup.
    >- Se não houver itens de reserva, a caixa de verificação de consentimento pedirá a eliminação.

4. Selecione a caixa de verificação de consentimento e, em seguida, **selecione Eliminar**.

5. Verifique o ![ícone](./media/backup-azure-delete-vault/messages.png) **Notificação** eliminar dados de backup . Após o acabamento da operação, o serviço exibe a mensagem: Parar a cópia de segurança e apagar os dados de *backup para "Backup Item".* *Concluída com sucesso a operação*.
6. Selecione **Refresh** no menu **'Backup Itens',** para se certificar de que o item de reserva é eliminado.

Após o final deste processo, pode eliminar os itens de backup da consola de gestão:

- [Eliminar itens de backup da consola de gestão MARS](#delete-backup-items-from-the-mars-management-console)
- [Eliminar itens de backup da consola de gestão MABS](#delete-backup-items-from-the-mabs-management-console)

### <a name="delete-backup-items-from-the-mars-management-console"></a>Eliminar itens de backup da consola de gestão MARS

1. Abra a consola de gestão MARS, vá ao painel **DeAções** e selecione **'Backup'** de Agenda .
2. A partir da página De alteração ou paragem de uma página **de backup agendada,** selecione Parar usando esta agenda de cópia de **segurança e eliminar todas as cópias de segurança armazenadas**. Em seguida, selecione **Next**.

    ![Modifique ou pare uma cópia de segurança programada.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. A partir da página de **backup Stop,** selecione **Finish**.

    ![Parem com um reforço programado.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. É solicitado a introduzir um PIN de segurança (número de identificação pessoal), que deve gerar manualmente. Para isso, assine primeiro o portal Azure.
5. Vá ao cofre de recuperação De **serviços de** > **configurações** > **propriedades**.
6. Em **'Pin' de segurança,** selecione **Generate**. Copie este PIN. O PIN é válido por apenas cinco minutos.
7. Na consola de gestão, cola o PIN e, em seguida, selecione **OK**.

    ![Gere um PIN de segurança.](./media/backup-azure-delete-vault/security-pin.png)

8. Na página Modificar O Progresso de Cópia de **Segurança,** aparece a seguinte mensagem: Os dados de *backup eliminados serão conservados durante 14 dias. Após esse período, os dados de cópia de segurança serão eliminados permanentemente.*  

    ![Apague a infraestrutura de reserva.](./media/backup-azure-delete-vault/deleted-backup-data.png)

Depois de eliminar os itens de backup no local, siga os próximos passos do portal.

### <a name="delete-backup-items-from-the-mabs-management-console"></a>Eliminar itens de backup da consola de gestão MABS

Existem dois métodos que pode utilizar para eliminar itens de backup da consola de gestão MABS.

#### <a name="method-1"></a>Método 1

Para parar a proteção e eliminar os dados de backup, faça os seguintes passos:

1. Abra a Consola de Administrador DPM e, em seguida, selecione **Protection** na barra de navegação.
2. No painel de visualização, selecione o membro do grupo de proteção que pretende remover. Clique à direita para selecionar a opção **Stop Protection of Group Members.**
3. Na caixa de diálogo **Stop Protection,** selecione **Eliminar dados protegidos**e, em seguida, selecione a caixa de verificação **online de armazenamento Eliminar.** Em seguida, selecione **Stop Protection**.

    ![Selecione Eliminar dados protegidos do painel stop protection.](./media/backup-azure-delete-vault/delete-storage-online.png)

    O estado do membro protegido altera-se à *réplica inativa disponível*.

4. Clique no grupo de proteção inativo e selecione **Remover a proteção inativa**.

    ![Remova a proteção inativa.](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. A partir da janela **Eliminar Proteção Inativa,** selecione a caixa de verificação de **armazenamento on-line Delete** e, em seguida, selecione **OK**.

    ![Eliminar o armazenamento online.](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

#### <a name="method-2"></a>Método 2

Abra a consola de **gestão MABS.** No **método de proteção de dados Select,** limpe a caixa de verificação de **proteção on-line.**

  ![Selecione o método de proteção de dados.](./media/backup-azure-delete-vault/data-protection-method.png)

Depois de eliminar os itens de backup no local, siga os próximos passos do portal.

## <a name="delete-the-recovery-services-vault"></a>Eliminar o cofre dos Serviços de Recuperação

1. Quando todas as dependências tiverem sido removidas, desloque-se para o painel **Essentials** no menu do cofre.
2. Verifique se não existem itens de backup, servidores de gestão de backup ou itens replicados listados. Se os itens ainda aparecerem no cofre, consulte a secção Antes de [iniciar.](#before-you-start)

3. Quando não houver mais itens no cofre, **selecione Delete** no painel de instrumentos do cofre.

    ![Selecione Eliminar no painel do cofre.](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Selecione **Sim** para verificar se pretende eliminar o cofre. O cofre foi apagado. O portal regressa ao **novo** menu de serviço.

## <a name="delete-the-recovery-services-vault-by-using-powershell"></a>Elimine o cofre dos Serviços de Recuperação utilizando o PowerShell

Primeiro, leia a secção **[Antes de começar](#before-you-start)** para entender as dependências e o processo de eliminação do cofre.

Para parar a proteção e eliminar os dados de backup:

- Se estiver a utilizar a Cópia de Segurança SQL em VMs Azure e a proteção automática ativada para instâncias SQL, desative primeiro a proteção automática.

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

  [Saiba mais](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection?view=azps-2.6.0) sobre como desativar a proteção de um artigo protegido por backup Azure.

- Pare a proteção e elimine os dados de todos os itens protegidos por backup na nuvem (ex. laaS VM, Partilha de Ficheiros Azure etc.):

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

    [Saiba mais](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-2.6.0&viewFallbackFrom=azps-2.5.0) sobre a proteção contra desativações para um artigo protegido por backup.

- Para ficheiros e pastas no local protegidos utilizando o Agente de Backup Azure (MARS) a apoiar o Azure, utilize o seguinte comando PowerShell para eliminar os dados de reserva de cada módulo MARS PowerShell:

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Post onde aparece o seguinte pedido:

    *Backup do Microsoft Azure Tem a certeza de que pretende remover esta política de backup? Os dados de cópia de segurança eliminados serão conservados durante 14 dias. Após esse período, os dados de cópia de segurança serão eliminados permanentemente. <br/> Sim [A] Sim a Todos [N] Não [L] Não a Todos [S] Suspender [?] Ajuda (padrão é "Y"):*

- Para máquinas no local protegidas utilizando MABS (Microsoft Azure Backup Server) ou DPM para Azure (System Center Data Protection Manager), utilize o seguinte comando para eliminar os dados de back-up no Azure.

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Post onde aparece o seguinte pedido:

   *Backup do Microsoft Azure* Tem certeza de que quer remover esta política de reserva? Os dados de cópia de segurança eliminados serão conservados durante 14 dias. Após esse período, os dados de cópia de segurança serão eliminados permanentemente. <br/>
   Sim [A] Sim a Todos [N] Não [L] Não a Todos [S] Suspender [?] Ajuda (padrão é "Y"):*

Depois de apagar os dados de back-up, não registe quaisquer contentores no local e servidores de gestão.

- Para ficheiros e pastas no local protegidos utilizando o Agente de Backup Azure (MARS) a apoiar o Azure:

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

    [Saiba mais](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0) sobre o desinscrição de um Servidor do Windows ou de outro recipiente a partir do cofre.

- Para máquinas no local protegidas utilizando MABS (Microsoft Azure Backup Server) ou DPM para Azure (System Center Data Protection Manage:

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

    [Saiba mais](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0) sobre o desinscrição de um recipiente de gestão de backup a partir do cofre.

Depois de eliminar permanentemente os dados de back-up e desregistar todos os contentores, proceda à eliminação do cofre.

Para eliminar um cofre dos Serviços de Recuperação:

   ```PowerShell
       Remove-AzRecoveryServicesVault
      -Vault <ARSVault>
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
   ```

[Saiba mais](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault) sobre a apagar um cofre de serviços de recuperação.

## <a name="delete-the-recovery-services-vault-by-using-cli"></a>Eliminar o cofre dos Serviços de Recuperação utilizando o CLI

Primeiro, leia a secção **[Antes de começar](#before-you-start)** para entender as dependências e o processo de eliminação do cofre.

> [!NOTE]
> Atualmente, o Azure Backup CLI suporta a gestão apenas de backups Azure VM, pelo que o seguinte comando para apagar o cofre funciona apenas se o cofre contiver backups Azure VM. Não é possível eliminar um cofre utilizando o AZURE Backup CLI, se o cofre contiver qualquer objeto de reserva do tipo que não seja os VMs Azure.

Para eliminar o cofre de serviços de Recuperação existente, execute o seguinte:

- Para parar a proteção e eliminar os dados de backup

    ```azurecli
    az backup protection disable --container-name
                             --item-name
                             [--delete-backup-data {false, true}]
                             [--ids]
                             [--resource-group]
                             [--subscription]
                             [--vault-name]
                             [--yes]
    ```

    Para mais informações, consulte este [artigo.](/cli/azure/backup/protection#az-backup-protection-disable)

- Eliminar um cofre de serviços de Recuperação existente:

    ```azurecli
    az backup vault delete [--force]
                       [--ids]
                       [--name]
                       [--resource-group]
                       [--subscription]
                       [--yes]
    ```

    Para mais informações, consulte este [artigo](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest)

## <a name="delete-the-recovery-services-vault-by-using-azure-resource-manager"></a>Elimine o cofre dos Serviços de Recuperação utilizando o Gestor de Recursos Azure

Esta opção de eliminar o cofre dos Serviços de Recuperação só é recomendada se todas as dependências forem removidas e ainda tiver o erro de *eliminação*do Cofre . Experimente todas ou todas as seguintes dicas:

- A partir do painel **Essentials** no menu do cofre, verifique se não existem itens de backup, servidores de gestão de backup ou itens replicados listados. Se houver itens de reserva, consulte a secção Antes de [iniciar.](#before-you-start)
- Tente [apagar o cofre do portal](#delete-the-recovery-services-vault) de novo.
- Se todas as dependências forem removidas e ainda tiver o erro de eliminação do *Cofre,* utilize a ferramenta ARMClient para executar os seguintes passos (após a nota).

1. Vá a [chocolatey.org](https://chocolatey.org/) para descarregar e instalar o Chocolatey. Em seguida, instale o ARMClient executando o seguinte comando:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Inscreva-se na sua conta Azure e, em seguida, execute o seguinte comando:

    `ARMClient.exe login [environment name]`

3. No portal Azure, recolha o nome de grupo de subscrição e de grupo de recursos para o cofre que pretende eliminar.

Para mais informações sobre o comando ARMClient, consulte [ARMClient README](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-the-azure-resource-manager-client-to-delete-a-recovery-services-vault"></a>Use o cliente do Gestor de Recursos Azure para apagar um cofre de Serviços de Recuperação

1. Execute o seguinte comando utilizando o seu ID de subscrição, nome do grupo de recursos e nome do cofre. Se não tiver dependências, o cofre é apagado quando executa o seguinte comando:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```

2. Se o cofre não estiver vazio, receberá a seguinte mensagem de erro: O cofre não pode ser apagado, uma vez que *existem recursos existentes dentro deste cofre.* Para remover um item ou recipiente protegido dentro de um cofre, executar o seguinte comando:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. No portal Azure, certifique-se de que o cofre é apagado.

## <a name="next-steps"></a>Passos seguintes

[Saiba mais sobre cofres de Serviços de Recuperação](backup-azure-recovery-services-vault-overview.md)<br/>
[Conheça a monitorização e gestão dos cofres dos Serviços de Recuperação](backup-azure-manage-windows-server.md)
