---
title: Eliminar um cofre de serviços de recuperação no Azure
description: Descreve como eliminar um cofre dos serviços de recuperação.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: raynew
ms.openlocfilehash: e195d9a4b9d2bbe21848e083dbccf864188e0790
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508426"
---
# <a name="delete-a-recovery-services-vault"></a>Eliminar um cofre dos Serviços de Recuperação

Este artigo descreve como eliminar uma [Azure Backup](backup-overview.md) cofre dos serviços de recuperação. Contém instruções para remover as dependências e, em seguida, eliminar um cofre e eliminar um cofre por força.


## <a name="before-you-start"></a>Antes de começar

Antes de começar, é importante compreender que não é possível eliminar um cofre de serviços de recuperação que tem os servidores registados no mesmo, ou que contém dados de cópia de segurança.

- Simplesmente eliminar um cofre, anular o registo de servidores contém, remover os dados do cofre e, em seguida, elimine o cofre.
- Se tentar eliminar um cofre que ainda tem dependências, uma mensagem de erro é emitida e terá de remover manualmente as dependências do cofre, incluindo:
    - Cópia de segurança de itens
    - Servidores protegidos
    - Servidores de gestão (servidor de cópia de segurança do Azure, o DPM) de cópia de segurança ![selecione o cofre para abrir o dashboard](./media/backup-azure-delete-vault/backup-items-backup-infrastructure.png)
- Se não pretender reter dados no cofre dos serviços de recuperação e pretende eliminar o cofre, pode eliminar o Cofre por força.
- Se tentar eliminar um cofre, mas não é possível, o Cofre ainda está configurado para receber dados de cópia de segurança.


## <a name="delete-a-vault-from-the-azure-portal"></a>Eliminar um cofre a partir do portal do Azure

