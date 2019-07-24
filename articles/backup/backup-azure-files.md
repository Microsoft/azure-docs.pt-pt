---
title: Fazer cópia de segurança das Partilhas de Ficheiros do Azure
description: Este artigo apresenta detalhes sobre como fazer cópias de segurança e restaurar as partilhas de ficheiros do Azure e explica as tarefas de gestão.
author: rayne-wiselman
ms.author: raynew
ms.date: 01/31/2019
ms.topic: tutorial
ms.service: backup
manager: carmonm
ms.openlocfilehash: 7233db1e685c3edcdbd6a97bc2ae23706ad6f767
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466553"
---
# <a name="back-up-azure-file-shares"></a>Fazer cópia de segurança das partilhas de ficheiros do Azure
Este artigo explica como utilizar o Portal do Azure para fazer a cópia de segurança e restauro das [partilhas de ficheiros do Azure](../storage/files/storage-files-introduction.md).

Neste guia, ficará a saber como:
> [!div class="checklist"]
> * Configurar um cofre dos Serviços de Recuperação para fazer cópias de segurança de Ficheiros do Azure
> * Executar uma tarefa de cópia de segurança a pedido para criar um ponto de restauro
> * Restaurar um ficheiro ou ficheiros a partir de um ponto de restauro
> * Gerir Tarefas de cópia de segurança
> * Parar a proteção dos Ficheiros do Azure
> * Eliminar os dados de cópia de segurança

