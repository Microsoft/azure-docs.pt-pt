---
title: Eliminar um cofre de serviços de recuperação no Azure
description: Descreve como eliminar um cofre dos serviços de recuperação.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/11/2019
ms.author: raynew
ms.openlocfilehash: 01c20ce84f5c97b3a0ac437fe602861085b5052c
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827880"
---
# <a name="delete-a-recovery-services-vault"></a>Eliminar um cofre dos Serviços de Recuperação

Este artigo descreve como eliminar uma [Azure Backup](backup-overview.md) cofre dos serviços de recuperação. Contém instruções para remover as dependências e, em seguida, eliminar um cofre.


## <a name="before-you-start"></a>Antes de começar

Não é possível eliminar um cofre de serviços de recuperação que tenha dependências, tais como servidores protegidos ou servidores de gestão de cópia de segurança associados ao cofre.<br/>
Não é possível eliminar o cofre que contém os dados de cópia de segurança (ou seja, mesmo se tiver parado a proteção, mas retidos os dados de cópia de segurança).

Se eliminar um cofre que contém as dependências, em seguida, irá apresentar o erro seguinte:

![Eliminar erro de cofre](./media/backup-azure-delete-vault/error.png)

Simplesmente eliminar um cofre de seguir os passos mencionados na sequência abaixo:
- Pare a proteção e eliminar dados de cópia de segurança
- Eliminar os servidores protegidos ou servidores de gestão de cópia de segurança
- Eliminar o cofre


## <a name="delete-backup-data-and-backup-items"></a>Eliminar dados de cópia de segurança e itens de cópia de segurança

