---
title: Excluir um cofre dos serviços de recuperação no Azure
description: Descreve como excluir um cofre dos serviços de recuperação.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: dacurwin
ms.openlocfilehash: 34484c309cb186aabec519e54269fefae316165e
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639902"
---
# <a name="delete-a-recovery-services-vault"></a>Eliminar um cofre dos Serviços de Recuperação

Este artigo descreve como excluir um cofre dos serviços de recuperação de [backup do Azure](backup-overview.md) . Ele contém instruções para remover dependências e, em seguida, excluir um cofre.


## <a name="before-you-start"></a>Antes de começar

Você não pode excluir um cofre dos serviços de recuperação que tem dependências como servidores protegidos ou servidores de gerenciamento de backup associados ao cofre.

- O cofre que contém os dados de backup não pode ser excluído (ou seja, mesmo que você tenha interrompido a proteção, mas manteve os dados de backup).

- Se você excluir um cofre que contém dependências, ele exibirá o seguinte erro:

  ![Erro ao excluir o cofre](./media/backup-azure-delete-vault/error.png)

- Se você excluir um item protegido local (MARS, MABS ou DPM para o Azure) do portal que contém dependências, uma mensagem de aviso será exibida:

  ![Erro ao excluir servidor protegido](./media/backup-azure-delete-vault/error-message.jpg)

  
Para excluir o cofre normalmente, escolha o cenário que corresponde à sua configuração e siga as etapas recomendadas:

