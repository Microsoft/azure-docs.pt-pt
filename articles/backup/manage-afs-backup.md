---
title: Gerir cópias de segurança de partilha de ficheiros do Azure
description: Este artigo descreve tarefas comuns para gerir e monitorizar as ações de ficheiro supérno do Azure que são apoiadas pela Azure Backup.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: cb764fa441c063328dc350cf26f42c5bc7a0ca99
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76935880"
---
# <a name="manage-azure-file-share-backups"></a>Gerir cópias de segurança de partilha de ficheiros do Azure

Este artigo descreve tarefas comuns para gerir e monitorizar as ações de ficheiro supérno do Azure que são apoiadas pela [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview). Vais aprender a fazer tarefas de gestão no cofre dos Serviços de Recuperação.

## <a name="monitor-jobs"></a>Monitorizar tarefas

Quando você dispara uma operação de backup ou restauração, o serviço de backup cria um trabalho para acompanhamento. Pode monitorizar o progresso de todas as tarefas na página **Tarefas de Cópia de Segurança**.

Para abrir a página **Tarefas de Cópia de Segurança**:

1. Abra o cofre dos serviços de recuperação que você usou para configurar o backup para seus compartilhamentos de arquivos. No painel **de visão geral,** selecione **Backup Jobs** sob a secção **de monitorização.**

   ![Trabalhos de backup na seção monitoramento](./media/manage-afs-backup/backup-jobs.png)

1. Depois de selecionar **OK,** o painel **Backup Jobs** lista o estado de todos os empregos. Selecione o nome da carga de trabalho correspondente à partilha de ficheiros que pretende monitorizar.

   ![Nome da carga de trabalho](./media/manage-afs-backup/workload-name.png)

## <a name="create-a-new-policy"></a>Criar uma nova política

Pode criar uma nova política para apoiar as ações de ficheiros azure da secção de políticas de **backup** do cofre dos Serviços de Recuperação. Todas as políticas criadas quando configuraram a cópia de segurança para partilhas de ficheiros aparecem com o **Tipo de Política** como Partilha de **Ficheiros Azure**.

Para ver as políticas de backup existentes:

1. Abra o cofre dos Serviços de Recuperação que usou para configurar a cópia de segurança para a partilha de ficheiros. No menu do cofre dos Serviços de Recuperação, selecione **as políticas** de backup sob a secção **Gerir.** Todas as políticas de reserva configuradas no cofre aparecem.

   ![Todas as políticas de backup](./media/manage-afs-backup/all-backup-policies.png)

1. Para ver políticas específicas para a Partilha de **Ficheiros Azure,** selecione **Azure File Share** da lista de drop-down no canto superior direito.

   ![Selecione Partilha de Ficheiros Azure](./media/manage-afs-backup/azure-file-share.png)

Para criar uma nova política de backup:

1. No painel de **políticas de backup,** selecione **+ Adicionar**.

   ![Nova política de backup](./media/manage-afs-backup/new-backup-policy.png)

1. No painel **Adicionar,** selecione **Azure File Share** como o Tipo de **Política**. Abre o painel de **política de backup** para a Partilha de **Ficheiros Azure.** Especifique o nome da política, a frequência de backup e o período de retenção para os pontos de recuperação. Depois de definir a apólice, selecione **OK**.

   ![Definir a política de backup](./media/manage-afs-backup/define-backup-policy.png)

## <a name="modify-policy"></a>Modificar política

Você pode modificar uma política de backup para alterar a frequência de backup ou o período de retenção.

Para modificar uma política:

1. Abra o cofre dos Serviços de Recuperação que usou para configurar a cópia de segurança para a partilha de ficheiros. No menu do cofre dos Serviços de Recuperação, selecione **as políticas** de backup sob a secção **Gerir.** Todas as políticas de reserva configuradas no cofre aparecem.

   ![Todas as políticas de backup no cofre](./media/manage-afs-backup/all-backup-policies-modify.png)

