---
title: Gerir cópias de segurança de partilha de ficheiros do Azure
description: Este artigo descreve as tarefas comuns para gerenciar e monitorar os compartilhamentos de arquivos do Azure cujo backup é feito pelo serviço de backup do Azure.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: a5477d021b6e3600693e183d8707e11592b7cc38
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294555"
---
# <a name="manage-azure-file-share-backups"></a>Gerir cópias de segurança de partilha de ficheiros do Azure

Este artigo descreve as tarefas comuns para gerenciar e monitorar os compartilhamentos de arquivos do Azure cujo backup é feito pelo serviço de [backup do Azure](https://docs.microsoft.com/azure/backup/backup-overview) . Você aprenderá a executar as seguintes tarefas de gerenciamento no cofre dos serviços de recuperação:

* [Monitorizar trabalhos](#monitor-jobs)
* [Criar uma nova política](#create-a-new-policy)
* [Modificar política](#modify-policy)
* [Parar a proteção numa partilha de ficheiros](#stop-protection-on-a-file-share)
* [Retomar a proteção numa partilha de ficheiros](#resume-protection-on-a-file-share)
* [Eliminar dados de cópia de segurança](#delete-backup-data)
* [Cancelar registro da conta de armazenamento](#unregister-storage-account)

## <a name="monitor-jobs"></a>Monitorizar tarefas

Quando você dispara uma operação de backup ou restauração, o serviço de backup cria um trabalho para acompanhamento. Pode monitorizar o progresso de todas as tarefas na página **Tarefas de Cópia de Segurança**.

Para abrir a página **Tarefas de Cópia de Segurança**:

1. Abra o cofre dos serviços de recuperação que você usou para configurar o backup para seus compartilhamentos de arquivos. Na folha **visão geral** , clique em **trabalhos de backup** na seção **monitoramento** .

   ![Trabalhos de backup na seção monitoramento](./media/manage-afs-backup/backup-jobs.png)

2. Depois de clicar em OK, a folha **trabalhos de backup** será exibida listando o status de todos os trabalhos. Você pode clicar no nome da carga de trabalho correspondente ao compartilhamento de arquivos que deseja monitorar.

   ![Nome da carga de trabalho](./media/manage-afs-backup/workload-name.png)

## <a name="create-a-new-policy"></a>Criar uma nova política

Você pode criar uma nova política para fazer backup de compartilhamentos de arquivos do Azure na seção **políticas de backup** do cofre dos serviços de recuperação. Todas as políticas criadas quando você configurou o backup para compartilhamentos de arquivos aparecem com o tipo de política como compartilhamento de arquivos do Azure.

Para ver as Políticas de cópia de segurança existentes:

1. Abra o cofre dos serviços de recuperação que você usou para configurar o backup de compartilhamento de arquivos e, no menu do cofre dos serviços de recuperação, clique em **políticas de backup** na seção Gerenciar. Todas as políticas de backup configuradas no cofre serão listadas.

   ![Todas as políticas de backup](./media/manage-afs-backup/all-backup-policies.png)

2. Para exibir políticas específicas do compartilhamento de arquivos do Azure, selecione **compartilhamento de arquivos do Azure** na lista suspensa no canto superior direito.

   ![Escolher compartilhamento de arquivos do Azure](./media/manage-afs-backup/azure-file-share.png)

Para criar uma nova Política de cópia de segurança:

1. Clique em **+ Adicionar** na folha políticas de backup.

   ![Nova política de backup](./media/manage-afs-backup/new-backup-policy.png)

2. Selecione **compartilhamento de arquivos do Azure** como o **tipo de política** na folha **Adicionar** . A folha política de backup do compartilhamento de arquivos do Azure será aberta. Especifique o nome da política, a frequência de backup e o período de retenção para os pontos de recuperação. Clique em **OK** quando tiver definido a política.

   ![Definir a política de backup](./media/manage-afs-backup/define-backup-policy.png)

## <a name="modify-policy"></a>Modificar Política

Você pode modificar uma política de backup para alterar a frequência de backup ou o período de retenção.

Para modificar uma política:

1. Abra o cofre dos serviços de recuperação usado para configurar o backup para o compartilhamento de arquivos e, no menu do cofre dos serviços de recuperação, clique em **políticas de backup** na seção Gerenciar. Todas as políticas de backup configuradas no cofre serão listadas.

   ![Todas as políticas de backup no cofre](./media/manage-afs-backup/all-backup-policies-modify.png)

2. Para exibir políticas específicas para um compartilhamento de arquivos do Azure, selecione **compartilhamento de arquivos do Azure** na lista suspensa no canto superior direito. Clique na política de backup que você deseja modificar.

   ![Compartilhamento de arquivos do Azure para modificar](./media/manage-afs-backup/azure-file-share-modify.png)

3. A folha **agenda** será aberta. Edite o período de retenção/agendamento de backup conforme necessário e clique em **salvar**. Você verá uma mensagem de "atualização em andamento" na folha e, quando as alterações de política forem atualizadas com êxito, você verá a mensagem "a política de backup foi atualizada com êxito".

   ![Salvar a política modificada](./media/manage-afs-backup/save-policy.png)

## <a name="stop-protection-on-a-file-share"></a>Parar a proteção numa partilha de ficheiros

Existem duas formas de parar a proteção de partilhas de ficheiros do Azure:

* Parar todos os trabalhos de backup futuros e *excluir todos os pontos de recuperação*
* Parar todos os trabalhos de backup futuros, mas *deixar os pontos de recuperação*

Pode haver um custo associado à saída dos pontos de recuperação no armazenamento, pois os instantâneos subjacentes criados pelo backup do Azure serão mantidos. No entanto, a vantagem de deixar os pontos de recuperação é que pode restaurar a Partilha de ficheiros mais tarde, se assim o desejar. Para obter informações sobre o custo de deixar os pontos de recuperação, consulte os [detalhes de preços](https://azure.microsoft.com/pricing/details/backup/). Se optar por eliminar todos os pontos de recuperação, não pode restaurar a Partilha de ficheiros.

Para parar a proteção de uma partilha de ficheiros do Azure:

1. Abra o cofre dos serviços de recuperação que contém os pontos de recuperação de compartilhamento de arquivos e clique em **itens de backup** na seção itens protegidos. É apresentada a lista de tipos de Itens de Cópia de Segurança.

   ![Itens de cópia de segurança](./media/manage-afs-backup/backup-items.png)

2. Na lista **Tipo de Gestão de Cópia de Segurança**, selecione **Armazenamento do Azure (Ficheiros do Azure)** . A lista de **itens de backup para (armazenamento do Azure (arquivos do Azure))** é exibida.

   ![Selecionar o armazenamento do Azure (arquivos do Azure)](./media/manage-afs-backup/azure-storage-azure-files.png)

3. Na lista de **itens de backup (armazenamento do Azure (arquivos do Azure))** , selecione o item de backup para o qual você deseja interromper a proteção.

4. Selecione a opção **parar backup** no menu da folha do **item de backup** .

   ![Selecione parar backup](./media/manage-afs-backup/stop-backup.png)

5. Na folha **parar backup** , escolha **manter dados de backup** ou **excluir dados de backup** e clique em **parar backup**.

    ![Escolha reter ou excluir dados de backup](./media/manage-afs-backup/retain-or-delete-backup-data.png)

## <a name="resume-protection-on-a-file-share"></a>Retomar a proteção numa partilha de ficheiros

Se a opção **reter dados de backup** foi escolhida quando a proteção para o compartilhamento de arquivos foi interrompida, é possível retomar a proteção. Se escolheu a opção **Eliminar Dados da Cópia de Segurança**, não pode retomar a proteção para a partilha de ficheiros.

Para retomar a proteção para o compartilhamento de arquivos do Azure:

1. Abra o cofre dos serviços de recuperação que contém os pontos de recuperação de compartilhamento de arquivos e clique em **itens de backup** na seção itens protegidos. É apresentada a lista de tipos de Itens de Cópia de Segurança.

   ![Itens de backup para retomar](./media/manage-afs-backup/backup-items-resume.png)

2. Na lista **Tipo de Gestão de Cópia de Segurança**, selecione **Armazenamento do Azure (Ficheiros do Azure)** . A lista de **itens de backup para (armazenamento do Azure (arquivos do Azure))** é exibida.

   ![Lista de armazenamento do Azure (arquivos do Azure)](./media/manage-afs-backup/azure-storage-azure-files.png)

3. Na lista de **itens de backup (armazenamento do Azure (arquivos do Azure))** , selecione o item de backup para o qual você deseja retomar a proteção.

4. Selecione **retomar** opção de backup no menu da folha do **item de backup** .

   ![Selecione retomar backup](./media/manage-afs-backup/resume-backup.png)

5. A folha **política de backup** será aberta e você poderá escolher uma política de sua escolha para retomar o backup.

6. Clique em **salvar** depois de selecionar a **política de backup**desejada. Você verá uma mensagem de "atualização em andamento" no portal e, depois que o backup for retomado com êxito, verá a mensagem "política de backup atualizada com êxito para compartilhamento de arquivos do Azure protegido".

   ![Política de backup atualizada com êxito](./media/manage-afs-backup/successfully-updated.png)

## <a name="delete-backup-data"></a>Eliminar dados de cópia de segurança

Você pode excluir o backup de um compartilhamento de arquivos durante o trabalho **parar backup** ou a qualquer momento após a interrupção da proteção. Até pode ser vantajoso aguardar dias ou semanas antes de eliminar os pontos de recuperação. Ao excluir dados de backup, você não pode escolher pontos de recuperação específicos para excluir. Se você optar por excluir os dados de backup, exclua todos os pontos de recuperação associados ao compartilhamento de arquivos.

O procedimento a seguir pressupõe que a proteção foi interrompida para o compartilhamento de arquivos.

Para excluir dados de backup para o compartilhamento de arquivos do Azure:

1. Depois que o trabalho de backup for interrompido, as opções **retomar backup** e **excluir dados de backup** estarão disponíveis no painel do **item de backup** . Clique na opção **excluir dados de backup** no menu da folha do **item de backup** .

   ![Eliminar dados de cópia de segurança](./media/manage-afs-backup/delete-backup-data.png)

2. A folha **excluir dados de backup** será aberta. Digite o nome do compartilhamento de arquivos para confirmar a exclusão. Opcionalmente, forneça um **motivo** para excluir ou **comentar**. Clique em **excluir** quando tiver certeza de que deseja excluir os dados de backup.

   ![Confirmar exclusão de dados](./media/manage-afs-backup/confirm-delete-data.png)

## <a name="unregister-storage-account"></a>Cancelar registro da conta de armazenamento

Se você quiser proteger seus compartilhamentos de arquivos em uma conta de armazenamento específica usando um cofre de serviços de recuperação diferente, primeiro [interrompa a proteção para todos os compartilhamentos de arquivos](#stop-protection-on-a-file-share) nessa conta de armazenamento. Em seguida, cancele o registro da conta do cofre de serviços de recuperação atual usado para proteção.

O procedimento a seguir pressupõe que a proteção foi interrompida para todos os compartilhamentos de arquivos na conta de armazenamento cujo registro você deseja cancelar.

Para cancelar o registro da conta de armazenamento:

1. Abra o cofre dos serviços de recuperação em que sua conta de armazenamento está registrada.
2. Clique na opção **infraestrutura de backup** na seção **gerenciar** da folha **visão geral** .

   ![Clique em infraestrutura de backup](./media/manage-afs-backup/backup-infrastructure.png)

3. A folha **infraestrutura de backup** é aberta. Clique em **contas de armazenamento** na seção contas de **armazenamento do Azure** nesta folha.

   ![Clique em contas de armazenamento](./media/manage-afs-backup/storage-accounts.png)

4. Depois de clicar em **contas de armazenamento**, uma lista de contas de armazenamento registradas com o cofre será exibida.
5. Clique com o botão direito do mouse na conta de armazenamento que você deseja cancelar o registro e escolha **Cancelar registro**.

   ![Selecione cancelar registro](./media/manage-afs-backup/select-unregister.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte [solucionar problemas de falhas de backup/restauração para compartilhamentos de arquivos do Azure](https://docs.microsoft.com/azure/backup/troubleshoot-azure-files)