Cenário | Etapas para remover dependências para excluir o cofre |
-- | --
Tenho arquivos e pastas locais protegidos usando o MARS (agente de backup do Azure) fazendo backup no Azure | Execute as etapas em excluir dados de backup e itens de backup- [para o agente Mars](#delete-backup-items-from-mars-management-console)
Tenho computadores locais protegidos usando o MABS (Backup do Microsoft Azure Server) ou o DPM no Azure (System Center Data Protection Manager) | Execute as etapas em excluir dados de backup e itens de backup- [para o agente mAbs](#delete-backup-items-from-mabs-management-console)
Tenho itens protegidos na nuvem (por exemplo, VM laaS, compartilhamento de arquivos do Azure, etc.)  | Execute as etapas em excluir dados de backup e itens de backup- [para itens protegidos na nuvem](#delete-protected-items-in-cloud)
Eu protegi itens locais e na nuvem | Execute as etapas em excluir dados de backup e itens de backup na sequência abaixo: <br> - [Para itens protegidos na nuvem](#delete-protected-items-in-cloud)<br> - [Para o agente MARS](#delete-backup-items-from-mars-management-console) <br> - [Para o agente MABS](#delete-backup-items-from-mabs-management-console)
Não tenho itens protegidos no local ou na nuvem; no entanto, ainda estou recebendo o erro de exclusão do cofre | Execute as etapas em [excluir o cofre dos serviços de recuperação usando Azure Resource Manager cliente](#delete-the-recovery-services-vault-using-azure-resource-manager-client)


## <a name="delete-protected-items-in-cloud"></a>Excluir itens protegidos na nuvem

Antes de continuar, leia **[esta](#before-you-start)** seção para entender as dependências e o processo de exclusão de cofre.

Para interromper a proteção e excluir os dados de backup, execute o seguinte:

1. No portal >**itens de backup** do **cofre** > de serviços de recuperação escolha os itens protegidos na nuvem (exemplo de máquina AzureVirtual, armazenamento do Azure (arquivos do Azure), VM do SQL do Azure e assim por diante).

    ![Selecione o tipo de backup](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. Clique com o botão direito do mouse no item de backup, dependendo se o item de backup está protegido ou não, se o menu exibirá **parar backup** ou **excluir dados de backup**.

    - Para **parar o backup**, selecione **excluir dados de backup** na lista suspensa. Insira o **nome** do item de backup (diferencia maiúsculas de minúsculas), selecione um **motivo**, insira **comentários**e clique em **parar backup**.

        ![Selecione o tipo de backup](./media/backup-azure-delete-vault/stop-backup-item.png)

    - Para **excluir dados de backup**, insira o nome do item de backup (diferencia maiúsculas de minúsculas), selecione um **motivo**, insira **comentários**e clique em **excluir**. 

         ![excluir dados de backup](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. Verifique a **notificação** ![excluir dados](./media/backup-azure-delete-vault/messages.png)de backup. Após a conclusão, o serviço exibirá a mensagem: Parando **backup e excluindo dados de backup para "*item de backup*"** . **Operação concluída com êxito**.
6. Clique em **Atualizar** no menu **itens de backup** para verificar se o item de backup foi removido.

      ![excluir dados de backup](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>Excluir itens protegidos localmente

Antes de continuar, leia **[esta](#before-you-start)** seção para entender as dependências e o processo de exclusão de cofre.

1. No menu do painel do cofre, clique em **infraestrutura de backup**.
2. Dependendo do seu cenário local, escolha a opção abaixo:

      - Para o **agente de backup do Azure**, escolha **servidores** > protegidos**agente de backup do Azure** e selecione o servidor que você deseja excluir. 

        ![Selecione seu cofre para abrir seu painel](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - Para **servidor de backup do Azure**/**o DPM**, escolha **backup servidores de gerenciamento**. Selecione o servidor que você deseja excluir. 


          ![Selecione cofre para abrir seu painel](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. A folha **excluir** é exibida com a mensagem de aviso.

     ![excluir dados de backup](./media/backup-azure-delete-vault/delete-protected-server.png)

     Examine a mensagem de aviso e as instruções fornecidas na caixa de seleção consentimento.
    
    > [!NOTE]
    >- Se o servidor protegido estiver em sincronia com o serviço do Azure e os itens de backup existirem, a caixa de seleção consentimento exibirá o número de itens de backup dependentes e o link para exibir os itens de backup.
    >- Se o servidor protegido não estiver sincronizado com o serviço do Azure e os itens de backup existirem, a caixa de seleção consentimento exibirá o número de itens de backup.
    >- Se os itens de backup não existirem, a caixa de seleção consentimento solicitará a exclusão.

4. Marque a caixa de seleção consentimento e clique em **excluir**.




5. Verifique a **notificação** ![excluir dados](./media/backup-azure-delete-vault/messages.png)de backup. Após a conclusão, o serviço exibirá a mensagem: Parando **backup e excluindo dados de backup para "*item de backup*"** . **Operação concluída com êxito**.
6. Clique em **Atualizar** no menu **itens de backup** para verificar se o item de backup foi removido.

Agora você pode continuar a excluir os itens de backup do console de gerenciamento:
    
   - [Itens protegidos usando MARS](#delete-backup-items-from-mars-management-console)
    - [Itens protegidos usando MABS](#delete-backup-items-from-mabs-management-console)


### <a name="delete-backup-items-from-mars-management-console"></a>Excluir itens de backup do console de gerenciamento do MARS

Para excluir itens de backup do console de gerenciamento do MARS

- Inicie o console de gerenciamento do MARS, vá para o painel **ações** e escolha **agendar backup**.
- Em **modificar ou parar um** assistente de backup agendado, escolha a opção **parar de usar essa agenda de backup e excluir todos os backups armazenados** e clique em **Avançar**.

    ![Modificar ou parar um backup agendado](./media/backup-azure-delete-vault/modify-schedule-backup.png)

- Em **parar um** assistente de backup agendado, clique em **concluir**.

    ![Parar um backup agendado](./media/backup-azure-delete-vault/stop-schedule-backup.png)
- Você será solicitado a inserir um PIN de segurança. Para gerar o PIN, execute as etapas a seguir:
  - Inicie sessão no Portal do Azure.
  - Navegue até **serviços de recuperação** > **configurações** > do cofre**Propriedades**.
  - Em **PIN de segurança**, clique em **gerar**. Copie este PIN. (Esse PIN é válido por apenas cinco minutos)
- No console de gerenciamento (aplicativo cliente), Cole o PIN e clique em **OK**.

  ![PIN de segurança](./media/backup-azure-delete-vault/security-pin.png)

- No assistente de **modificação de progresso de backup** , *você verá que os dados de backup excluídos serão mantidos por 14 dias. Após esse período, os dados de backup serão excluídos permanentemente.*  

    ![Excluir infraestrutura de backup](./media/backup-azure-delete-vault/deleted-backup-data.png)

Agora que você excluiu os itens de backup do local, conclua as próximas etapas do Portal.

### <a name="delete-backup-items-from-mabs-management-console"></a>Excluir itens de backup do console de gerenciamento do MABS

Para excluir itens de backup do console de gerenciamento do MABS

**Método 1** Para interromper a proteção e excluir dados de backup, execute as etapas a seguir:

1.  Em Console do Administrador do DPM, clique em **proteção** na barra de navegação.
2.  No painel de exibição, selecione o membro do grupo de proteção que você deseja remover. Clique com o botão direito do mouse para escolher a opção **parar proteção de membros do grupo** .
3.  Na caixa de diálogo **parar proteção** , selecione a caixa de seleção **excluir dados** > protegidos**Excluir armazenamento online** e clique em **parar proteção**.

    ![Excluir armazenamento online](./media/backup-azure-delete-vault/delete-storage-online.png)

O status do membro protegido agora é alterado para **réplica inativa disponível**.

4. Clique com o botão direito do mouse no grupo de proteção inativo e selecione **remover proteção inativa**.

    ![Remover proteção inativa](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. Na janela **excluir proteção inativa** , selecione **Excluir armazenamento online** e clique em **OK**.

    ![Remover réplicas em disco e online](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

**Método 2** Inicie o console de **Gerenciamento do mAbs** . Na seção **selecionar método de proteção de dados** , cancele a seleção **desejo proteção online**.

  ![selecionar método de proteção de dados](./media/backup-azure-delete-vault/data-protection-method.png)

Agora que você excluiu os itens de backup do local, conclua as próximas etapas do Portal.


## <a name="delete-the-recovery-services-vault"></a>Eliminar o cofre dos Serviços de Recuperação

1. Quando todas as dependências tiverem sido removidas, role até o painel **Essentials** no menu do cofre.
2. Verifique se não há **itens de backup**, **servidores de gerenciamento de backup**ou **itens replicados** listados. Se os itens ainda aparecerem no cofre, consulte a seção [antes de começar](#before-you-start) .

3. Quando não houver mais itens no cofre, no painel do cofre, clique em **excluir**.

    ![excluir dados de backup](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Para verificar se você deseja excluir o cofre, clique em **Sim**. O cofre é excluído e o portal retorna para o **novo** menu de serviço.

## <a name="delete-the-recovery-services-vault-using-azure-resource-manager-client"></a>Excluir o cofre dos serviços de recuperação usando Azure Resource Manager cliente

Esta opção para excluir o cofre dos serviços de recuperação só é recomendada quando todas as dependências são removidas e você ainda está obtendo o *erro de exclusão do cofre*.

- No painel **Essentials** no menu do cofre, verifique se não há itens de **backup**, **servidores de gerenciamento de backup**ou **itens replicados** listados. Se houver itens de backup, consulte a seção [antes de começar](#before-you-start) .
- Tente [excluir o cofre do portal](#delete-the-recovery-services-vault)novamente.
- Se todas as dependências forem removidas e você ainda estiver recebendo o *erro de exclusão do cofre* , use a ferramenta ARMClient para executar as etapas abaixo;

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Instale o Chocolatey [aqui](https://chocolatey.org/) e para instalar o ARMClient execute o comando abaixo:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Entre em sua conta do Azure e execute este comando:

    `ARMClient.exe login [environment name]`

3. Na portal do Azure, reúna a ID da assinatura e o nome do grupo de recursos para o cofre que você deseja excluir.

Para obter mais informações sobre o comando ARMClient, consulte este [documento](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-azure-resource-manager-client-to-delete-recovery-services-vault"></a>Usar Azure Resource Manager cliente para excluir o cofre dos serviços de recuperação

1. Execute o comando a seguir usando sua ID de assinatura, o nome do grupo de recursos e o nome do cofre. Quando você executar o comando, ele excluirá o cofre se você não tiver nenhuma dependência.

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
2. Se o cofre não estiver vazio, você receberá o erro "o cofre não pode ser excluído, pois há recursos existentes neste cofre". Para remover um contêiner/itens protegidos em um cofre, faça o seguinte:

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. No portal do Azure, verifique se o cofre foi excluído.


## <a name="next-steps"></a>Passos Seguintes

[Saiba mais](backup-azure-recovery-services-vault-overview.md) Cofres dos serviços de recuperação.<br/>
[Saiba mais sobre como](backup-azure-manage-windows-server.md) monitorar e gerenciar cofres de serviços de recuperação.
