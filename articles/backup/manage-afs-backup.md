---
title: Gerir cópias de segurança de partilha de ficheiros do Azure
description: Este artigo descreve tarefas comuns para gerir e monitorizar as ações de ficheiros Azure que são apoiadas pela Azure Backup.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 784b22f11b57fb025f6d9401e10d527c83751898
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88757630"
---
# <a name="manage-azure-file-share-backups"></a>Gerir cópias de segurança de partilha de ficheiros do Azure

Este artigo descreve tarefas comuns para gerir e monitorizar as ações de ficheiros Azure que são apoiadas pela [Azure Backup](./backup-overview.md). Você vai aprender a fazer tarefas de gestão no cofre dos Serviços de Recuperação.

## <a name="monitor-jobs"></a>Monitorizar trabalhos

Quando aciona uma operação de backup ou restauro, o serviço de backup cria um trabalho para rastrear. Pode monitorizar o progresso de todas as tarefas na página **Tarefas de Cópia de Segurança**.

Para abrir a página **Tarefas de Cópia de Segurança**:

1. Abra o cofre dos Serviços de Recuperação que usou para configurar a cópia de segurança para as suas ações de ficheiros. No **painel de visão** geral, selecione **Backup Jobs** na secção **Monitor.**

   ![Trabalhos de reserva na secção de monitorização](./media/manage-afs-backup/backup-jobs.png)

1. Depois de selecionar **OK,** o **painel backup Jobs** lista o estado de todos os trabalhos. Selecione o nome da carga de trabalho que corresponde à partilha de ficheiros que pretende monitorizar.

   ![Nome da carga de trabalho](./media/manage-afs-backup/workload-name.png)

## <a name="create-a-new-policy"></a>Criar uma nova política

Pode criar uma nova política para apoiar as ações de ficheiros Azure a partir da secção de políticas de **backup** do cofre dos Serviços de Recuperação. Todas as políticas criadas quando configuraste a cópia de segurança para ações de ficheiros aparecem com o **Tipo de Política** como **Azure File Share**.

Para visualizar as políticas de backup existentes:

1. Abra o cofre dos Serviços de Recuperação que usou para configurar a cópia de segurança para a partilha de ficheiros. No menu do cofre dos Serviços de Recuperação, selecione **as políticas de Backup** na secção **Gerir.** Todas as políticas de reserva configuradas no cofre aparecem.

   ![Todas as políticas de backup](./media/manage-afs-backup/all-backup-policies.png)

1. Para visualizar políticas específicas do **Azure File Share**, selecione **Azure File Share** da lista de drop-down no canto superior direito.

   ![Selecione Azure File Share](./media/manage-afs-backup/azure-file-share.png)

Para criar uma nova política de backup:

1. No painel **de políticas de backup,** selecione **+ Adicionar**.

   ![Nova política de backup](./media/manage-afs-backup/new-backup-policy.png)

1. No painel **de adicionar,** selecione **Azure File Share** como o Tipo de **Política**. O **painel de política de backup** para **Azure File Share** abre. Especifique o nome da apólice, a frequência de backup e o intervalo de retenção para os pontos de recuperação. Depois de definir a apólice, selecione **OK**.

   ![Defina a política de backup](./media/manage-afs-backup/define-backup-policy.png)

## <a name="modify-policy"></a>Alterar a política

Pode modificar uma política de backup para alterar a frequência de backup ou o intervalo de retenção.

Para modificar uma política:

1. Abra o cofre dos Serviços de Recuperação que usou para configurar a cópia de segurança para a partilha de ficheiros. No menu do cofre dos Serviços de Recuperação, selecione **as políticas de Backup** na secção **Gerir.** Todas as políticas de reserva configuradas no cofre aparecem.

   ![Todas as políticas de backup no cofre](./media/manage-afs-backup/all-backup-policies-modify.png)

