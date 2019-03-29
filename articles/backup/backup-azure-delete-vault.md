---
title: Eliminar um cofre de serviços de recuperação no Azure
description: Descreve como eliminar um cofre dos serviços de recuperação.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: raynew
ms.openlocfilehash: 94d66e28f8edbda6c41dcceaf427d7d7d869c90f
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58620122"
---
# <a name="delete-a-recovery-services-vault"></a>Eliminar um cofre dos Serviços de Recuperação

Este artigo descreve como eliminar uma [Azure Backup](backup-overview.md) cofre dos serviços de recuperação. Contém instruções para remover as dependências e, em seguida, eliminar um cofre e eliminar um cofre por força.


## <a name="before-you-start"></a>Antes de começar

Antes de começar, é importante compreender que não é possível eliminar um cofre de serviços de recuperação que tem os servidores registados no mesmo, ou que contém dados de cópia de segurança.

- Para eliminar um cofre corretamente, anular o registo de servidores no mesmo, remover os dados do cofre e, em seguida, elimine o cofre.
- Se tentar eliminar um cofre que ainda tem dependências, uma mensagem de erro é emitida. e terá de remover manualmente as dependências do cofre, incluindo:
    - Cópia de segurança de itens
    - Servidores protegidos
    - Servidores de gestão (servidor de cópia de segurança do Azure, o DPM) de cópia de segurança ![selecione o cofre para abrir o dashboard](./media/backup-azure-delete-vault/backup-items-backup-infrastructure.png)
- Se não pretender reter dados no cofre dos serviços de recuperação e pretende eliminar o cofre, pode eliminar o Cofre por força.
- Se tentar eliminar um cofre, mas não é possível, o Cofre ainda está configurado para receber dados de cópia de segurança.


## <a name="delete-a-vault-from-the-azure-portal"></a>Eliminar um cofre a partir do portal do Azure