## <a name="prerequisites"></a>Pré-requisitos
Antes de fazer uma cópia de segurança de uma partilha de ficheiros do Azure, garanta que está presente num dos [tipos de Conta de Armazenamento suportados](backup-azure-files.md#limitations-for-azure-file-share-backup-during-preview). Depois de verificar esta situação, pode proteger as suas partilhas de ficheiros.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Limitações da cópia de segurança da partilha de ficheiros do Azure durante a Pré-visualização
O backup para compartilhamentos de arquivos do Azure está em versão prévia. Há suporte para compartilhamentos de arquivos do Azure em contas de armazenamento de uso geral v1 e de finalidade geral v2. Os seguintes cenários de cópia de segurança não são suportados nas partilhas de ficheiros do Azure:
- O suporte para backup de compartilhamentos de arquivos do Azure em contas de armazenamento com replicação ZRS ( [armazenamento com redundância de zona](../storage/common/storage-redundancy-zrs.md) ) está atualmente limitado a [essas regiões](backup-azure-files-faq.md#in-which-geos-can-i-back-up-azure-file-shares-).
- Não pode proteger partilhas de ficheiros do Azure em contas de armazenamento que têm Redes Virtuais ou Firewall ativadas.
- Não há uma CLI disponível para proteger os arquivos do Azure usando o backup do Azure.
- O número máximo de cópias de segurança agendadas por dia é de um.
- O número máximo de cópias de segurança a pedido por dia é de quatro.
- Utilize os [bloqueios de recursos](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) da conta de armazenamento para impedir a eliminação acidental de cópias de segurança do seu cofre dos Serviços de Recuperação.
- Não elimine os instantâneos criados pelo Azure Backup. A eliminação de instantâneos pode resultar na perda de pontos de recuperação e/ou falhas de restauro.
- Não exclua compartilhamentos de arquivos protegidos pelo backup do Azure. A solução atual excluirá todos os instantâneos feitos pelo backup do Azure quando o compartilhamento de arquivos for excluído e, portanto, perderá todos os pontos de restauração.



## <a name="configuring-backup-for-an-azure-file-share"></a>Configurar cópia de segurança de uma partilha de ficheiros do Azure
Este tutorial parte do princípio que já estabeleceu uma partilha de ficheiros do Azure. Para criar cópias de segurança da partilha de ficheiros do Azure:

1. Crie um cofre dos Serviços de Recuperação na mesma região que a partilha de ficheiros. Se já tiver um cofre, abra a página de Descrição geral do cofre e clique em **Cópia de segurança**.

    ![Escolher a Partilha de Ficheiros do Azure como o Objetivo de cópia de segurança](./media/backup-file-shares/overview-backup-page.png)

2. No menu **meta de backup** , em do **que você deseja fazer backup?** , escolha Azure FileShare.

    ![Escolher a Partilha de Ficheiros do Azure como o Objetivo de cópia de segurança](./media/backup-file-shares/choose-azure-fileshare-from-backup-goal.png)

3. Clique em **Cópia de segurança** para configurar a partilha de ficheiros do Azure para o cofre dos Serviços de Recuperação.

   ![clique em Cópia de segurança para associar a partilha de ficheiros do Azure com o cofre](./media/backup-file-shares/set-backup-goal.png)

    Assim que o cofre estiver associado à partilha de ficheiros do Azure, o menu de Cópia de segurança abre-se e pede-lhe que selecione uma Conta de armazenamento. O menu apresenta todas as Contas de Armazenamento suportadas na região onde existe o cofre, que ainda não estão associadas a um cofre dos Serviços de Recuperação.

   ![clique em Cópia de segurança para associar a partilha de ficheiros do Azure com o cofre](./media/backup-file-shares/list-of-storage-accounts.png)

4. Na lista de Contas de armazenamento, selecione uma conta e clique em **OK**. O Azure procura na conta de armazenamento partilhas de ficheiros que cuja cópia de segurança possa ser feita. Se adicionou as suas partilhas de ficheiros recentemente e não as vê na lista, aguarde algum tempo para as partilhas de ficheiros aparecerem.

   ![clique em Cópia de segurança para associar a partilha de ficheiros do Azure com o cofre](./media/backup-file-shares/discover-file-shares.png)

5. Na lista **Partilhas de Ficheiros**, selecione um ou mais das partilhas de ficheiros que quer fazer cópia de segurança e clique em **OK**.

6. Depois de escolher as Partilhas de Ficheiros, o menu de Cópia de segurança muda para a **Política de cópia de segurança**. Neste menu, selecione uma política de cópia de segurança existente ou crie uma nova e, em seguida, clique em **Ativar a Cópia de Segurança**.

   ![clique em Cópia de segurança para associar a partilha de ficheiros do Azure com o cofre](./media/backup-file-shares/apply-backup-policy.png)

    Depois de estabelecer uma política de cópia de segurança, será captado um instantâneo das Partilhas de Ficheiros à hora agendada e o ponto de recuperação é retido para o período de escolhido.

## <a name="create-an-on-demand-backup"></a>Criar uma cópia de segurança a pedido
Ocasionalmente, poderá gerar um instantâneo de cópia de segurança ou um ponto de recuperação fora das horas agendadas na política de cópia de segurança. Uma hora comum para gerar uma cópia de segurança a pedido é logo depois de configurar a política de cópia de segurança. Com base na agenda na política de cópia de segurança, poderá demorar horas ou dias até tirar um instantâneo. Para proteger os dados até que a política de cópia de segurança seja aplicada, inicie uma cópia de segurança a pedido. Muitas vezes, é necessário criar um backup sob demanda antes de fazer alterações planejadas em seus compartilhamentos de arquivos.

### <a name="to-create-an-on-demand-backup"></a>Para criar uma cópia de segurança a pedido

1. Abra o cofre dos Serviços de Recuperação que contém os pontos de recuperação das partilhas de ficheiros e clique em **Itens de Cópia de Segurança**. É apresentada a lista de tipos de Itens de Cópia de Segurança.

   ![clique em Cópia de segurança para associar a partilha de ficheiros do Azure com o cofre](./media/backup-file-shares/list-of-backup-items.png)

2. Na lista, selecione **Armazenamento do Azure (Ficheiros do Azure)** . É apresentada a lista de partilhas de ficheiros do Azure.

   ![clique em Cópia de segurança para associar a partilha de ficheiros do Azure com o cofre](./media/backup-file-shares/list-of-azure-files-backup-items.png)

3. Na lista de partilhas de ficheiros do Azure, selecione a partilha de ficheiros que quer. Abre-se o menu de Item de Cópia de Segurança para a partilha de ficheiros selecionada.

   ![clique em Cópia de segurança para associar a partilha de ficheiros do Azure com o cofre](./media/backup-file-shares/backup-item-menu.png)

4. No menu de Item de Cópia de Segurança, clique em **Fazer Cópia de Segurança Agora**. Por se tratar de uma tarefa de cópia de segurança a pedido, não há nenhuma política de retenção associada ao ponto de recuperação. Abre-se a caixa de diálogo **Fazer Cópia de Segurança Agora**. Especifique o último dia em que quer reter o ponto de recuperação.

   ![clique em Cópia de segurança para associar a partilha de ficheiros do Azure com o cofre](./media/backup-file-shares/backup-now-menu.png)

## <a name="restore-from-backup-of-azure-file-share"></a>Restaurar a partir da cópia de segurança da partilha de ficheiros do Azure
Se precisar de restaurar uma partilha de ficheiros completa ou ficheiros ou pastas individuais a partir de um Ponto de Restauro, aceda ao Item de Cópia de Segurança conforme detalhado na secção anterior. Escolha **Restaurar Partilha** para restaurar uma partilha de ficheiros completa para um ponto anterior no tempo desejado. Na lista de Pontos de Restauro apresentada, selecione um que consiga Substituir a partilha de ficheiros atual ou Restaurar para uma partilha de ficheiros alternativa na mesma região.

   ![clique em Cópia de segurança para associar a partilha de ficheiros do Azure com o cofre](./media/backup-file-shares/select-restore-location.png)

## <a name="restore-individual-files-or-folders-from-backup-of-azure-file-shares"></a>Restaurar ficheiros ou pastas individuais a partir da cópia de segurança de partilhas de ficheiros do Azure
A Cópia de Segurança do Azure proporciona a capacidade de procurar um Ponto de Restauro no portal do Azure. Para restaurar um ficheiro ou pasta à sua escolha, clique em Recuperação de Ficheiros na página de Item de Cópia de Segurança e escolha um na lista de Pontos de Restauro. Selecione o Destino de Recuperação e, em seguida, clique em **Selecionar Ficheiro** para procurar o ponto de restauro. Selecione o ficheiro ou pasta à sua escolha e **Restaurar**.

   ![clique em Cópia de segurança para associar a partilha de ficheiros do Azure com o cofre](./media/backup-file-shares/restore-individual-files-folders.png)

## <a name="manage-azure-file-share-backups"></a>Gerir cópias de segurança de partilha de ficheiros do Azure

Pode executar várias tarefas de gestão para Cópias de segurança de partilhas de Ficheiros na página **Tarefas de Cópia de Segurança**, incluindo:
- [Monitorizar trabalhos](backup-azure-files.md#monitor-jobs)
- [Criar uma nova política](backup-azure-files.md#create-a-new-policy)
- [Parar a proteção numa partilha de ficheiros](backup-azure-files.md#stop-protecting-an-azure-file-share)
- [Retomar a proteção numa partilha de ficheiros](backup-azure-files.md#resume-protection-for-azure-file-share)
- [Eliminar dados de cópia de segurança](backup-azure-files.md#delete-backup-data)

### <a name="monitor-jobs"></a>Monitorizar trabalhos

Pode monitorizar o progresso de todas as tarefas na página **Tarefas de Cópia de Segurança**.

Para abrir a página **Tarefas de Cópia de Segurança**:

- Abra o cofre dos Serviços de Recuperação que quer monitorizar e no menu do cofre dos Serviços de Recuperação, clique em **Tarefas** e, em seguida, clique em **Tarefas de Cópia de Segurança**.

   ![Selecionar a tarefa que quer monitorizar](./media/backup-file-shares/open-backup-jobs.png)

    É apresentada a lista de Tarefas de cópia de segurança e o estado dessas tarefas.

    ![Selecionar a tarefa que quer monitorizar](./media/backup-file-shares/backup-jobs-progress-list.png)

### <a name="create-a-new-policy"></a>Criar uma nova política

Pode criar uma nova política para fazer cópias de segurança de partilhas de ficheiros do Azure das **Políticas de Cópia de Segurança** do cofre dos Serviços de Recuperação. Todas as políticas criadas quando configurar a Cópia de segurança para partilhas de ficheiros são apresentadas com o Tipo de Política como a Partilha de ficheiros do Azure.

Para ver as Políticas de cópia de segurança existentes:

- Abra o cofre dos Serviços de Recuperação que quer e no menu do cofre dos Serviços de Recuperação, clique em **Políticas de cópia de segurança**. Todas as Políticas de cópia de segurança são listadas.

   ![Selecionar a tarefa que quer monitorizar](./media/backup-file-shares/list-of-backup-policies.png)

Para criar uma nova Política de cópia de segurança:

1. No menu do cofre dos Serviços de Recuperação, clique em **Políticas de cópia de segurança**.
2. Na lista de Políticas de cópias de segurança, clique em **Adicionar**.

   ![Selecionar a tarefa que quer monitorizar](./media/backup-file-shares/new-backup-policy.png)

3. No menu **Adicionar**, selecione **Partilha de Ficheiros do Azure**. Abre-se o menu de Política de cópia de segurança para a partilha de ficheiros do Azure. Proporcione o nome para a política, a frequência de cópia de segurança e o intervalo de retenção para os pontos de recuperação. Clique em OK quando tiver definido a política.

   ![Selecionar a tarefa que quer monitorizar](./media/backup-file-shares/create-new-policy.png)

### <a name="stop-protecting-an-azure-file-share"></a>Parar a proteção de uma partilha de ficheiros do Azure

Se optar por parar de proteger uma partilha de ficheiros do Azure, é-lhe perguntado se quer reter os pontos de recuperação. Existem duas formas de parar a proteção de partilhas de ficheiros do Azure:

- Parar todas as tarefas de cópia de segurança futuras e eliminar todos os pontos de recuperação ou
- Parar todas as tarefas de cópia de segurança futuras, mas manter os pontos de recuperação

Pode haver um custo associado a deixar os pontos de recuperação no armazenamento, uma vez que os instantâneos subjacentes criados pelo Azure Backup serão retidos. No entanto, a vantagem de deixar os pontos de recuperação é que pode restaurar a Partilha de ficheiros mais tarde, se assim o desejar. Para obter mais informações sobre o custo de deixar os pontos de recuperação, veja os detalhes dos preços. Se optar por eliminar todos os pontos de recuperação, não pode restaurar a Partilha de ficheiros.

Para parar a proteção de uma partilha de ficheiros do Azure:

1. Abra o cofre dos Serviços de Recuperação que contém os pontos de recuperação das partilhas de ficheiros e clique em **Itens de Cópia de Segurança**. É apresentada a lista de tipos de Itens de Cópia de Segurança.

   ![clique em Cópia de segurança para associar a partilha de ficheiros do Azure com o cofre](./media/backup-file-shares/list-of-backup-items.png)

2. Na lista **Tipo de Gestão de Cópia de Segurança**, selecione **Armazenamento do Azure (Ficheiros do Azure)** . É apresentada a lista de Itens de Cópia de Segurança para o (Armazenamento do Azure (Ficheiros do Azure)).

   ![clique no item para abrir o menu adicional](./media/backup-file-shares/azure-file-share-backup-items.png)

3. Na lista de Itens de Cópia de Segurança (Armazenamento do Azure (Ficheiros do Azure)), selecione o item de cópia de segurança que quer parar.

4. Nos itens da partilha de ficheiros do Azure, clique no menu **Mais** e selecione **Parar Cópia de Segurança**.

   ![clique no item para abrir o menu adicional](./media/backup-file-shares/stop-backup.png)

5. No menu Parar Cópia de Segurança, opte por **Reter** ou **Eliminar Dados da Cópia de Segurança** e clique em **Parar Cópia de Segurança**.

   ![clique no item para abrir o menu adicional](./media/backup-file-shares/retain-data.png)

### <a name="resume-protection-for-azure-file-share"></a>Retomar a proteção da partilha de ficheiros do Azure

Se a opção Reter Dados de Cópia de Segurança foi escolhida quando a proteção para a partilha de ficheiros foi parada, pode retomar a proteção. Se escolheu a opção **Eliminar Dados da Cópia de Segurança**, não pode retomar a proteção para a partilha de ficheiros.

Para retomar a proteção para a partilha de ficheiros, aceda ao Item de Cópia de Segurança e clique em Retomar Cópia de Segurança. Abre-se a Política de Cópia de Segurança e pode escolher uma política à sua escolha para retomar a cópia de segurança.

   ![Selecionar a tarefa que quer monitorizar](./media/backup-file-shares/resume-backup-job.png)

### <a name="delete-backup-data"></a>Eliminar Dados de cópia de segurança

Pode eliminar a cópia de segurança de uma partilha de ficheiros durante a tarefa Parar cópia de segurança ou a qualquer altura depois de parar a proteção. Até pode ser vantajoso aguardar dias ou semanas antes de eliminar os pontos de recuperação. Ao contrário de restaurar pontos de recuperação, ao eliminar dados de cópia de segurança, não pode escolher pontos de recuperação específicos para eliminar. Se optar por eliminar os dados de cópia de segurança, tem de eliminar todos os pontos de recuperação associados ao item.

O procedimento seguinte assume que a tarefa de Cópia de segurança para a máquina virtual foi parada. Depois de parar a Tarefa de cópia de segurança, as opções Retomar a cópia de segurança e Eliminar Dados da Cópia de Segurança estão disponíveis no dashboard do item de Cópia de segurança. Clique em Eliminar Dados de Cópia de Segurança e escreva o nome da Partilha de ficheiros para confirmar a eliminação. Opcionalmente, indique um Motivo para eliminar ou um Comentário.

## <a name="see-also"></a>Consultar Também
Para obter mais informações sobre as partilhas de ficheiros do Azure, veja
- [FAQ sobre a cópia de segurança da partilha de ficheiros do Azure](backup-azure-files-faq.md)
- [Resolver problemas com a cópia de segurança das partilhas de ficheiros do Azure](troubleshoot-azure-files.md)
