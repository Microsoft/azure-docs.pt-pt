---
title: Apagar um cofre dos Serviços de Recuperação do Microsoft Azure
description: Neste artigo, aprenda a remover dependências e, em seguida, elimine um cofre dos Serviços de Recuperação de Backup Azure.
ms.topic: conceptual
ms.date: 06/04/2020
ms.openlocfilehash: 1526e9aeef1574f261dcb1a58ee12a12fbf08866
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864963"
---
# <a name="delete-an-azure-backup-recovery-services-vault"></a>Apagar um cofre dos Serviços de Recuperação de Backup da Azure

Este artigo descreve como apagar um cofre dos Serviços de Recuperação [de Backup Azure.](backup-overview.md) Contém instruções para remover dependências e depois apagar um cofre.

## <a name="before-you-start"></a>Antes de começar

Não pode eliminar um cofre dos Serviços de Recuperação com qualquer uma das seguintes dependências:

- Não é possível apagar um cofre que contenha fontes de dados protegidas (por exemplo, IaaS VMs, bases de dados SQL, partilhas de ficheiros Azure).
- Não é possível apagar um cofre que contenha dados de reserva. Depois de eliminar os dados em cópia de segurança, entrará no estado de eliminação recuperável.
- Não é possível apagar um cofre que contenha dados de backup no estado de eliminação suave.
- Não se pode apagar um cofre que tenha contas de armazenamento registadas.

Se tentar apagar o cofre sem remover as dependências, encontrará uma das seguintes mensagens de erro:

- Não é possível eliminar o cofre, pois existem recursos dentro do cofre. Certifique-se de que não existem itens de backup, servidores protegidos ou servidores de gestão de cópias de segurança associados a este cofre. Desmarça os seguintes recipientes associados a este cofre antes de proceder à eliminação.

- O cofre dos Serviços de Recuperação não pode ser eliminado, uma vez que existem muitos itens de cópias de segurança no estado de eliminação de forma recuperável no cofre. Os itens apagados suaves são permanentemente eliminados após 14 dias de operação de eliminação. Tente a eliminação do cofre depois de os itens de reserva serem permanentemente apagados e não há nenhum item em estado de eliminação suave deixado no cofre. Para obter mais informações, consulte [Soft delete for Azure Backup](./backup-azure-security-feature-cloud.md).

## <a name="proper-way-to-delete-a-vault"></a>Maneira apropriada de apagar um cofre

>[!WARNING]
>A seguinte operação é destrutiva e não pode ser desfeita. Todos os dados de backup e itens de backup associados ao servidor protegido serão permanentemente eliminados. Avance com cuidado.

Para eliminar corretamente um cofre, deve seguir os passos desta ordem:

- **Passo 1**: Desative a função de eliminação suave. [Consulte aqui](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) os passos para desativar a exclusão suave.

