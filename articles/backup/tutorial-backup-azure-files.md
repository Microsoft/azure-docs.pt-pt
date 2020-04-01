---
title: Tutorial - Back up Azure Files file shares
description: Neste tutorial, aprenda a usar o portal Azure para configurar um cofre de Serviços de Recuperação e fazer o back up de ações de ficheiros Azure.
ms.date: 06/10/2019
ms.topic: tutorial
ms.openlocfilehash: ec9074a39f2ece7878c0c3ef828dc21748d0ab89
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "76293934"
---
# <a name="back-up-azure-file-shares-in-the-azure-portal"></a>Back up Ações de arquivo Azure no portal Azure

Este tutorial explica como usar o portal Azure para fazer o backup de ações de [ficheiros Azure.](../storage/files/storage-files-introduction.md)

Neste guia, ficará a saber como:
> [!div class="checklist"]
>
> * Configurar um cofre dos Serviços de Recuperação para fazer cópias de segurança de Ficheiros do Azure
> * Executar uma tarefa de cópia de segurança a pedido para criar um ponto de restauro

## <a name="prerequisites"></a>Pré-requisitos

Antes de fazer uma cópia de segurança de uma partilha de ficheiros do Azure, garanta que está presente num dos [tipos de Conta de Armazenamento suportados](tutorial-backup-azure-files.md#limitations-for-azure-file-share-backup-during-preview). Depois de verificar esta situação, pode proteger as suas partilhas de ficheiros.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Limitações da cópia de segurança da partilha de ficheiros do Azure durante a pré-visualização

A cópia de segurança de partilhas de ficheiros do Azure está em pré-visualização. As ações de ficheiros Azure em contas de armazenamento v1 de uso geral e de uso geral v2 são suportadas. Os seguintes cenários de cópia de segurança não são suportados nas partilhas de ficheiros do Azure:

* Não existe clI disponível para proteger ficheiros Azure utilizando o Backup Azure.
* O número máximo de cópias de segurança agendadas por dia é de um.
* O número máximo de cópias de segurança a pedido por dia é de quatro.
* Utilize [bloqueios](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) de recursos na conta de armazenamento para evitar a eliminação acidental de cópias de segurança no cofre dos Serviços de Recuperação.
* Não elimine os instantâneos criados pelo Azure Backup. A eliminação de instantâneos pode resultar na perda de pontos de recuperação e/ou falhas de restauro.
* Não elimine as partilhas de ficheiros protegidas pela Azure Backup. A solução atual eliminará todos os instantâneos tirados pela Azure Backup assim que a parte do ficheiro for eliminada e, portanto, perdertodos os pontos de restauro

O back up para As ações de ficheiros Azure em Contas de Armazenamento com réplica de [armazenamento redundante](../storage/common/storage-redundancy-zrs.md) de zona (ZRS) está atualmente disponível apenas nos EUA Central (CUS), Leste dos EUA (EUS2), Leste dos EUA (EUS2), Norte da Europa (NE), Ásia do Sudeste Asiático (SEA), Europa Ocidental (WE) e Oeste DOS 2 (WUS2).

## <a name="configuring-backup-for-an-azure-file-share"></a>Configurar cópia de segurança de uma partilha de ficheiros do Azure

Este tutorial parte do princípio que já estabeleceu uma partilha de ficheiros do Azure. Para criar cópias de segurança da partilha de ficheiros do Azure:

1. Crie um cofre dos Serviços de Recuperação na mesma região que a partilha de ficheiros. Se já tiver um cofre, abra a página de Descrição geral do cofre e clique em **Cópia de segurança**.

    ![Clique em Backup na página de visão geral do seu cofre](./media/tutorial-backup-azure-files/overview-backup-page.png)

2. No menu **Backup Goal,** a partir do **que pretende fazer backup?**

    ![Escolher a Partilha de Ficheiros do Azure como o Objetivo de cópia de segurança](./media/tutorial-backup-azure-files/choose-azure-fileshare-from-backup-goal.png)

3. Clique em **Cópia de segurança** para configurar a partilha de ficheiros do Azure para o cofre dos Serviços de Recuperação.

   ![clique em Cópia de segurança para associar a partilha de ficheiros do Azure com o cofre](./media/tutorial-backup-azure-files/set-backup-goal.png)

    Uma vez que o cofre esteja associado à partilha de ficheiros Azure, o menu 'Backup' abre e pede-lhe para selecionar uma conta de Armazenamento. O menu exibe todas as Contas de Armazenamento suportadas na região onde existe o seu cofre que ainda não estão associados a um cofre dos Serviços de Recuperação.

   ![Selecione a sua conta de armazenamento](./media/tutorial-backup-azure-files/list-of-storage-accounts.png)

4. Na lista de Contas de armazenamento, selecione uma conta e clique em **OK**. O Azure procura na conta de armazenamento partilhas de ficheiros que cuja cópia de segurança possa ser feita. Se adicionou as suas partilhas de ficheiros recentemente e não as vê na lista, aguarde algum tempo para as partilhas de ficheiros aparecerem.

   ![As ações de ficheiros estão a ser descobertas](./media/tutorial-backup-azure-files/discover-file-shares.png)

5. A partir da lista de **Partilhas** de Ficheiros, selecione uma ou mais das partilhas de ficheiros que pretende fazer recuar e clique em **OK**.

6. Depois de escolher as Partilhas de Ficheiros, o menu de Cópia de segurança muda para a **Política de cópia de segurança**. Neste menu, selecione uma política de cópia de segurança existente ou crie uma nova e, em seguida, clique em **Ativar a Cópia de Segurança**.

   ![Selecione uma política de backup ou crie uma nova](./media/tutorial-backup-azure-files/apply-backup-policy.png)

    Depois de estabelecer uma política de cópia de segurança, será captado um instantâneo das Partilhas de Ficheiros à hora agendada e o ponto de recuperação é retido para o período de escolhido.

## <a name="create-an-on-demand-backup"></a>Criar uma cópia de segurança a pedido

Depois de configurar a política de backup, irá querer criar uma cópia de segurança a pedido para garantir que os seus dados estão protegidos até à próxima cópia de segurança agendada.

### <a name="to-create-an-on-demand-backup"></a>Para criar uma cópia de segurança a pedido

1. Abra o cofre dos Serviços de Recuperação que contém os pontos de recuperação das partilhas de ficheiros e clique em **Itens de Cópia de Segurança**. É apresentada a lista de tipos de Itens de Cópia de Segurança.

   ![Lista de itens de backup](./media/tutorial-backup-azure-files/list-of-backup-items.png)

2. Na lista, selecione **Armazenamento do Azure (Ficheiros do Azure)**. É apresentada a lista de partilhas de ficheiros do Azure.

   ![Lista de ações de ficheiros da Azure](./media/tutorial-backup-azure-files/list-of-azure-files-backup-items.png)

3. Na lista de partilhas de ficheiros do Azure, selecione a partilha de ficheiros que quer. Abre-se o menu de Item de Cópia de Segurança para a partilha de ficheiros selecionada.

   ![Menu de 'Backup Item' para a partilha de ficheiros selecionado](./media/tutorial-backup-azure-files/backup-item-menu.png)

4. No menu de Item de Cópia de Segurança, clique em **Fazer Cópia de Segurança Agora**. Por se tratar de uma tarefa de cópia de segurança a pedido, não há nenhuma política de retenção associada ao ponto de recuperação. Abre-se a caixa de diálogo **Fazer Cópia de Segurança Agora**. Especifique o último dia em que quer reter o ponto de recuperação.

   ![Escolha a data para a retenção do ponto de recuperação](./media/tutorial-backup-azure-files/backup-now-menu.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, utilizou o portal do Azure para:

> [!div class="checklist"]
>
> * Configurar um cofre dos Serviços de Recuperação para fazer cópias de segurança de Ficheiros do Azure
> * Executar uma tarefa de cópia de segurança a pedido para criar um ponto de restauro

Continue para o próximo artigo para restaurar a partir de uma cópia de segurança de uma partilha de ficheiros Azure.

> [!div class="nextstepaction"]
> [Restaurar a partir de backup de ações de ficheiros Azure](restore-afs.md)