1. Para visualizar políticas específicas de uma partilha de ficheiros Azure, selecione **Azure File Share** da lista de drop-down no canto superior direito. Selecione a política de backup que pretende modificar.

   ![Azure partilha de ficheiros para modificar](./media/manage-afs-backup/azure-file-share-modify.png)

1. O **painel de horários** abre. Edite o **horário de backup** e o intervalo de **retenção** conforme necessário e selecione **Save**. Verá uma mensagem "Update in Progress" no painel. Após a atualização da política com sucesso, verá a mensagem "Atualizou com sucesso a política de backup".

   ![Salvar a política modificada](./media/manage-afs-backup/save-policy.png)

## <a name="stop-protection-on-a-file-share"></a>Parar a proteção numa partilha de ficheiros

Existem duas formas de parar a proteção de partilhas de ficheiros do Azure:

* Pare com todos os futuros trabalhos de backup e *elimine todos os pontos de recuperação*.
* Parem com todos os futuros trabalhos de apoio, mas *deixem os pontos de recuperação.*

Pode haver um custo associado a deixar os pontos de recuperação armazenados, porque os instantâneos subjacentes criados pela Azure Backup serão mantidos. O benefício de deixar os pontos de recuperação é que pode restaurar a partilha do ficheiro mais tarde. Para obter informações sobre o custo de sair dos pontos de recuperação, consulte os [detalhes dos preços.](https://azure.microsoft.com/pricing/details/backup/) Se decidir apagar todos os pontos de recuperação, não poderá restaurar a partilha de ficheiros.

Para parar a proteção de uma partilha de ficheiros do Azure:

1. Abra o cofre dos Serviços de Recuperação que contém os pontos de recuperação da partilha de ficheiros. Selecione **Itens de cópia de segurança** na secção **Itens Protegidos.** Aparece a lista de tipos de artigos de backup.

   ![Itens de Cópia de Segurança](./media/manage-afs-backup/backup-items.png)

1. Na lista **Tipo de Gestão de Cópia de Segurança**, selecione **Armazenamento do Azure (Ficheiros do Azure)**. Aparece a lista **de Itens de Reserva (Azure Storage (Ficheiros Azure)).**

   ![Selecione Azure Storage (Ficheiros Azure)](./media/manage-afs-backup/azure-storage-azure-files.png)

1. Na lista **de Itens de Cópia de Segurança (Azure Storage (Ficheiros Azure)),** selecione o item de cópia de segurança para o qual pretende parar a proteção.

1. Selecione a opção **'Stop backup'.**

   ![Selecione Parar backup](./media/manage-afs-backup/stop-backup.png)

1. No painel **stop backup,** selecione **Reter dados de backup** ou eliminar **dados de backup**. Em seguida, **selecione Parar de fazer backup**.

    ![Selecione reter dados de backup ou eliminar dados de backup](./media/manage-afs-backup/retain-or-delete-backup-data.png)

## <a name="resume-protection-on-a-file-share"></a>Retomar a proteção numa partilha de ficheiros

Se a opção **'Reten's Backup Data** foi selecionada quando a proteção para a partilha de ficheiros foi interrompida, é possível retomar a proteção. Se a opção **Eliminar Dados de Cópia de Segurança** foi selecionada, a proteção para a partilha de ficheiros não pode ser retomada.

Para retomar a proteção da partilha de ficheiros Azure:

1. Abra o cofre dos Serviços de Recuperação que contém os pontos de recuperação da partilha de ficheiros. Selecione **Itens de cópia de segurança** na secção **Itens Protegidos.** Aparece a lista de tipos de artigos de backup.

   ![Itens de reserva para currículo](./media/manage-afs-backup/backup-items-resume.png)

1. Na lista **Tipo de Gestão de Cópia de Segurança**, selecione **Armazenamento do Azure (Ficheiros do Azure)**. Aparece a lista **de Itens de Reserva (Azure Storage (Ficheiros Azure)).**

   ![Lista de Armazenamento Azure (Ficheiros Azure)](./media/manage-afs-backup/azure-storage-azure-files.png)

1. Na lista **de Itens de Cópia de Segurança (Azure Storage (Ficheiros Azure)),** selecione o item de cópia de segurança para o qual pretende retomar a proteção.

1. Selecione a opção **de backup do Currículo.**

   ![Selecione retomar a cópia de segurança](./media/manage-afs-backup/resume-backup.png)

1. O painel **de segurança** abre. Selecione uma política à sua escolha para retomar a cópia de segurança.

1. Depois de selecionar uma política de backup, **selecione Guardar**. Verá uma mensagem "Update in Progress" no portal. Após o backup retomar com sucesso, verá a mensagem "Política de backup atualizada com sucesso para a Partilha de Ficheiros Azure Protegido".

   ![Política de backup atualizada com sucesso](./media/manage-afs-backup/successfully-updated.png)

## <a name="delete-backup-data"></a>Eliminar dados de cópia de segurança

Pode eliminar a cópia de segurança de uma partilha de ficheiros durante o trabalho **de backup Stop** ou em qualquer momento após parar a proteção. Pode ser benéfico esperar dias ou mesmo semanas antes de apagar os pontos de recuperação. Quando elimina dados de backup, não é possível escolher pontos de recuperação específicos para eliminar. Se decidir eliminar os seus dados de backup, elimine todos os pontos de recuperação associados à partilha de ficheiros.

O procedimento seguinte pressupõe que a proteção foi interrompida para a partilha de ficheiros.

Para eliminar dados de backup para a partilha de ficheiros Azure:

1. Após a paragem da tarefa de backup, as opções **de cópia de segurança do Currículo** e apagar as opções de **dados de cópia de segurança** estão disponíveis no painel de **instrumentos de cópia de segurança.** Selecione a opção **de dados de cópia de segurança Eliminar.**

   ![Eliminar dados de cópia de segurança](./media/manage-afs-backup/delete-backup-data.png)

1. O painel de **dados de cópia de segurança** apaga-se. Insira o nome da partilha de ficheiros para confirmar a eliminação. Opcionalmente, forneça mais informações nas caixas **De Motivo** ou **Comentários.** Depois de ter a certeza de eliminar os dados de cópia de segurança, selecione **Delete**.

   ![Confirmar eliminar dados](./media/manage-afs-backup/confirm-delete-data.png)

## <a name="unregister-a-storage-account"></a>Não registar uma conta de armazenamento

Para proteger as suas ações de ficheiros numa determinada conta de armazenamento utilizando um cofre diferente dos Serviços de Recuperação, [primeiro pare a proteção para todas as ações de ficheiros](#stop-protection-on-a-file-share) nessa conta de armazenamento. Em seguida, desagrega a conta do cofre atual dos Serviços de Recuperação utilizado para proteção.

O procedimento seguinte pressupõe que a proteção foi interrompida para todas as ações de ficheiro na conta de armazenamento que pretende não registar.

Para desregistralmente a conta de armazenamento:

1. Abra o cofre dos Serviços de Recuperação onde está registada a sua conta de armazenamento.
1. No **painel de visão** geral, selecione a opção **Infraestrutura de backup** na secção **'Gerir'.**

   ![Selecione infraestrutura de backup](./media/manage-afs-backup/backup-infrastructure.png)

1. O **painel de infraestruturas de reserva** abre. Selecione **Contas de Armazenamento** na secção Contas de Armazenamento **Azure.**

   ![Selecione Contas de Armazenamento](./media/manage-afs-backup/storage-accounts.png)

1. Depois de selecionar **contas de armazenamento,** aparece uma lista de contas de armazenamento registadas no cofre.
1. Clique com o botão direito na conta de armazenamento que pretende não registar e selecione **Unregister**.

   ![Selecione Unregister](./media/manage-afs-backup/select-unregister.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte [a Troubleshoot Azure file shares backup](./troubleshoot-azure-files.md).
