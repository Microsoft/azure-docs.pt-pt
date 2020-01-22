---
title: Fazer backup de compartilhamentos de arquivos do Azure no portal do Azure
description: Saiba como usar o portal do Azure para fazer backup de compartilhamentos de arquivos do Azure no cofre dos serviços de recuperação
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: b2e2053857147513a95b3ae72b82d55450ebcffa
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294516"
---
# <a name="back-up-azure-file-shares-in-a-recovery-services-vault"></a>Fazer backup de compartilhamentos de arquivos do Azure em um cofre dos serviços de recuperação

Este artigo explica como usar o portal do Azure para fazer backup de [compartilhamentos de arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

Neste guia, ficará a saber como:

* Criar um cofre dos Serviços de Recuperação
* Descobrir compartilhamentos de FileShare e configurar backups
* Executar uma tarefa de cópia de segurança a pedido para criar um ponto de restauro

## <a name="prerequisites"></a>Pré-requisitos

* Identifique ou crie um [cofre dos serviços de recuperação](#create-a-recovery-services-vault) na mesma região que a conta de armazenamento que hospeda o compartilhamento de arquivos.

* Verifique se o compartilhamento de arquivos está presente em um dos [tipos de conta de armazenamento com suporte](#limitations-for-azure-file-share-backup-during-preview).

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Limitações da cópia de segurança da partilha de ficheiros do Azure durante a Pré-visualização

O backup para compartilhamentos de arquivos do Azure está em versão prévia. Há suporte para compartilhamentos de arquivos do Azure em contas de armazenamento de uso geral v1 e de finalidade geral v2. Estas são as limitações para fazer backup de compartilhamentos de arquivos do Azure:

* O suporte para backup de compartilhamentos de arquivos do Azure em contas de armazenamento com replicação ZRS ( [armazenamento com redundância de zona](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) ) está atualmente limitado a [essas regiões](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#in-which-geos-can-i-back-up-azure-file-shares).
* O backup do Azure atualmente dá suporte à configuração de backups agendados uma vez por dia de compartilhamentos de arquivos
* O número máximo de cópias de segurança agendadas por dia é de um.
* O número máximo de cópias de segurança a pedido por dia é de quatro.
* Utilize os [bloqueios de recursos](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) da conta de armazenamento para impedir a eliminação acidental de cópias de segurança do seu cofre dos Serviços de Recuperação.
* Não elimine os instantâneos criados pelo Azure Backup. A eliminação de instantâneos pode resultar na perda de pontos de recuperação e/ou falhas de restauro.
* Não exclua compartilhamentos de arquivos protegidos pelo backup do Azure. A solução atual excluirá todos os instantâneos feitos pelo backup do Azure depois que o compartilhamento de arquivos for excluído e, portanto, todos os pontos de restauração serão perdidos.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Modificar a replicação de armazenamento

Por padrão, os cofres usam o [grs (armazenamento com redundância geográfica)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Se o cofre for seu mecanismo de backup primário, recomendamos que você use GRS.

* Você pode usar o [LRS (armazenamento com redundância local)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) como uma opção de baixo custo.

Modifique o tipo de replicação de armazenamento da seguinte maneira:

1. No novo cofre, clique em **Propriedades** na seção **configurações** .

2. Em **Propriedades**, em **configuração de backup**, clique em **Atualizar**.

3. Selecione o tipo de replicação de armazenamento e clique em **salvar**.

    ![Atualizar configuração de backup](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> Não é possível modificar o tipo de replicação de armazenamento depois que o cofre é configurado e contém itens de backup. Se desejar fazer isso, você precisará recriar o cofre.
>

## <a name="discover-file-shares-and-configure-backup"></a>Descobrir compartilhamentos de arquivos e configurar o backup

1. No [portal do Azure](https://portal.azure.com/), abra o cofre dos serviços de recuperação que você deseja usar para fazer backup do compartilhamento de arquivos.

2. No painel do **cofre dos serviços de recuperação** , selecione **+ backup**.

   ![Cofre dos Serviços de Recuperação](./media/backup-afs/recovery-services-vault.png)

   a. Em **meta de backup**, defina **onde sua carga de trabalho está em execução? para o** **Azure**.

    ![Escolha o compartilhamento de arquivos do Azure como meta de backup](./media/backup-afs/backup-goal.png)

    b.    Em **o que você deseja fazer backup**, selecione **compartilhamento de arquivos do Azure** no menu suspenso.

    c.    Clique em **backup** para registrar a extensão de compartilhamento de arquivos do Azure no cofre.

      ![Clique em backup para associar o compartilhamento de arquivos do Azure ao cofre](./media/backup-afs/register-extension.png)

3. Depois de clicar em **backup**, a folha backup será aberta e solicitará que você selecione uma conta de armazenamento de uma lista de contas de armazenamento descobertos com suporte. Eles estão associados a este cofre ou estão presentes na mesma região que o cofre, mas ainda não estão associados a nenhum cofre de serviços de recuperação.

   ![Selecionar a conta de armazenamento](./media/backup-afs/select-storage-account.png)

4. Na lista de contas de armazenamento descobertas, selecione uma conta e clique em **OK**. O Azure procura na conta de armazenamento partilhas de ficheiros que cuja cópia de segurança possa ser feita. Se você adicionou recentemente os compartilhamentos de arquivos e não os vê na lista, forneça algum tempo para que os compartilhamentos de arquivos sejam exibidos.

    ![Descobrindo compartilhamentos de arquivos](./media/backup-afs/discovering-file-shares.png)

5. Na lista **compartilhamentos de arquivos** , selecione um ou mais dos compartilhamentos de arquivos que você deseja fazer backup e clique em **OK**.

6. Depois de escolher os compartilhamentos de arquivos, o menu **backup** alterna para a **política de backup**. Nesse menu, selecione uma política de backup existente ou crie uma nova e, em seguida, clique em **habilitar backup**.

    ![Selecionar política de backup](./media/backup-afs/select-backup-policy.png)

Depois de estabelecer uma política de cópia de segurança, será captado um instantâneo das Partilhas de Ficheiros à hora agendada e o ponto de recuperação é retido para o período de escolhido.

## <a name="create-an-on-demand-backup"></a>Criar uma cópia de segurança a pedido

Ocasionalmente, talvez você queira gerar um instantâneo de backup ou um ponto de recuperação, fora dos horários agendados na política de backup. Um motivo comum para gerar um backup sob demanda é logo depois de configurar a política de backup. Com base na agenda na política de cópia de segurança, poderá demorar horas ou dias até tirar um instantâneo. Para proteger os dados até que a política de cópia de segurança seja aplicada, inicie uma cópia de segurança a pedido. Muitas vezes, é necessário criar um backup sob demanda antes de fazer alterações planejadas em seus compartilhamentos de arquivos.

### <a name="to-create-an-on-demand-backup"></a>Para criar uma cópia de segurança a pedido

1. Abra o cofre dos serviços de recuperação que você usou para fazer backup do compartilhamento de arquivos e clique em **itens de backup** na seção **itens protegidos** da folha **visão geral** .

   ![Clique em itens de backup](./media/backup-afs/backup-items.png)

2. Depois de clicar em **itens de backup**, uma nova folha listando todos os tipos de gerenciamento de **backup** será exibida ao lado da folha **visão geral** da seguinte maneira:

   ![Lista de tipos de gerenciamento de backup](./media/backup-afs/backup-management-types.png)

3. Na lista de **tipos de gerenciamento de backup**, selecione **armazenamento do Azure (arquivos do Azure)** . Você verá uma lista de todos os compartilhamentos de arquivos e das contas de armazenamento correspondentes com backup usando esse cofre.

   ![Itens de backup do armazenamento do Azure (arquivos do Azure)](./media/backup-afs/azure-files-backup-items.png)

4. Na lista de partilhas de ficheiros do Azure, selecione a partilha de ficheiros que quer. Os detalhes do **item de backup** são exibidos. No menu **item de backup** , clique em **fazer backup agora**. Por se tratar de uma tarefa de cópia de segurança a pedido, não há nenhuma política de retenção associada ao ponto de recuperação.

   ![Clique em fazer backup agora](./media/backup-afs/backup-now.png)

5. A folha **backup agora** é aberta. Especifique o último dia em que quer reter o ponto de recuperação. Você pode ter uma retenção máxima de 10 anos para um backup sob demanda.

   ![Escolher data de retenção](./media/backup-afs/retention-date.png)

6. Clique em **OK** para confirmar a execução do trabalho de backup sob demanda.

7. Monitore as notificações do portal para manter um controle da conclusão da execução do trabalho de backup. Você pode monitorar o andamento do trabalho no painel do cofre > **trabalhos de Backup** > **em andamento**.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [restaurar compartilhamentos de arquivos do Azure](restore-afs.md)

* Saiba como [gerenciar backups de compartilhamento de arquivos do Azure](manage-afs-backup.md)