1. Abra o dashboard do cofre.  
2. No dashboard, clique em **eliminar**. Certifique-se de que pretende eliminar.

    ![Selecione o cofre para abrir o dashboard](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

Se receber um erro, remova [itens de cópia de segurança](#remove-backup-items), [servidores de infraestrutura](#remove-backup-infrastructure-servers), e [pontos de recuperação](#remove-azure-backup-agent-recovery-points)e, em seguida, elimine o cofre.

![Eliminar erro de cofre](./media/backup-azure-delete-vault/error.png)


## <a name="delete-the-recovery-services-vault-by-force"></a>Eliminar o Cofre de serviços de recuperação por força

Pode eliminar um cofre por força com o PowerShell. A eliminação forçada significa que o Cofre e associados todos os dados de cópia de segurança é eliminado permanentemente.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


Para eliminar um cofre por força:

1. Inicie sessão na sua subscrição do Azure com o `Connect-AzAccount` de comando e siga na tela as direções.

   ```powershell
    Connect-AzAccount
   ```
2. Na primeira vez que utilizar o Azure Backup, tem de registar o fornecedor de serviços de recuperação do Azure na sua subscrição com [Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider).

   ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
   ```

3. Abra uma janela do PowerShell com privilégios de administrador.
4. Utilize `Set-ExecutionPolicy Unrestricted` para remover quaisquer restrições.
5. Execute o seguinte comando para transferir o pacote de cliente do Azure Resource Manager de chocolately.org.

    `iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

6. Utilize o seguinte comando para instalar o cliente de API do Azure Resource Manager.

   `choco.exe install armclient`

7. No portal do Azure, colete o nome de grupo de subscrição ID e recursos, para o cofre que pretende eliminar.
8. No PowerShell, execute o comando seguinte com o seu ID de subscrição, o nome do grupo de recursos e o nome do cofre. Ao executar o comando, elimina o Cofre e todas as dependências.

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
9. Se o Cofre não 's vazio, receberá o erro "Cofre não pode ser eliminado porque existem recursos existentes dentro deste cofre". Para remover um contidos dentro de um cofre, faça o seguinte:

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

10. No portal do Azure, certifique-se de que o Cofre é eliminado.


## <a name="remove-vault-items-and-delete-the-vault"></a>Remover itens do cofre e elimine o Cofre

Estes procedimento forneça alguns exemplos para remover dados de cópia de segurança e servidores de infraestrutura. Depois de tudo o que é removido de um cofre, pode eliminá-la.

### <a name="remove-backup-items"></a>Remover itens de cópia de segurança

Este procedimento fornece um exemplo que mostra como remover dados de cópia de segurança de ficheiros do Azure.

1. Clique em **itens de cópia de segurança** > **Azure Storage (ficheiros do Azure)**

    ![Selecione o tipo de cópia de segurança](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

2. Com o botão direito cada item de ficheiros do Azure para remover e clique em **parar cópia de segurança**.

    ![Selecione o tipo de cópia de segurança](./media/backup-azure-delete-vault/stop-backup-item.png)


3. Na **parar cópia de segurança** > **escolha uma opção**, selecione **eliminar dados de cópia de segurança**.
4. Escreva o nome do item e clique em **parar cópia de segurança**. 
   - Isto verifica que pretende eliminar o item.
   - O **parar cópia de segurança** botão ativa depois de verificar.
   - Se manter e não elimine os dados, não poderá eliminar o cofre.

     ![eliminar dados de cópia de segurança](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. Opcionalmente, forneça um motivo por que motivo está a eliminar os dados e adicione comentários.
6. Para verificar que a tarefa de eliminação foi concluída, verifique as mensagens do Azure ![eliminar dados de cópia de segurança](./media/backup-azure-delete-vault/messages.png).
7. Depois de concluída a tarefa, o serviço envia uma mensagem: **o processo de cópia de segurança foi parado e os dados de cópia de segurança foram eliminados**.
8. Depois de eliminar um item na lista, sobre o **itens de cópia de segurança** menu, clique em **atualizar** para ver os itens no cofre.

      ![eliminar dados de cópia de segurança](./media/backup-azure-delete-vault/empty-items-list.png)


### <a name="remove-backup-infrastructure-servers"></a>Remover servidores de infraestrutura de cópia de segurança

1. No menu do dashboard do cofre, clique em **infraestrutura de cópia de segurança**.
2. Clique em **servidores de gestão de cópia de segurança** para visualizar os servidores. 

    ![Selecione o cofre para abrir o dashboard](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

2. Com o botão direito do item > **eliminar**.

    ![Selecione o tipo de cópia de segurança](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

3. . Na **parar cópia de segurança** > **escolha uma opção**, selecione **eliminar dados de cópia de segurança**.
4. Escreva o nome do item e clique em **parar cópia de segurança**. 
   - Isto verifica que pretende eliminar o item.
   - O **parar cópia de segurança** botão ativa depois de verificar.
   - Se manter e não elimine os dados, não poderá eliminar o cofre.

     ![eliminar dados de cópia de segurança](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. Opcionalmente, forneça um motivo por que motivo está a eliminar os dados e adicione comentários.
6. Para verificar que a tarefa de eliminação foi concluída, verifique as mensagens do Azure ![eliminar dados de cópia de segurança](./media/backup-azure-delete-vault/messages.png).
7. Depois de concluída a tarefa, o serviço envia uma mensagem: **o processo de cópia de segurança foi parado e os dados de cópia de segurança foram eliminados**.
8. Depois de eliminar um item na lista, sobre o **itens de cópia de segurança** menu, clique em **atualizar** para ver os itens no cofre.


### <a name="remove-azure-backup-agent-recovery-points"></a>Remover pontos de recuperação de agente de cópia de segurança do Azure

1. No menu do dashboard do cofre, clique em **infraestrutura de cópia de segurança**.
2. Clique em **servidores de gestão de cópia de segurança** para visualizar os servidores de infraestrutura.

    ![Selecione o cofre para abrir o dashboard](./media/backup-azure-delete-vault/identify-protected-servers.png)

3. Na **servidores protegidos** , clique em agente de cópia de segurança do Azure.

    ![Selecione o tipo de cópia de segurança](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

4. Clique no servidor na lista de servidores protegidos com o agente de cópia de segurança do Azure.

    ![Selecione o servidor protegido específico](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

5. No dashboard do servidor selecionado, clique em **eliminar**.

    ![eliminar o servidor selecionado](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

6. Sobre o **elimine** escreva o nome do item de menu e clique em **eliminar**.
   - Isto verifica que pretende eliminar o item.
   - O **parar cópia de segurança** botão ativa depois de verificar.
   - Se manter e não elimine os dados, não poderá eliminar o cofre.

     ![eliminar dados de cópia de segurança](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

7. Opcionalmente, forneça um motivo por que motivo está a eliminar os dados e adicione comentários.
8. Para verificar que a tarefa de eliminação foi concluída, verifique as mensagens do Azure ![eliminar dados de cópia de segurança](./media/backup-azure-delete-vault/messages.png).
7. Depois de concluída a tarefa, o serviço envia uma mensagem: **o processo de cópia de segurança foi parado e os dados de cópia de segurança foram eliminados**.
8. Depois de eliminar um item na lista, sobre o **itens de cópia de segurança** menu, clique em **atualizar** para ver os itens no cofre.






### <a name="delete-the-vault-after-removing-dependencies"></a>Eliminar o Cofre depois de remover as dependências

1. Quando tiverem sido removidas todas as dependências, desloque-se para o **Essentials** painel no menu do cofre.
2. Certifique-se de que não qualquer **itens de cópia de segurança**, **servidores de gestão de cópia de segurança**, ou **itens replicados** listados. Se ainda forem apresentados itens no cofre, removê-los.

2. Quando existem não existem mais itens no cofre, no dashboard do cofre, clique **eliminar**.

    ![eliminar dados de cópia de segurança](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

1. Certifique-se de que pretende eliminar o cofre, clique em **Sim**. O Cofre é eliminado e o portal devolve para o **New** menu de serviços.

## <a name="what-if-i-stop-the-backup-process-but-retain-the-data"></a>E se parar o processo de cópia de segurança mas manter os dados?

Se parar o processo de cópia de segurança mas acidentalmente reter os dados, tem de eliminar os dados de cópia de segurança, tal como descrito nas secções anteriores.

## <a name="next-steps"></a>Passos Seguintes

[Saiba mais sobre](backup-azure-recovery-services-vault-overview.md) cofres dos serviços de recuperação.