- **Passo 2**: Depois de desativar a eliminação suave, verifique se existem itens anteriormente restantes no estado de eliminação suave. Se houver itens em estado de eliminação suave, então tem de *os desembolar* e *apagar* novamente. [Siga estes passos](./backup-azure-security-feature-cloud.md#permanently-deleting-soft-deleted-backup-items) para encontrar itens de eliminação suave e elimine-os permanentemente.

- **Passo 3**: Deve verificar todos os três locais seguintes para verificar se existem itens protegidos:

  - **Itens protegidos em nuvem**: Vá ao menu do painel de abóbadas > **itens de reserva**. Todos os itens listados aqui devem ser removidos com **Backup stop** ou **eliminar dados de backup,** juntamente com os seus dados de cópia de segurança.  [Siga estes passos](#delete-protected-items-in-the-cloud) para remover estes itens.
  - **Sql Server instance**: Vá ao menu do painel de abóbadas > Servidores Protegidos de **Infraestruturas** de Reserva  >  . Em Servidores Protegidos, selecione o servidor para não registar. Para apagar o cofre, tem de não registar todos os servidores. Clique com o botão direito no servidor protegido e selecione **Unregister**.
  - **Servidores protegidos por MARS**: Vá ao menu do painel de abóbadas > servidores protegidos por **infraestruturas** de backup  >  . Se tiver servidores protegidos pela MARS, todos os itens listados aqui devem ser eliminados juntamente com os seus dados de cópia de segurança. [Siga estes passos](#delete-protected-items-on-premises) para eliminar servidores protegidos pela MARS.
  - **Servidores de gestão MABS ou DPM**: Vá ao menu do painel de abóbadas > **Backup Infrastructure** Backup  >  **Management Servers**. Se tiver DPM ou Azure Backup Server (MABS), todos os itens listados aqui devem ser eliminados ou não registados juntamente com os seus dados de backup. [Siga estes passos](#delete-protected-items-on-premises) para eliminar os servidores de gestão.

- **Passo 4**: Deve assegurar-se de que todas as contas de armazenamento registadas são eliminadas. Aceda ao menu do painel de abóbadas > Contas de Armazenamento **de Infraestruturas de Backup**  >  . Se tem contas de armazenamento listadas aqui, então deve não registar todas. Para aprender a desregistralmente da conta, consulte [Não registar uma conta de armazenamento](manage-afs-backup.md#unregister-a-storage-account).
- **Passo 5**: Certifique-se de que não existem pontos finais privados criados para o cofre. Vá ao menu do painel de instrumentos Vault > **As ligações de ponto final privados** em 'Definições' > se o cofre tiver alguma ligação de ponto final privado criada ou tentada ser criada, certifique-se de que são removidas antes de prosseguir com a eliminação do cofre. 

Depois de ter concluído estes passos, pode continuar a [apagar o cofre.](#delete-the-recovery-services-vault)

Se não tiver quaisquer itens protegidos no local ou na nuvem, mas ainda estiver a obter o erro de eliminação do cofre, execute os passos no [cofre delete the Recovery Services usando o Azure Resource Manager](#delete-the-recovery-services-vault-by-using-azure-resource-manager)

## <a name="delete-protected-items-in-the-cloud"></a>Eliminar itens protegidos na nuvem

Primeiro, leia a secção **[Antes de começar](#before-you-start)** a entender as dependências e o processo de eliminação do cofre.

Para parar a proteção e eliminar os dados de cópia de segurança, execute os seguintes passos:

1. A partir do portal, vá ao **cofre dos Serviços de Recuperação** e, em seguida, vá para **itens de reserva**. Em seguida, na lista **do Tipo de Gestão de Cópias** de Segurança, selecione os itens protegidos na nuvem (por exemplo, Máquinas Virtuais Azure, Armazenamento Azure [o serviço de Ficheiros Azure], ou SQL Server em Máquinas Virtuais Azure).

    ![Selecione o tipo de cópia de segurança.](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. Você verá uma lista de todos os itens para a categoria. Clique com o botão direito para selecionar o item de cópia de segurança. Dependendo se o item de cópia de segurança está protegido ou não, o menu apresenta o painel **de backup stop** ou o painel de **dados de cópia de segurança de eliminar.**

    - Se aparecer o **painel de backup stop,** selecione **Eliminar dados** de cópia de segurança do menu suspenso. Introduza o nome do item de cópia de segurança (este campo é sensível a casos e, em seguida, selecione uma razão do menu suspenso. Insira os seus comentários, se tiver algum. Em seguida, **selecione Stop backup**.

        ![O painel de apoio de paragem.](./media/backup-azure-delete-vault/stop-backup-item.png)

    - Se aparecer o painel **de dados de cópia de segurança,** insira o nome do item de cópia de segurança (este campo é sensível a casos e, em seguida, selecione uma razão do menu suspenso. Insira os seus comentários, se tiver algum. Em seguida, **selecione Delete**.

         ![O painel de dados de cópia de segurança delete.](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

   Esta opção elimina as cópias de segurança programadas, também elimina cópias de segurança a pedido.
3. Verifique o ícone **de Notificação:** ![ O ícone de Notificação.](./media/backup-azure-delete-vault/messages.png) Após o fim do processo, o serviço apresenta a seguinte mensagem: *Parar a cópia de segurança e eliminar dados de backup para "* Item de cópia de segurança *".* *Concluída com sucesso a operação.*
4. Selecione **Refresh** no menu **Itens de cópia de segurança,** para se certificar de que o item de cópia de segurança foi eliminado.

      ![A página 'Eliminar Itens de Cópia de Segurança'.](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>Eliminar artigos protegidos nas instalações

Primeiro, leia a secção **[Antes de começar](#before-you-start)** a entender as dependências e o processo de eliminação do cofre.

1. A partir do menu do painel de instrumentos do cofre, selecione **Infraestrutura de backup**.
2. Dependendo do seu cenário no local, escolha uma das seguintes opções:

      - Para o MARS, selecione **Servidores Protegidos** e, em seguida,  **Agente de Backup Azure**. Em seguida, selecione o servidor que pretende eliminar.

        ![Para o MARS, selecione o seu cofre para abrir o seu painel de instrumentos.](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - Para MABS ou DPM, selecione **Servidores de Gestão de Cópias de Segurança**. Em seguida, selecione o servidor que pretende eliminar.

          ![Para MABS ou DPM, selecione o seu cofre para abrir o seu painel de instrumentos.](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. O **painel delete** aparece com uma mensagem de aviso.

     ![O painel de exclusão.](./media/backup-azure-delete-vault/delete-protected-server.png)

     Reveja a mensagem de aviso e as instruções na caixa de verificação de consentimento.
    > [!NOTE]
    >
    >- Se o servidor protegido estiver sincronizado com os serviços Azure e existirem itens de backup, a caixa de verificação de consentimento apresentará o número de itens de backup dependentes e o link para visualizar os itens de cópia de segurança.
    >- Se o servidor protegido não estiver sincronizado com os serviços Azure e existirem itens de backup, a caixa de verificação de consentimento apresentará apenas o número de itens de cópia de segurança.
    >- Se não houver itens de reserva, a caixa de verificação de consentimento pedirá a eliminação.

4. Selecione a caixa de verificação de consentimento e, em seguida, **selecione Delete**.

5. Verifique o ícone **de Notificação** ![ apagar dados de cópia de segurança ](./media/backup-azure-delete-vault/messages.png) . Após o fim da operação, o serviço exibe a mensagem: *Parar a cópia de segurança e eliminar dados de backup para "Item de cópia de segurança".* *Concluída com sucesso a operação.*
6. Selecione **Refresh** no menu **Itens de cópia de segurança,** para se certificar de que o item de cópia de segurança é eliminado.

>[!NOTE]
>Se eliminar um item protegido no local de um portal que contenha dependências, receberá um aviso dizendo "Eliminar o registo do servidor é uma operação destrutiva e não pode ser desfeito. Todos os dados de backup (pontos de recuperação necessários para restaurar os dados) e itens de backup associados ao servidor protegido serão permanentemente eliminados."

Após o final deste processo, pode eliminar os itens de backup da consola de gestão:

- [Eliminar itens de backup da consola de gestão MARS](#delete-backup-items-from-the-mars-management-console)
- [Eliminar itens de backup da consola de gestão MABS ou DPM](#delete-backup-items-from-the-mabs-or-dpm-management-console)

### <a name="delete-backup-items-from-the-mars-management-console"></a>Eliminar itens de backup da consola de gestão MARS

>[!NOTE]
>Se apagar ou perder a máquina de origem sem parar a cópia de segurança, a próxima cópia de segurança programada falhará. O antigo ponto de recuperação expira de acordo com a política, mas o último ponto de recuperação único é sempre mantido até parar a cópia de segurança e apagar os dados. Pode fazê-lo seguindo os passos [desta secção.](#delete-protected-items-on-premises)

1. Abra a consola de gestão MARS, vá ao painel **de Ações** e selecione **Agendar Backup**.
2. A partir da página **Modificar ou Parar uma** página de Backup agendada, selecione Parar de usar este horário de backup e eliminar todas as **cópias de segurança armazenadas**. Em seguida, selecione **Seguinte**.

    ![Modifique ou pare uma cópia de segurança programada.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. A partir da página **Stop a Scheduled Backup,** selecione **Finish**.

    ![Pare com um reforço programado.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. É-lhe pedido que introduza um PIN de segurança (número de identificação pessoal), que deve gerar manualmente. Para isso, primeiro inscreva-se no portal Azure.
5. Vá para **o cofre dos serviços de recuperação**  >  **Propriedades de**  >  **configurações**.
6. Em **Pin de segurança,** selecione **Gerar**. Copie este PIN. O PIN é válido por apenas cinco minutos.
7. Na consola de gestão, cole o PIN e, em seguida, selecione **OK**.

    ![Gere um PIN de segurança.](./media/backup-azure-delete-vault/security-pin.png)

8. Na página **Modificar Backup Progress** aparece: Os dados de cópia de *segurança eliminados serão retidos durante 14 dias. Após esse período, os dados de backup serão permanentemente eliminados.*  

    ![Apague a infraestrutura de reserva.](./media/backup-azure-delete-vault/deleted-backup-data.png)

Depois de eliminar os itens de backup no local, siga os próximos passos a partir do portal.

### <a name="delete-backup-items-from-the-mabs-or-dpm-management-console"></a>Eliminar itens de backup da consola de gestão MABS ou DPM

>[!NOTE]
>Se apagar ou perder a máquina de origem sem parar a cópia de segurança, a próxima cópia de segurança programada falhará. O antigo ponto de recuperação expira de acordo com a política, mas o último ponto de recuperação único é sempre mantido até parar a cópia de segurança e apagar os dados. Pode fazê-lo seguindo os passos [desta secção.](#delete-protected-items-on-premises)

Existem dois métodos que pode utilizar para eliminar itens de backup da consola de gestão MABS ou DPM.

#### <a name="method-1"></a>Método 1

Para parar a proteção e eliminar dados de backup, faça os seguintes passos:

1. Abra a consola de administrador DPM e, em seguida, **selecione Proteção** na barra de navegação.
2. No painel de visualização, selecione o membro do grupo de proteção que pretende remover. Clique com o botão direito para selecionar a opção **Stop Protection of Group Members.**
3. A partir da caixa de diálogo **Stop Protection,** selecione **Eliminar dados protegidos** e, em seguida, selecione a caixa de verificação **de armazenamento online Delete.** Em seguida, **selecione Stop Protection**.

    ![Selecione Eliminar dados protegidos do painel stop Protection.](./media/backup-azure-delete-vault/delete-storage-online.png)

    O estado do membro protegido altera a *réplica inativa disponível*.

4. Clique com o botão direito no grupo de proteção inativo e **selecione Remover a proteção inativa**.

    ![Remover proteção inativa.](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. A partir da janela **Eliminar Proteção Inativa,** selecione a caixa de **verificação de armazenamento on-line Delete** e, em seguida, selecione **OK**.

    ![Apague o armazenamento online.](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

#### <a name="method-2"></a>Método 2

Abra a **gestão MABS** ou a consola **de gestão DPM.** De acordo com **o método de proteção de dados Select,** limpe a caixa  **de verificação de proteção** on-line.

  ![Selecione o método de proteção de dados.](./media/backup-azure-delete-vault/data-protection-method.png)

Depois de eliminar os itens de backup no local, siga os próximos passos a partir do portal.

## <a name="delete-the-recovery-services-vault"></a>Eliminar o cofre dos Serviços de Recuperação

1. Quando todas as dependências tiverem sido **removidas,** percorra o painel Essentials no menu do cofre.
2. Verifique se não existem itens de backup, servidores de gestão de backup ou itens replicados listados. Se os itens ainda aparecerem no cofre, consulte a secção Antes de [iniciar.](#before-you-start)

3. Quando não houver mais itens no cofre, **selecione Delete** no painel de abóbada.

    ![Selecione Excluir no painel de instrumentos do cofre.](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Selecione **Sim** para verificar se deseja apagar o cofre. O cofre está apagado. O portal regressa ao menu de serviço **Novo.**

## <a name="delete-the-recovery-services-vault-by-using-powershell"></a>Elimine o cofre dos Serviços de Recuperação utilizando o PowerShell

Primeiro, leia a secção **[Antes de começar](#before-you-start)** a entender as dependências e o processo de eliminação do cofre.

Para parar a proteção e eliminar os dados de cópia de segurança:

- Se estiver a utilizar a cópia de segurança SQL em Azure VMs e ativar a proteção automática para casos DE SQL, desative primeiro a proteção automática.

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

  [Saiba mais](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection) sobre como desativar a proteção de um artigo protegido por cópia de segurança Azure.

- Pare a proteção e elimine os dados de todos os itens protegidos por cópia de segurança na nuvem (por exemplo: IaaS VM, partilha de ficheiros Azure, etc.)

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

    [Saiba mais](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection)   sobre desativar a proteção de um item protegido por cópia de segurança.

- Para ficheiros e pastas no local protegidos utilizando o Agente de Backup Azure (MARS) que está a fazer backup até ao Azure, utilize o seguinte comando PowerShell para eliminar os dados de backup de cada módulo MARS PowerShell:

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Posteriormente, aparecerá o seguinte pedido:

    *Backup do Microsoft Azure Tem a certeza de que pretende remover esta política de backup? Os dados de cópias de segurança eliminados serão conservados durante 14 dias. Após esse período, os dados de backup serão permanentemente eliminados. <br/> [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspender [?] Ajuda (padrão é "Y"):*

- Para máquinas no local protegidas utilizando MABS (Microsoft Azure Backup Server) ou DPM (System Center Data Protection Manager) para Azure, utilize o seguinte comando para eliminar os dados de backup em Azure.

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Posteriormente, aparecerá o seguinte pedido:

   *Backup microsoft Azure* Tem a certeza de que pretende remover esta política de reserva? Os dados de cópias de segurança eliminados serão conservados durante 14 dias. Após esse período, os dados de cópia de segurança serão eliminados permanentemente. <br/>
   [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspender [?] Ajuda (padrão é "Y"):*

Depois de eliminar os dados de back-up, desmarcem quaisquer contentores no local e servidores de gestão.

- Para ficheiros e pastas no local protegidos utilizando o Agente de Backup Azure (MARS) que está a fazer backup até ao Azure:

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

    [Saiba mais](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer) sobre a desinsuserção de um Servidor Windows ou outro recipiente a partir do cofre.

- Para máquinas no local protegidas utilizando MABS (Microsoft Azure Backup Server) ou DPM to Azure (System Center Data Protection Manage:

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

    [Saiba mais](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer) sobre a desinsusagem de um recipiente de gestão de reserva do cofre.

Depois de eliminar permanentemente os dados de retenção e desinscrever todos os contentores, proceda à eliminação do cofre.

Para eliminar um cofre dos Serviços de Recuperação:

   ```PowerShell
       Remove-AzRecoveryServicesVault
      -Vault <ARSVault>
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
   ```

[Saiba mais](/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault) sobre a eliminação de um cofre dos Serviços de Recuperação.

## <a name="delete-the-recovery-services-vault-by-using-cli"></a>Elimine o cofre dos Serviços de Recuperação utilizando o CLI

Primeiro, leia a secção **[Antes de começar](#before-you-start)** a entender as dependências e o processo de eliminação do cofre.

> [!NOTE]
> Atualmente, o Azure Backup CLI suporta a gestão apenas de backups Azure VM, pelo que o seguinte comando para apagar o cofre funciona apenas se o cofre contiver backups Azure VM. Não é possível apagar um cofre utilizando o CLI de backup Azure, se o cofre contiver qualquer produto de backup do tipo diferente dos VMs Azure.

Para eliminar o cofre dos Serviços de Recuperação existentes, execute os seguintes passos:

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

- Eliminar um cofre dos Serviços de Recuperação existente:

    ```azurecli
    az backup vault delete [--force]
                       [--ids]
                       [--name]
                       [--resource-group]
                       [--subscription]
                       [--yes]
    ```

    Para mais informações, consulte este [artigo](/cli/azure/backup/vault)

## <a name="delete-the-recovery-services-vault-by-using-azure-resource-manager"></a>Elimine o cofre dos Serviços de Recuperação utilizando o Gestor de Recursos Azure

Esta opção de eliminar o cofre dos Serviços de Recuperação só é recomendada se todas as dependências forem removidas e ainda estiver a receber o *erro de eliminação* do Cofre . Experimente todas ou todas as seguintes dicas:

- A partir do painel **Essentials** no menu do cofre, verifique se não existem itens de backup, servidores de gestão de backup ou itens replicados listados. Se existirem itens de reserva, consulte a secção [Antes de iniciar.](#before-you-start)
- Tente [apagar o cofre do portal](#delete-the-recovery-services-vault) de novo.
- Se todas as dependências forem removidas e ainda estiver a obter o erro de eliminação do *Cofre,* utilize a ferramenta ARMClient para executar os seguintes passos (após a nota).

1. Vá a [chocolatey.org](https://chocolatey.org/) para descarregar e instalar chocolatey. Em seguida, instale o ARMClient executando o seguinte comando:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Inscreva-se na sua conta Azure e, em seguida, execute o seguinte comando:

    `ARMClient.exe login [environment name]`

3. No portal Azure, reúna o ID de subscrição e o nome do grupo de recursos para o cofre que pretende eliminar.

Para obter mais informações sobre o comando ARMClient, consulte [ARMClient README](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-the-azure-resource-manager-client-to-delete-a-recovery-services-vault"></a>Utilize o cliente Azure Resource Manager para apagar um cofre dos Serviços de Recuperação

1. Executar o seguinte comando utilizando o seu ID de subscrição, nome de grupo de recursos e nome do cofre. Se não tiver dependências, o cofre é apagado quando executar o seguinte comando:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<Recovery Services vault name>?api-version=2015-03-15
   ```

2. Se o cofre não estiver vazio, receberá a seguinte mensagem de erro: *O cofre não pode ser apagado, uma vez que existem recursos existentes dentro deste cofre.* Para remover um item ou recipiente protegido dentro de um cofre, executar o seguinte comando:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<Recovery Services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. No portal Azure, certifique-se de que o cofre é apagado.

## <a name="next-steps"></a>Passos seguintes

[Conheça os cofres dos Serviços](backup-azure-recovery-services-vault-overview.md) 
 de Recuperação [Conheça a monitorização e gestão dos cofres dos Serviços de Recuperação](backup-azure-manage-windows-server.md)