Antes de continuar a leitura adicional **[isso ](#before-you-start)** secção para compreender as dependências e processo de eliminação do cofre.

### <a name="for-protected-items-in-cloud"></a>Para os itens protegidos na Cloud

Para parar a proteção e eliminar os dados de cópia de segurança, execute o abaixo:

1. No portal > cofre dos serviços de recuperação > itens de cópia de segurança escolher os itens protegidos na cloud.

    ![Selecione o tipo de cópia de segurança](./media/backup-azure-delete-vault/azure-storage-selected.jpg)

2. Para cada item, precisa com o botão direito e escolher **parar cópia de segurança**.

    ![Selecione o tipo de cópia de segurança](./media/backup-azure-delete-vault/stop-backup-item.png)

3. Na **parar cópia de segurança** > **escolha uma opção**, selecione **eliminar dados de cópia de segurança**.
4. Escreva o nome do item e clique em **parar cópia de segurança**.
   - Isto verifica que pretende eliminar o item.
   - O **parar cópia de segurança** botão ativa depois de verificar.
   - Se manter e não elimine os dados, não poderá eliminar o cofre.

     ![eliminar dados de cópia de segurança](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. Verifique os **notificação** ![eliminar dados de cópia de segurança](./media/backup-azure-delete-vault/messages.png). Após a conclusão, o serviço apresenta a mensagem: **A parar a cópia de segurança e a eliminar a cópia de segurança de dados para "*Item de cópia de segurança*"** . **A operação foi concluída com êxito**.
6. Clique em **Atualize** sobre o **itens de cópia de segurança** menu, para verificar se o item de cópia de segurança é removido.

      ![eliminar dados de cópia de segurança](./media/backup-azure-delete-vault/empty-items-list.png)

### <a name="for-mars-agent"></a>Para o agente MARS

Para parar a proteção e eliminar dados de cópia de segurança, execute as etapas na ordem listada a seguir:

- [Passo 1: Eliminar itens de cópia de segurança a partir da consola de gestão de MARS](#step-1-delete-backup-items-from-mars-management-console)
- [Passo 2: No portal, remova o agente de cópia de segurança do Azure](#step-1-delete-backup-items-from-mars-management-console)


#### <a name="step-1-delete-backup-items-from-mars-management-console"></a>Passo 1: Eliminar itens de cópia de segurança a partir da consola de gestão de MARS

Se não é possível executar este passo devido à indisponibilidade do servidor, em seguida, contacte o suporte da Microsoft.

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

Agora que eliminou os itens de cópia de segurança no local, conclua os passos seguintes a partir do portal.

#### <a name="step-2-from-portal-remove-azure-backup-agent"></a>Passo 2: No portal, remova o agente de cópia de segurança do Azure

Certifique-se [passo 1](#step-1-delete-backup-items-from-mars-management-console) é concluída antes de avançar:

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

> [!NOTE]
> Se vir o erro abaixo, então primeiro execute os passos listados na [eliminar itens de cópia de segurança do console de gerenciamento](#step-1-delete-backup-items-from-mars-management-console).
>
>![Falha na eliminação](./media/backup-azure-delete-vault/deletion-failed.png)
>
>Se não é possível executar os passos para eliminar cópias de segurança a partir da consola de gestão, por exemplo, devido à indisponibilidade do servidor com a consola de gestão, contacte o suporte da Microsoft.

7. Verifique os **notificação** ![eliminar dados de cópia de segurança](./media/backup-azure-delete-vault/messages.png). Após a conclusão, o serviço apresenta a mensagem: **A parar a cópia de segurança e a eliminar a cópia de segurança de dados para "*Item de cópia de segurança*"** . **A operação foi concluída com êxito**.
8. Clique em **Atualize** sobre o **itens de cópia de segurança** menu, para verificar se o item de cópia de segurança é removido.


### <a name="for-mabs-agent"></a>Para o agente do MABS

Para parar a proteção e eliminar dados de cópia de segurança, execute as etapas na ordem listada a seguir:

- [Passo 1: Eliminar itens de cópia de segurança a partir da consola de gestão do MABS](#step-1-delete-backup-items-from-mabs-management-console)
- [Passo 2: No portal, remova os servidores de gestão de cópia de segurança do Azure](#step-2-from-portal-remove-azure-backup-agent)

#### <a name="step-1-delete-backup-items-from-mabs-management-console"></a>Passo 1: Eliminar itens de cópia de segurança a partir da consola de gestão do MABS

Se não é possível executar este passo devido à indisponibilidade do servidor, em seguida, contacte o suporte da Microsoft.

**Método 1** para parar a proteção e eliminar dados de cópia de segurança, execute os passos abaixo:

1.  Na consola do administrador do DPM, clique em **proteção** na barra de navegação.
2.  No painel de visualização, selecione o membro do grupo de proteção que pretende remover. Botão direito do mouse para escolher **parar a proteção dos membros do grupo** opção.
3.  Partir do **parar proteção** caixa de diálogo, selecione **eliminar dados protegidos** > **eliminar o armazenamento online** caixa de verificação e, em seguida, clique em **parar Proteção**.

    ![Eliminar o armazenamento online](./media/backup-azure-delete-vault/delete-storage-online.png)

O estado de membro protegido é agora alterado para **réplica inativa disponível**.

5. O grupo de proteção inativa com o botão direito e selecione **remover proteção inativa**.

    ![Remover proteção inativa](./media/backup-azure-delete-vault/remove-inactive-protection.png)

6. Partir do **eliminar proteção inativa** janela, selecione **eliminar o armazenamento online** e clique em **Ok**.

    ![Remover as réplicas no disco e online](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

**Método 2** iniciar o **MABS gestão** consola. Na **selecionar método de proteção de dados** secção, desmarque **pretendo proteção online**.

  ![Selecionar método de proteção de dados](./media/backup-azure-delete-vault/data-protection-method.png)

Agora que eliminou os itens de cópia de segurança no local, conclua os passos seguintes a partir do portal.

#### <a name="step-2-from-portal-remove-azure-backup-management-servers"></a>Passo 2: No portal, remova os servidores de gestão de cópia de segurança do Azure

Certifique-se [passo 1](#step-1-delete-backup-items-from-mabs-management-console) é concluída antes de avançar:

1. No menu do dashboard do cofre, clique em **infraestrutura de cópia de segurança**.
2. Clique em **servidores de gestão de cópia de segurança** para visualizar os servidores.

    ![Selecione o cofre para abrir o dashboard](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Com o botão direito do item > **eliminar**.
4. Sobre o **elimine** menu, escreva o nome do servidor e clique em **eliminar**.

     ![eliminar dados de cópia de segurança](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

> [!NOTE]
> Se vir o erro abaixo, então primeiro execute os passos listados na [eliminar itens de cópia de segurança do console de gerenciamento](#step-2-from-portal-remove-azure-backup-management-servers).
>
>![Falha na eliminação](./media/backup-azure-delete-vault/deletion-failed.png)
>
> Se não é possível executar os passos para eliminar cópias de segurança a partir da consola de gestão, por exemplo, devido à indisponibilidade do servidor com a consola de gestão, contacte o suporte da Microsoft.

5. Verifique os **notificação** ![eliminar dados de cópia de segurança](./media/backup-azure-delete-vault/messages.png). Após a conclusão, o serviço apresenta a mensagem: **A parar a cópia de segurança e a eliminar a cópia de segurança de dados para "*Item de cópia de segurança*"** . **A operação foi concluída com êxito**.
6. Clique em **Atualize** sobre o **itens de cópia de segurança** menu, para verificar se o item de cópia de segurança é removido.


## <a name="delete-the-recovery-services-vault"></a>Eliminar o cofre dos Serviços de Recuperação

1. Quando tiverem sido removidas todas as dependências, desloque-se para o **Essentials** painel no menu do cofre.
2. Certifique-se de que não qualquer **itens de cópia de segurança**, **servidores de gestão de cópia de segurança**, ou **itens replicados** listados. Se ainda forem apresentados itens no cofre, [removê-los](#delete-backup-data-and-backup-items).

3. Quando existem não existem mais itens no cofre, no dashboard do cofre, clique **eliminar**.

    ![eliminar dados de cópia de segurança](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Certifique-se de que pretende eliminar o cofre, clique em **Sim**. O Cofre é eliminado e o portal devolve para o **New** menu de serviços.

## <a name="delete-the-recovery-services-vault-using-azure-resource-manager-client"></a>Eliminação do Cofre de serviços de recuperação com o cliente do Azure Resource Manager

Esta opção para eliminar o Cofre dos serviços de recuperação só é recomendada quando todas as dependências são removidas e continuar a obter o *erro ao eliminar o Cofre dos*.



- Partir do **Essentials** painel no menu do cofre, certifique-se de que não qualquer **itens de cópia de segurança**, **servidores de gestão de cópia de segurança**, ou **deitensreplicados** listados. Se existirem itens de cópia de segurança, em seguida, execute os passos no [eliminar dados de cópia de segurança e itens de cópia de segurança](#delete-backup-data-and-backup-items).
- Repita [a eliminar o cofre a partir do portal](#delete-the-recovery-services-vault).
- Se todas as dependências são removidas e continuar a obter o *erro ao eliminar o Cofre dos* , em seguida, utilizar ARMClient ferramenta para executar os passos abaixo;

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Instalar o chocolatey partir [aqui](https://chocolatey.org/) e para instalar ARMClient executar o comando abaixo:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Inicie sessão na sua conta do Azure e execute este comando:

    `ARMClient.exe login [environment name]`

3. No portal do Azure, colete o nome de grupo de subscrição ID e recursos, para o cofre que pretende eliminar.

Para obter mais informações sobre ARMClient comando, consulte [documento](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-azure-resource-manager-client-to-delete-recovery-services-vault"></a>Utilize o cliente do Azure Resource Manager para eliminar o Cofre dos serviços de recuperação

1. Execute o comando seguinte com o seu ID de subscrição, o nome do grupo de recursos e o nome do cofre. Ao executar o comando, elimina o Cofre se não tiver quaisquer dependências.

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
2. Se o Cofre não estiver vazio, receberá o erro "Cofre não pode ser eliminado porque existem recursos existentes dentro deste cofre". Para remover um itens protegidos / contentor dentro de um cofre, efetue o seguinte procedimento:

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. No portal do Azure, certifique-se de que o Cofre é eliminado.


## <a name="next-steps"></a>Passos seguintes

[Saiba mais sobre](backup-azure-recovery-services-vault-overview.md) cofres dos serviços de recuperação.<br/>
[Saiba mais sobre](backup-azure-manage-windows-server.md) monitorizar e gerir cofres dos serviços de recuperação.
