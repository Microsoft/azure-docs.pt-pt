---
title: Fazer backup de compartilhamentos de arquivos do Azure com o serviço de backup do Azure
description: Neste tutorial, saiba como usar o portal do Azure para configurar um cofre dos serviços de recuperação e fazer backup de compartilhamentos de arquivos do Azure.
author: dcurwin
ms.author: dacurwin
ms.date: 06/10/2019
ms.topic: tutorial
ms.service: backup
manager: carmonm
ms.openlocfilehash: a8b08f87441f9b4c67f718dfe9f0c894d0730a5f
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747044"
---
# <a name="back-up-azure-file-shares-in-the-azure-portal"></a>Fazer backup de compartilhamentos de arquivos do Azure no portal do Azure

Este tutorial explica como usar o portal do Azure para fazer backup de [compartilhamentos de arquivos do Azure](../storage/files/storage-files-introduction.md).

Neste guia, ficará a saber como:
> [!div class="checklist"]
>
> * Configurar um cofre dos Serviços de Recuperação para fazer cópias de segurança de Ficheiros do Azure
> * Executar uma tarefa de cópia de segurança a pedido para criar um ponto de restauro

## <a name="prerequisites"></a>Pré-requisitos

Antes de fazer uma cópia de segurança de uma partilha de ficheiros do Azure, garanta que está presente num dos [tipos de Conta de Armazenamento suportados](tutorial-backup-azure-files.md#limitations-for-azure-file-share-backup-during-preview). Depois de verificar esta situação, pode proteger as suas partilhas de ficheiros.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Limitações para o backup do compartilhamento de arquivos do Azure durante a visualização

A cópia de segurança de partilhas de ficheiros do Azure está em pré-visualização. Há suporte para compartilhamentos de arquivos do Azure em contas de armazenamento de uso geral v1 e de finalidade geral v2. Os seguintes cenários de cópia de segurança não são suportados nas partilhas de ficheiros do Azure:

* Não pode proteger partilhas de ficheiros do Azure em contas de armazenamento que têm Redes Virtuais ou Firewall ativadas.
* Não há uma CLI disponível para proteger os arquivos do Azure usando o backup do Azure.
* O número máximo de cópias de segurança agendadas por dia é de um.
* O número máximo de cópias de segurança a pedido por dia é de quatro.
* Utilize os [bloqueios de recursos](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) da conta de armazenamento para impedir a eliminação acidental de cópias de segurança do seu cofre dos Serviços de Recuperação.
* Não elimine os instantâneos criados pelo Azure Backup. A eliminação de instantâneos pode resultar na perda de pontos de recuperação e/ou falhas de restauro.
* Não exclua compartilhamentos de arquivos protegidos pelo backup do Azure. A solução atual excluirá todos os instantâneos feitos pelo backup do Azure quando o compartilhamento de arquivos for excluído e, portanto, perderá todos os pontos de restauração

O backup de compartilhamentos de arquivos do Azure em contas de armazenamento com replicação de ZRS ( [armazenamento com redundância de zona](../storage/common/storage-redundancy-zrs.md) ) está disponível no momento apenas em EUA Central (cus), leste dos EUA (eus), leste dos EUA 2 (EUS2), Europa setentrional (ne), Sudeste Asiático (Sea), Europa Ocidental (nós) e oeste dos EUA 2 (WUS2).

## <a name="configuring-backup-for-an-azure-file-share"></a>Configurar cópia de segurança de uma partilha de ficheiros do Azure

Este tutorial parte do princípio que já estabeleceu uma partilha de ficheiros do Azure. Para criar cópias de segurança da partilha de ficheiros do Azure:

1. Crie um cofre dos Serviços de Recuperação na mesma região que a partilha de ficheiros. Se já tiver um cofre, abra a página de Descrição geral do cofre e clique em **Cópia de segurança**.

    ![Clique em backup na página de visão geral do seu cofre](./media/backup-file-shares/overview-backup-page.png)

2. No menu **meta de backup** , do **que você deseja fazer backup?** , escolha Azure FileShare.

    ![Escolher a Partilha de Ficheiros do Azure como o Objetivo de cópia de segurança](./media/backup-file-shares/choose-azure-fileshare-from-backup-goal.png)

3. Clique em **Cópia de segurança** para configurar a partilha de ficheiros do Azure para o cofre dos Serviços de Recuperação.

   ![clique em Cópia de segurança para associar a partilha de ficheiros do Azure com o cofre](./media/backup-file-shares/set-backup-goal.png)

    Depois que o cofre estiver associado ao compartilhamento de arquivos do Azure, o menu backup será aberto e solicitará que você selecione uma conta de armazenamento. O menu exibe todas as contas de armazenamento com suporte na região onde o cofre já não está associado a um cofre dos serviços de recuperação.

   ![Selecione sua conta de armazenamento](./media/backup-file-shares/list-of-storage-accounts.png)

4. Na lista de Contas de armazenamento, selecione uma conta e clique em **OK**. O Azure procura na conta de armazenamento partilhas de ficheiros que cuja cópia de segurança possa ser feita. Se adicionou as suas partilhas de ficheiros recentemente e não as vê na lista, aguarde algum tempo para as partilhas de ficheiros aparecerem.

   ![Os compartilhamentos de arquivos estão sendo descobertos](./media/backup-file-shares/discover-file-shares.png)

5. Na lista **compartilhamentos de arquivos** , selecione um ou mais dos compartilhamentos de arquivos que você deseja fazer backup e clique em **OK**.

6. Depois de escolher as Partilhas de Ficheiros, o menu de Cópia de segurança muda para a **Política de cópia de segurança**. Neste menu, selecione uma política de cópia de segurança existente ou crie uma nova e, em seguida, clique em **Ativar a Cópia de Segurança**.

   ![Selecione uma política de backup ou crie uma nova](./media/backup-file-shares/apply-backup-policy.png)

    Depois de estabelecer uma política de cópia de segurança, será captado um instantâneo das Partilhas de Ficheiros à hora agendada e o ponto de recuperação é retido para o período de escolhido.

## <a name="create-an-on-demand-backup"></a>Criar uma cópia de segurança a pedido

Depois de configurar a política de backup, você desejará criar um backup sob demanda para garantir que seus dados sejam protegidos até o próximo backup agendado.

### <a name="to-create-an-on-demand-backup"></a>Para criar uma cópia de segurança a pedido

1. Abra o cofre dos Serviços de Recuperação que contém os pontos de recuperação das partilhas de ficheiros e clique em **Itens de Cópia de Segurança**. É apresentada a lista de tipos de Itens de Cópia de Segurança.

   ![Lista de itens de backup](./media/backup-file-shares/list-of-backup-items.png)

2. Na lista, selecione **Armazenamento do Azure (Ficheiros do Azure)** . É apresentada a lista de partilhas de ficheiros do Azure.

   ![Lista de compartilhamentos de arquivos do Azure](./media/backup-file-shares/list-of-azure-files-backup-items.png)

3. Na lista de partilhas de ficheiros do Azure, selecione a partilha de ficheiros que quer. Abre-se o menu de Item de Cópia de Segurança para a partilha de ficheiros selecionada.

   ![Menu de item de backup para o compartilhamento de arquivos selecionado](./media/backup-file-shares/backup-item-menu.png)

4. No menu de Item de Cópia de Segurança, clique em **Fazer Cópia de Segurança Agora**. Por se tratar de uma tarefa de cópia de segurança a pedido, não há nenhuma política de retenção associada ao ponto de recuperação. Abre-se a caixa de diálogo **Fazer Cópia de Segurança Agora**. Especifique o último dia em que quer reter o ponto de recuperação.

   ![Escolher a data para retenção do ponto de recuperação](./media/backup-file-shares/backup-now-menu.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, utilizou o portal do Azure para:

> [!div class="checklist"]
>
> * Configurar um cofre dos Serviços de Recuperação para fazer cópias de segurança de Ficheiros do Azure
> * Executar uma tarefa de cópia de segurança a pedido para criar um ponto de restauro

Continue no próximo artigo para restaurar a partir de um backup de um compartilhamento de arquivos do Azure.

> [!div class="nextstepaction"]
> [Restaurar do backup de compartilhamentos de arquivos do Azure](./backup-azure-files.md#restore-from-backup-of-azure-file-share)
