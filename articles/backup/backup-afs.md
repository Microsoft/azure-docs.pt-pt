---
title: Fazer backup de compartilhamentos de arquivos do Azure no portal do Azure
description: Saiba como usar o portal Azure para apoiar as ações de ficheiros azure no cofre dos Serviços de Recuperação
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: c1dea6925bad96be178f875567077fafa4db9326
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938064"
---
# <a name="back-up-azure-file-shares-in-a-recovery-services-vault"></a>Back up Ações de arquivo Azure em cofre de Serviços de Recuperação

Este artigo explica como usar o portal do Azure para fazer backup de [compartilhamentos de arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

Neste artigo, você aprenderá a:

* Crie um cofre dos Serviços de Recuperação.
* Descubra as partilhas de ficheiros e configure cópias de segurança.
* Faça um trabalho de reserva a pedido para criar um ponto de restauro.

## <a name="prerequisites"></a>Pré-requisitos

* Identifique ou crie um cofre de Serviços de [Recuperação](#create-a-recovery-services-vault) na mesma região que a conta de armazenamento que acolhe a partilha de ficheiros.
* Certifique-se de que a parte do ficheiro está presente num dos tipos de conta de [armazenamento suportado](#limitations-for-azure-file-share-backup-during-preview).

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Limitações para o backup do compartilhamento de arquivos do Azure durante a visualização

A cópia de segurança de partilhas de ficheiros do Azure está em pré-visualização. Há suporte para compartilhamentos de arquivos do Azure em contas de armazenamento de uso geral v1 e de finalidade geral v2. Aqui estão as limitações para apoiar as ações de ficheiros Azure:

* O suporte para cópia de segurança das ações de ficheiros Azure em contas de armazenamento com replicação de [armazenamento redundante](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) (ZRS) está atualmente limitado a [estas regiões.](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#in-which-geos-can-i-back-up-azure-file-shares)
* O backup do Azure atualmente dá suporte à configuração de backups agendados uma vez por dia de compartilhamentos de arquivos
* O número máximo de cópias de segurança agendadas por dia é de um.
* O número máximo de cópias de segurança a pedido por dia é de quatro.
* Utilize os [bloqueios de recursos](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) da conta de armazenamento para impedir a eliminação acidental de cópias de segurança do seu cofre dos Serviços de Recuperação.
* Não apague as imagens criadas pela Azure Backup. A apagar instantâneos pode resultar na perda de pontos de recuperação ou restabelecer falhas.
* Não elimine as ações de ficheiros protegidas pela Azure Backup. A solução atual elimina todas as imagens tiradas pela Azure Backup após a eliminação da parte do ficheiro, pelo que todos os pontos de restauro serão perdidos.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Modificar a replicação de armazenamento

Por padrão, os cofres usam o [grs (armazenamento com redundância geográfica)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Se o cofre for o seu principal mecanismo de reserva, recomendamos que utilize GRS.
* Você pode usar o [LRS (armazenamento com redundância local)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) como uma opção de baixo custo.

Para modificar o tipo de replicação de armazenamento:

1. No novo cofre, selecione **Propriedades** sob a secção **Definições.**

1. Na página **Propriedades,** sob **configuração de backup,** selecione **Atualização**.

1. Selecione o tipo de replicação de armazenamento e selecione **Guardar**.

    ![Atualizar configuração de backup](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> Não é possível modificar o tipo de replicação de armazenamento depois que o cofre é configurado e contém itens de backup. Se queres fazer isto, tens de recriar o cofre.
>

## <a name="discover-file-shares-and-configure-backup"></a>Descubra as partilhas de ficheiros e configure a cópia de segurança

1. No [portal do Azure](https://portal.azure.com/), abra o cofre dos serviços de recuperação que você deseja usar para fazer backup do compartilhamento de arquivos.

1. No painel de **abóbadas** dos Serviços de Recuperação, selecione **+Backup**.

   ![Cofre dos Serviços de Recuperação](./media/backup-afs/recovery-services-vault.png)

    a. Em **meta de backup**, defina **onde sua carga de trabalho está em execução? para o** **Azure**.

    ![Escolha a Partilha de Ficheiros Azure como objetivo de backup](./media/backup-afs/backup-goal.png)

    b.  Em O que quer fazer de **reserva?**

    c.  Selecione **Backup** para registar a extensão de partilha de ficheiros Azure no cofre.

    ![Selecione Backup para associar a partilha de ficheiros Azure com cofre](./media/backup-afs/register-extension.png)

1. Depois de selecionar **Backup**, o painel **de backup** abre e pede-lhe para selecionar uma conta de armazenamento a partir de uma lista de contas de armazenamento suportadas descobertas. Ou estão associados a este cofre ou presentes na mesma região que o cofre, mas ainda não estão associados a qualquer cofre dos Serviços de Recuperação.

   ![Selecionar a conta de armazenamento](./media/backup-afs/select-storage-account.png)

1. A partir da lista de contas de armazenamento descobertas, selecione uma conta e selecione **OK**. O Azure procura na conta de armazenamento por ações de ficheiros que podem ser apoiadas. Se adicionou recentemente as suas ações de ficheiro e não as vê na lista, dê algum tempo para que as ações do ficheiro apareçam.

    ![Descobrir ações de ficheiros](./media/backup-afs/discovering-file-shares.png)

1. A partir da lista de **Partilhas** de Ficheiros, selecione uma ou mais das ações de ficheiro que pretende fazer. Selecione **OK**.

1. Depois de escolher as suas partilhas de ficheiros, o menu **'Backup'** muda para a política de **backup**. A partir deste menu, selecione uma política de backup existente ou crie uma nova. Em seguida, selecione **'Ativar 'Backup**' .

    ![Selecionar política de backup](./media/backup-afs/select-backup-policy.png)

Depois de definir uma política de backup, uma foto das ações de arquivo é tirada na hora programada. O ponto de recuperação também é mantido para o período escolhido.

## <a name="create-an-on-demand-backup"></a>Criar uma cópia de segurança a pedido

Ocasionalmente, é melhor gerar um instantâneo de backup, ou ponto de recuperação, fora dos horários programados na política de backup. Um motivo comum para gerar um backup sob demanda é logo depois de configurar a política de backup. Com base no calendário da política de backup, pode levar horas ou dias até que uma foto seja tirada. Para proteger os dados até que a política de cópia de segurança seja aplicada, inicie uma cópia de segurança a pedido. Muitas vezes, é necessário criar um backup sob demanda antes de fazer alterações planejadas em seus compartilhamentos de arquivos.

### <a name="create-a-backup-job-on-demand"></a>Criar um trabalho de reserva a pedido

1. Abra o cofre dos Serviços de Recuperação que usou para fazer o back-up da sua parte de ficheiro. No painel de **visão geral,** selecione **itens de backup** sob a secção de itens **protegidos.**

   ![Selecione itens de backup](./media/backup-afs/backup-items.png)

1. Depois de selecionar **itens de backup,** um novo painel que lista todos os Tipos de **Gestão** de Backup aparece ao lado do painel de **visão geral.**

   ![Lista de tipos de gerenciamento de backup](./media/backup-afs/backup-management-types.png)

1. A partir da lista do Tipo de **Gestão de Backup,** selecione **Armazenamento Azure (Ficheiros Azure)** . Verá uma lista de todas as ações de ficheiros e as correspondentes contas de armazenamento apoiadas usando este cofre.

   ![Itens de backup do armazenamento do Azure (arquivos do Azure)](./media/backup-afs/azure-files-backup-items.png)

1. A partir da lista de ações de ficheiros Azure, selecione a partilha de ficheiros que deseja. Os detalhes do **item de backup** são exibidos. No menu **'Backup Item',** selecione **Backup agora**. Como este trabalho de reserva está a pedido, não há nenhuma política de retenção associada ao ponto de recuperação.

   ![Selecione Backup agora](./media/backup-afs/backup-now.png)

1. O painel **de backup agora** abre. Especifique o último dia em que quer reter o ponto de recuperação. Você pode ter uma retenção máxima de 10 anos para um backup sob demanda.

   ![Escolher data de retenção](./media/backup-afs/retention-date.png)

1. Selecione **OK** para confirmar o trabalho de backup a pedido que corre.

1. Monitore as notificações do portal para manter um controle da conclusão da execução do trabalho de backup. Pode monitorizar o progresso do trabalho no painel do cofre. Selecione **Backup Jobs** > **Em curso**.

## <a name="next-steps"></a>Passos seguintes

Saiba como:
* [Restaurar as ações de ficheiros da Azure](restore-afs.md)
* [Gerir cópias de segurança de partilha de ficheiros Azure](manage-afs-backup.md)
