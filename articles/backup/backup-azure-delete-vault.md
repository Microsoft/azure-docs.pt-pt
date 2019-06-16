---
title: Eliminar um cofre de serviços de recuperação no Azure
description: Descreve como eliminar um cofre dos serviços de recuperação.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: raynew
ms.openlocfilehash: a7dd5530c3941fe55e8a649f8adb217159823f5d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66492796"
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


## <a name="delete-the-recovery-services-vault-using-azure-resource-manager-client"></a>Eliminação do Cofre de serviços de recuperação com o cliente do Azure Resource Manager

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Instalar o chocolatey partir [aqui](https://chocolatey.org/) e para instalar ARMClient executar o comando abaixo:

   ` choco install armclient --source=https://chocolatey.org/api/v2/ `
2. Inicie sessão na conta do Azure, a executar o comando abaixo

    ` ARMClient.exe login [environment name] `

3. No portal do Azure, colete o nome de grupo de subscrição ID e recursos, para o cofre que pretende eliminar.

Para obter mais informações sobre ARMClient comando, consulte [documento](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-azure-resource-manager-client-to-delete-recovery-services-vault"></a>Utilize o cliente do Azure Resource Manager para eliminar o Cofre dos serviços de recuperação

1. Execute o comando seguinte com o seu ID de subscrição, o nome do grupo de recursos e o nome do cofre. Ao executar o comando elimina o Cofre se não tiver quaisquer dependências.

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
2. Se o Cofre não 's vazio, receberá o erro "Cofre não pode ser eliminado porque existem recursos existentes dentro deste cofre". Para remover um itens protegidos / contentor dentro de um cofre, efetue o seguinte procedimento:

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. No portal do Azure, certifique-se de que o Cofre é eliminado.


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

3. Com o botão direito do item > **eliminar**.
4. Para verificar que a tarefa de eliminação foi concluída, verifique as mensagens do Azure ![eliminar dados de cópia de segurança](./media/backup-azure-delete-vault/messages.png).
5. Depois de concluída a tarefa, o serviço envia uma mensagem: **o processo de cópia de segurança foi parado e os dados de cópia de segurança foram eliminados**.
6. Depois de eliminar um item na lista, sobre o **infraestrutura de cópia de segurança** menu, clique em **atualizar** para ver os itens no cofre.


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

     ![eliminar dados de cópia de segurança](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

7. Opcionalmente, forneça um motivo por que motivo está a eliminar os dados e adicione comentários.
8. Para verificar que a tarefa de eliminação foi concluída, verifique as mensagens do Azure ![eliminar dados de cópia de segurança](./media/backup-azure-delete-vault/messages.png).
9. Depois de eliminar um item na lista, sobre o **infraestrutura de cópia de segurança** menu, clique em **atualizar** para ver os itens no cofre.

### <a name="delete-the-vault-after-removing-dependencies"></a>Eliminar o Cofre depois de remover as dependências

1. Quando tiverem sido removidas todas as dependências, desloque-se para o **Essentials** painel no menu do cofre.
2. Certifique-se de que não qualquer **itens de cópia de segurança**, **servidores de gestão de cópia de segurança**, ou **itens replicados** listados. Se ainda forem apresentados itens no cofre, removê-los.

3. Quando existem não existem mais itens no cofre, no dashboard do cofre, clique **eliminar**.

    ![eliminar dados de cópia de segurança](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Certifique-se de que pretende eliminar o cofre, clique em **Sim**. O Cofre é eliminado e o portal devolve para o **New** menu de serviços.

## <a name="what-if-i-stop-the-backup-process-but-retain-the-data"></a>E se parar o processo de cópia de segurança mas manter os dados?

Se parar o processo de cópia de segurança mas acidentalmente reter os dados, tem de eliminar os dados de cópia de segurança, tal como descrito nas secções anteriores.

## <a name="next-steps"></a>Passos Seguintes

[Saiba mais sobre](backup-azure-recovery-services-vault-overview.md) cofres dos serviços de recuperação.
