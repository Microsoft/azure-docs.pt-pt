---
title: Apoie um SQL Server VM a partir do painel VM
description: Neste artigo, aprenda a fazer o back bases de dados do SQL Server em máquinas virtuais Azure a partir do painel VM.
ms.topic: conceptual
ms.date: 08/13/2020
ms.openlocfilehash: 4f4ea202ee96e93a621c8dd0025c9ebc8b8d445d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88891662"
---
# <a name="back-up-a-sql-server-from-the-vm-pane"></a>Apoie um Servidor SQL a partir do painel VM

Este artigo explica como fazer backup do SQL Server em funcionamento em VMs Azure com o serviço [Azure Backup.](backup-overview.md) Pode fazer o back up SQL Server VMs utilizando dois métodos:

- Single SQL Server Azure VM: As instruções deste artigo descrevem como fazer o back up de um SQL Server VM diretamente a partir da vista VM.
- Vários VMs sql server: Pode configurar um cofre dos Serviços de Recuperação e configurar a cópia de segurança para vários VMs. Siga as instruções [deste artigo](backup-sql-server-database-azure-vms.md) para este cenário.

## <a name="before-you-start"></a>Antes de começar

1. Verifique o seu ambiente com a [matriz de suporte](sql-support-matrix.md).
2. Obtenha uma [visão geral](backup-azure-sql-database.md) do Azure Backup para SQL Server VM.
3. Verifique se o VM tem [conectividade de rede.](backup-sql-server-database-azure-vms.md#establish-network-connectivity)

## <a name="configure-backup-on-the-sql-server"></a>Configurar backup no SQL Server

Pode ativar a cópia de segurança no seu SQL Server VM a partir do painel **de backup** no VM. Este método faz duas coisas:

- Regista o SQL VM com o serviço Azure Backup para lhe dar acesso.
- Autoprotegem todas as instâncias do SQL Server que estão a decorrer dentro do VM. Isto significa que a política de backup é aplicada a todas as bases de dados existentes, bem como às bases de dados que serão adicionadas a estas instâncias no futuro.

1. Selecione o banner na parte superior da página para abrir a visualização de backup do SQL Server.

    ![Vista de backup do SQL Server](./media/backup-sql-server-vm-from-vm-pane/sql-server-backup-view.png)

    >[!NOTE]
    >Não vê o estandarte? O banner é apresentado apenas para os VMs do SqL Server que são criados usando imagens do Azure Marketplace. É adicionalmente exibido para os VMs que estão protegidos com backup Azure VM. Para outras imagens, pode configurar backup como explicado [aqui](backup-sql-server-database-azure-vms.md).

2. Insira o nome do cofre dos Serviços de Recuperação. Um cofre é uma entidade lógica para armazenar e gerir todos os seus backups. Se criar um novo cofre:

    - Será criado na mesma subscrição e região que o SQL Server VM que está a proteger.
    - Será criado com a definição de armazenamento geo-redundante (GRS) para todos os backups. Se desejar alterar o tipo de redundância, deve fazê-lo antes de proteger o VM. Para obter mais informações, consulte [este artigo](backup-create-rs-vault.md#set-storage-redundancy).

3. Escolha uma **política de backup**. Pode escolher entre a política padrão, ou quaisquer outras políticas existentes que tenha criado no cofre. Se quiser criar uma nova política, pode consultar [este artigo](backup-sql-server-database-azure-vms.md#create-a-backup-policy) para um guia passo a passo.

    ![Escolher uma política de cópias de segurança](./media/backup-sql-server-vm-from-vm-pane/backup-policy.png)

4. Selecione **Ativar Cópia de Segurança**. A operação pode demorar alguns minutos a ser concluída.

    ![Selecione ativar a cópia de segurança](./media/backup-sql-server-vm-from-vm-pane/enable-backup.png)

5. Assim que a operação estiver concluída, verá o nome do **cofre** no estandarte.

    ![Nome do cofre aparece no banner](./media/backup-sql-server-vm-from-vm-pane/vault-name.png)

6. Selecione o banner para ir à vista do cofre, onde pode ver todos os VMs registados e o seu estado de proteção.

    ![Vista do cofre com VMs registados](./media/backup-sql-server-vm-from-vm-pane/vault-view.png)

7. Para imagens não-marketplace, o registo pode ser bem sucedido, mas **a cópia de segurança configurada** pode não ser acionada até que a extensão de Backup Azure tenha permissão no SqL Server. Nestes casos, a coluna **de prontidão de reserva** diz **"Não Pronto".** Tem de [atribuir manualmente as permissões apropriadas](backup-azure-sql-database.md#set-vm-permissions) para imagens não-marketplace para que a cópia de segurança possa ser ativada.

    ![A prontidão de reserva não está pronta.](./media/backup-sql-server-vm-from-vm-pane/backup-readiness-not-ready.png)

8. Para mais operações ou monitorização que precisa de fazer no SQL Server VM, vá ao cofre correspondente dos Serviços de Recuperação. Vá a **Itens de Reserva** para ver todas as bases de dados apoiadas neste cofre, e desencadeie operações como backup a pedido e restauro. Da mesma forma, vá ao **Backup Jobs** para [monitorizar](manage-monitor-sql-database-backup.md) postos de trabalho correspondentes a operações como proteção de configuração, backup e restauro.

    ![Ver bases de dados de backup em Itens de Backup](./media/backup-sql-server-vm-from-vm-pane/backup-items.png)

>[!NOTE]
>A cópia de segurança não é configurada automaticamente em nenhuma das novas instâncias do SQL Server que podem ser adicionadas mais tarde ao VM protegido. Para configurar o backup das instâncias recém-adicionadas, você precisa ir ao cofre para o qual o VM está registrado e seguir os passos listados [aqui](backup-sql-server-database-azure-vms.md).

## <a name="next-steps"></a>Passos seguintes

Aprenda a:

- [Restaurar bases de dados de servidores SQL com ressarçamento](restore-sql-database-azure-vm.md)
- [Gerir bases de dados de servidores SQL com apoio](manage-monitor-sql-database-backup.md)