1. Abra o dashboard do cofre.  
2. No dashboard, clique em **eliminar**. Certifique-se de que pretende eliminar.

    ![Selecione o cofre para abrir o dashboard](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

Se receber um erro, remova [itens de cópia de segurança](#remove-backup-items), [servidores de infraestrutura](#remove-azure-backup-management-servers), e [pontos de recuperação](#remove-azure-backup-agent-recovery-points)e, em seguida, elimine o cofre.

![Eliminar erro de cofre](./media/backup-azure-delete-vault/error.png)


## <a name="delete-the-recovery-services-vault-using-azure-resource-manager-client"></a>Eliminação do Cofre de serviços de recuperação com o cliente do Azure Resource Manager

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Instalar o chocolatey partir [aqui](https://chocolatey.org/) e para instalar ARMClient executar o comando abaixo:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Inicie sessão na sua conta do Azure e execute este comando:

    `ARMClient.exe login [environment name]`

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

Remova todas as dependências antes do Cofre de serviços de recuperação é eliminado.

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

## <a name="deleting-backup-items-from-management-console"></a>A eliminar itens de cópia de segurança da consola de gestão

Para eliminar os itens de cópia de segurança da infraestrutura de cópia de segurança, navegue para o servidor no local com o Console de gerenciamento (MARS, servidor de cópia de segurança do Azure ou DPM SC, dependendo de onde os seus itens de back-estão protegidos).

### <a name="for-mars-agent"></a>Para o agente MARS

- Inicie o console de gerenciamento de MARS, vá para o **ações** painel e escolha **Agendar cópia de segurança**.
- Partir **modificar ou parar um Backup de agendada** assistente, escolha a opção **parar de utilizar esta agenda de cópia de segurança e eliminar todas as cópias de segurança armazenadas** e clique em **seguinte**.

    ![Modificar ou parar cópia de segurança agendada](./media/backup-azure-delete-vault/modify-schedule-backup.png)

- Partir **parar uma cópia de segurança agendada** assistente, clique em **concluir**.

    ![Parar uma cópia de segurança agendada](./media/backup-azure-delete-vault/stop-schedule-backup.png)
- Lhe for pedido para introduzir um Pin de segurança. Para gerar o PIN, execute os passos abaixo:
  - Inicie sessão no Portal do Azure.
  - Navegue até **cofre dos serviços de recuperação** > **definições** > **propriedades**.
  - Sob **PIN de segurança**, clique em **gerar**. Copie este PIN. (Este PIN é válido por apenas cinco minutos)
- Na consola de gestão (aplicação de cliente) cole o PIN e clique em **Ok**.

  ![Pin de segurança](./media/backup-azure-delete-vault/security-pin.png)

- Na **modificar a cópia de segurança progresso** assistente, verá *dados de cópia de segurança eliminados serão mantidos para 14 dias. Após esse tempo, dados de cópia de segurança serão permanentemente eliminados.*  

    ![Eliminar a infraestrutura de cópia de segurança](./media/backup-azure-delete-vault/deleted-backup-data.png)

Agora que eliminou os itens de cópia de segurança no local, concluir os passos no portal abaixo:
- MARS siga os passos em [pontos de recuperação de agente de remover o Azure Backup](#remove-azure-backup-agent-recovery-points)

### <a name="for-mabs-agent"></a>Para o agente do MABS

Existem métodos diferentes para parar/eliminar proteção online, executar qualquer um dos métodos abaixo:

**Método 1**

Iniciar o **MABS gestão** consola. Na **selecionar método de proteção de dados** secção, desmarque **pretendo proteção online**.

  ![Selecionar método de proteção de dados](./media/backup-azure-delete-vault/data-protection-method.png)

**Método 2**

Para eliminar um grupo de proteção, tem primeiro de parar proteção do grupo. Utilize o procedimento seguinte para parar a proteção e permitir a eliminação de um grupo de proteção.

1.  Na consola do administrador do DPM, clique em **proteção** na barra de navegação.
2.  No painel de visualização, selecione o membro do grupo de proteção que pretende remover. Botão direito do mouse para escolher **parar a proteção dos membros do grupo** opção.
3.  Partir do **parar proteção** caixa de diálogo, selecione **eliminar dados protegidos** > **eliminar o armazenamento online** caixa de verificação e, em seguida, clique em **parar Proteção**.

    ![Eliminar o armazenamento online](./media/backup-azure-delete-vault/delete-storage-online.png)

O estado de membro protegido é agora alterado para **réplica inativa disponível**.

5. O grupo de proteção inativa com o botão direito e selecione **remover proteção inativa**.

    ![Remover proteção inativa](./media/backup-azure-delete-vault/remove-inactive-protection.png)

6. Partir do **eliminar proteção inativa** janela, selecione **eliminar o armazenamento online** e clique em **Ok**.

    ![Remover as réplicas no disco e online](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

Agora que eliminou os itens de cópia de segurança no local, concluir os passos no portal abaixo:
- Para o MABS e o DPM, siga os passos em [servidores de gestão do Azure Backup remover](#remove-azure-backup-management-servers).


### <a name="remove-azure-backup-management-servers"></a>Remova os servidores de gestão de cópia de segurança do Azure

Antes de remover o servidor de gestão de cópia de segurança do Azure, certifique-se de realizar os passos listados na [eliminar itens de cópia de segurança do console de gerenciamento](#deleting-backup-items-from-management-console).

1. No menu do dashboard do cofre, clique em **infraestrutura de cópia de segurança**.
2. Clique em **servidores de gestão de cópia de segurança** para visualizar os servidores.

    ![Selecione o cofre para abrir o dashboard](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Com o botão direito do item > **eliminar**.
4. Sobre o **elimine** menu, escreva o nome do servidor e clique em **eliminar**.

     ![eliminar dados de cópia de segurança](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)
5.  Opcionalmente, forneça um motivo por que motivo está a eliminar os dados e adicione comentários.

> [!NOTE]
> Se vir o erro abaixo, então primeiro execute os passos listados na [eliminar itens de cópia de segurança do console de gerenciamento](#deleting-backup-items-from-management-console).
>
>![Falha na eliminação](./media/backup-azure-delete-vault/deletion-failed.png)
>
> Se não é possível executar os passos para eliminar cópias de segurança a partir da consola de gestão, por exemplo, devido à indisponibilidade do servidor com a consola de gestão, contacte o suporte da Microsoft.

6. Para verificar que a tarefa de eliminação foi concluída, verifique as mensagens do Azure ![eliminar dados de cópia de segurança](./media/backup-azure-delete-vault/messages.png).
7. Depois de concluída a tarefa, o serviço envia uma mensagem: **o processo de cópia de segurança foi parado e os dados de cópia de segurança foram eliminados**.
8. Depois de eliminar um item na lista, sobre o **infraestrutura de cópia de segurança** menu, clique em **atualizar** para ver os itens no cofre.


### <a name="remove-azure-backup-agent-recovery-points"></a>Remover pontos de recuperação de agente de cópia de segurança do Azure

Antes de remover o ponto de recuperação de cópia de segurança do Azure, certifique-se de realizar os passos listados na [eliminar itens de cópia de segurança do console de gerenciamento](#deleting-backup-items-from-management-console).

1. No menu do dashboard do cofre, clique em **infraestrutura de cópia de segurança**.
2. Clique em **servidores protegidos** para visualizar os servidores de infraestrutura.

    ![Selecione o cofre para abrir o dashboard](./media/backup-azure-delete-vault/identify-protected-servers.png)

3. Na **servidores protegidos** , clique em agente de cópia de segurança do Azure.

    ![Selecione o tipo de cópia de segurança](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

4. Clique no servidor na lista de servidores protegidos com o agente de cópia de segurança do Azure.

    ![Selecione o servidor protegido específico](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

5. No dashboard do servidor selecionado, clique em **eliminar**.

    ![eliminar o servidor selecionado](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

6. Sobre o **elimine** , escreva o nome do servidor e, clique em **eliminar**.

     ![eliminar dados de cópia de segurança](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

7. Opcionalmente, forneça um motivo por que motivo está a eliminar os dados e adicione comentários.

> [!NOTE]
> Se vir o erro abaixo, então primeiro execute os passos listados na [eliminar itens de cópia de segurança do console de gerenciamento](#deleting-backup-items-from-management-console).
>
>![Falha na eliminação](./media/backup-azure-delete-vault/deletion-failed.png)
>
> Se não é possível executar os passos para eliminar cópias de segurança a partir da consola de gestão, por exemplo, devido à indisponibilidade do servidor com a consola de gestão, contacte o suporte da Microsoft. 

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