1. Para ver políticas específicas para uma partilha de ficheiros Azure, **selecione Azure File Share** da lista de drop-down no canto superior direito. Selecione a política de backup que pretende modificar.

   ![Partilha de ficheiros Azure para modificar](./media/manage-afs-backup/azure-file-share-modify.png)

1. O painel de **horários** abre. Editar a gama de **reserva** e **retenção** conforme necessário, e selecione **Guardar**. Verá uma mensagem "Update in Progress" no painel. Após a atualização das alterações de política com sucesso, verá a mensagem "Atualizou com sucesso a política de backup".

   ![Salvar a política modificada](./media/manage-afs-backup/save-policy.png)

## <a name="stop-protection-on-a-file-share"></a>Parar a proteção numa partilha de ficheiros

Existem duas formas de parar a proteção de partilhas de ficheiros do Azure:

* Pare todos os futuros trabalhos de backup e *elimine todos os pontos de recuperação.*
* Parem todos os futuros trabalhos de apoio, mas *deixem os pontos de recuperação.*

Pode haver um custo associado à saída dos pontos de recuperação no armazenamento, porque os instantâneos subjacentes criados pela Azure Backup serão mantidos. O benefício de deixar os pontos de recuperação é que pode restaurar a parte do ficheiro mais tarde. Para obter informações sobre o custo de deixar os pontos de recuperação, consulte os [detalhes de preços](https://azure.microsoft.com/pricing/details/backup/). Se decidir eliminar todos os pontos de recuperação, não pode restaurar a parte do ficheiro.

Para parar a proteção de uma partilha de ficheiros do Azure:

1. Abra o cofre dos Serviços de Recuperação que contém os pontos de recuperação de partilhas de ficheiros. Selecione itens de **backup** sob a secção **itens protegidos.** A lista de tipos de itens de reserva aparece.

   ![Itens de backup](./media/manage-afs-backup/backup-items.png)

1. Na lista **Tipo de Gestão de Cópia de Segurança**, selecione **Armazenamento do Azure (Ficheiros do Azure)** . Aparece a lista de **itens de backup (Ficheiros Azure).**

   ![Selecionar o armazenamento do Azure (arquivos do Azure)](./media/manage-afs-backup/azure-storage-azure-files.png)

1. Na lista **de Itens de Backup (Ficheiros Azure),** selecione o item de backup para o qual pretende parar a proteção.

1. Selecione a opção **de backup Stop.**

   ![Selecione parar backup](./media/manage-afs-backup/stop-backup.png)

1. No painel **stop backup,** selecione **Reter dados** de backup ou eliminar dados de **backup**. Em seguida, selecione **Parar a cópia de segurança**.

    ![Selecione reter dados de backup ou eliminar dados de backup](./media/manage-afs-backup/retain-or-delete-backup-data.png)

## <a name="resume-protection-on-a-file-share"></a>Retomar a proteção numa partilha de ficheiros

Se a opção **Reter Dados** de Backup foi selecionada quando a proteção para a parte do ficheiro foi interrompida, é possível retomar a proteção. Se a opção Eliminar dados de **backup** foi selecionada, a proteção para a parte do ficheiro não pode ser retomada.

Para retomar a proteção para o compartilhamento de arquivos do Azure:

1. Abra o cofre dos Serviços de Recuperação que contém os pontos de recuperação de partilhas de ficheiros. Selecione itens de **backup** sob a secção **itens protegidos.** A lista de tipos de itens de reserva aparece.

   ![Itens de backup para retomar](./media/manage-afs-backup/backup-items-resume.png)

1. Na lista **Tipo de Gestão de Cópia de Segurança**, selecione **Armazenamento do Azure (Ficheiros do Azure)** . Aparece a lista de **itens de backup (Ficheiros Azure).**

   ![Lista de armazenamento do Azure (arquivos do Azure)](./media/manage-afs-backup/azure-storage-azure-files.png)

1. Na lista **de Itens de Backup (Ficheiros Azure),** selecione o item de backup para o qual pretende retomar a proteção.

1. Selecione a opção **de cópia de segurança Do Currículo.**

   ![Selecione cópia de segurança do Retomar](./media/manage-afs-backup/resume-backup.png)

1. O painel de **política de backup** abre. Selecione uma política à sua escolha para retomar o backup.

1. Depois de selecionar uma política de backup, selecione **Guardar**. Verá uma mensagem "Update in Progress" no portal. Depois de o backup recomeçar com sucesso, verá a mensagem "Atualizada com sucesso a política de backup para a Partilha de Ficheiros Azure Protegido".

   ![Política de backup atualizada com êxito](./media/manage-afs-backup/successfully-updated.png)

## <a name="delete-backup-data"></a>Eliminar dados de cópia de segurança

Pode eliminar a cópia de segurança de uma parte de ficheiro durante o trabalho de **backup Stop,** ou em qualquer momento após parar a proteção. Pode ser benéfico esperar dias ou mesmo semanas antes de apagar os pontos de recuperação. Ao eliminar dados de backup, não é possível escolher pontos de recuperação específicos para eliminar. Se decidir eliminar os seus dados de backup, elimina todos os pontos de recuperação associados à partilha de ficheiros.

O procedimento seguinte pressupõe que a proteção foi interrompida para a partilha do ficheiro.

Para eliminar os dados de backup da partilha de ficheiros Azure:

1. Após o trabalho de backup ser interrompido, as opções de **backup do Resume** e **eliminar** estão disponíveis no painel de instrumentos de **backup Item.** Selecione a opção eliminar dados de **backup.**

   ![Eliminar dados de cópia de segurança](./media/manage-afs-backup/delete-backup-data.png)

1. Abre-se o painel de dados de cópia de **segurança Eliminar.** Insira o nome da parte do ficheiro para confirmar a eliminação. Opcionalmente, forneça mais informações nas caixas **Reason** ou **Comments.** Depois de ter a certeza de eliminar os dados de cópia de segurança, selecione **Eliminar**.

   ![Confirmar exclusão de dados](./media/manage-afs-backup/confirm-delete-data.png)

## <a name="unregister-a-storage-account"></a>Cancelar o registro de uma conta de armazenamento

Para proteger as suas ações de ficheiro numa determinada conta de armazenamento utilizando um cofre de serviços de recuperação diferente, a primeira [paragem de proteção para todas as ações](#stop-protection-on-a-file-share) de ficheiro supor nessa conta de armazenamento. Em seguida, cancele o registro da conta do cofre de serviços de recuperação atual usado para proteção.

O procedimento seguinte pressupõe que a proteção foi interrompida para todas as ações de arquivo na conta de armazenamento que pretende desregistar.

Para cancelar o registro da conta de armazenamento:

1. Abra o cofre dos Serviços de Recuperação onde a sua conta de armazenamento está registada.
1. No painel **de visão geral,** selecione a opção **Infraestrutura de backup** sob a secção **Gerir.**

   ![Selecionar a infraestrutura de backup](./media/manage-afs-backup/backup-infrastructure.png)

1. O painel **de infraestruturas de reserva** abre. Selecione Contas de **Armazenamento** no âmbito da secção Contas de **Armazenamento Azure.**

   ![Selecione Contas de Armazenamento](./media/manage-afs-backup/storage-accounts.png)

1. Depois de selecionar Contas de **Armazenamento,** aparece uma lista de contas de armazenamento registadas no cofre.
1. Clique na conta de armazenamento que deseja desregistar e **selecione Desregistar**.

   ![Selecione Não registar](./media/manage-afs-backup/select-unregister.png)

## <a name="next-steps"></a>Passos seguintes

Para mais informações, consulte [o ficheiro Troubleshoot Azure partilha backup](https://docs.microsoft.com/azure/backup/troubleshoot-azure-files).
