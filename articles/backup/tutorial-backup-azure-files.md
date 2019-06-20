---
title: Fazer cópias de segurança de partilhas de ficheiros de ficheiros do Azure com o serviço de cópia de segurança do Azure
description: Este tutorial explica como fazer cópias de segurança de partilhas de ficheiros do Azure.
services: backup
author: dcurwin
ms.author: dacurwin
ms.date: 06/10/2019
ms.topic: tutorial
ms.service: backup
manager: carmonm
ms.openlocfilehash: 474d5454e30c35d3f3ccf4ea994093ef47bd6ceb
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275993"
---
# <a name="back-up-azure-file-shares"></a>Fazer cópia de segurança das partilhas de ficheiros do Azure
Este artigo explica como utilizar o Portal do Azure para fazer a cópia de segurança e restauro das [partilhas de ficheiros do Azure](../storage/files/storage-files-introduction.md).

Neste guia, ficará a saber como:
> [!div class="checklist"]
> * Configurar um cofre dos Serviços de Recuperação para fazer cópias de segurança de Ficheiros do Azure
> * Executar uma tarefa de cópia de segurança a pedido para criar um ponto de restauro


## <a name="prerequisites"></a>Pré-requisitos
Antes de fazer uma cópia de segurança de uma partilha de ficheiros do Azure, garanta que está presente num dos [tipos de Conta de Armazenamento suportados](tutorial-backup-azure-files.md#limitations-for-azure-file-share-backup-during-preview). Depois de verificar esta situação, pode proteger as suas partilhas de ficheiros.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Limitações da cópia de segurança de partilha de ficheiros do Azure durante a pré-visualização
A cópia de segurança de partilhas de ficheiros do Azure está em pré-visualização. Partilham de ficheiros do Azure em ambos os para fins gerais v1 e contas de armazenamento para fins gerais v2 são suportadas. Os seguintes cenários de cópia de segurança não são suportados nas partilhas de ficheiros do Azure:
- Não pode proteger partilhas de ficheiros do Azure em contas de armazenamento que têm Redes Virtuais ou Firewall ativadas.
- Não existe nenhum CLI disponível para proteger ficheiros do Azure utilizando o Azure Backup.
- O número máximo de cópias de segurança agendadas por dia é de um.
- O número máximo de cópias de segurança a pedido por dia é de quatro.
- Utilize os [bloqueios de recursos](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) da conta de armazenamento para impedir a eliminação acidental de cópias de segurança do seu cofre dos Serviços de Recuperação.
- Não elimine os instantâneos criados pelo Azure Backup. A eliminação de instantâneos pode resultar na perda de pontos de recuperação e/ou falhas de restauro.
- Não elimine as partilhas de ficheiros que estão protegidas pelo Azure Backup. A solução atual irá eliminar todos os instantâneos tirados pelo Azure Backup, assim que a partilha de ficheiros é eliminada e, por conseguinte, perder todos os pontos de restauro

Voltar para partilhas de ficheiros do Azure em contas de armazenamento com [armazenamento com redundância de zona](../storage/common/storage-redundancy-zrs.md) replicação (ZRS) está atualmente disponível apenas na Central US (CUS), Leste E.u.a. (EUS), este dos E.U.A. 2 (EUS2), Norte da Europa (m), Sudeste Asiático (SEA), Europa Ocidental (WE) e E.U.A. oeste 2 (WUS2).

## <a name="configuring-backup-for-an-azure-file-share"></a>Configurar cópia de segurança de uma partilha de ficheiros do Azure
Este tutorial parte do princípio que já estabeleceu uma partilha de ficheiros do Azure. Para criar cópias de segurança da partilha de ficheiros do Azure:

1. Crie um cofre dos Serviços de Recuperação na mesma região que a partilha de ficheiros. Se já tiver um cofre, abra a página de Descrição geral do cofre e clique em **Cópia de segurança**.

    ![Clique em cópia de segurança na página de descrição geral do seu Cofre](./media/backup-file-shares/overview-backup-page.png)

2. Na **objetivo de cópia de segurança** menu, do **o que pretende criar cópias de segurança?** , escolha a partilha de ficheiros do Azure.

    ![Escolher a Partilha de Ficheiros do Azure como o Objetivo de cópia de segurança](./media/backup-file-shares/choose-azure-fileshare-from-backup-goal.png)

3. Clique em **Cópia de segurança** para configurar a partilha de ficheiros do Azure para o cofre dos Serviços de Recuperação.

   ![clique em Cópia de segurança para associar a partilha de ficheiros do Azure com o cofre](./media/backup-file-shares/set-backup-goal.png)

    Assim que o Cofre estiver associado a partilha de ficheiros do Azure, o menu de cópia de segurança abre-se e pede-lhe para selecionar uma conta de armazenamento. O menu apresenta todas as contas de armazenamento suportadas na região onde existe o cofre que ainda não está associado a um cofre dos serviços de recuperação.

   ![Selecione a sua conta de armazenamento](./media/backup-file-shares/list-of-storage-accounts.png)

4. Na lista de Contas de armazenamento, selecione uma conta e clique em **OK**. O Azure procura na conta de armazenamento partilhas de ficheiros que cuja cópia de segurança possa ser feita. Se adicionou as suas partilhas de ficheiros recentemente e não as vê na lista, aguarde algum tempo para as partilhas de ficheiros aparecerem.

   ![Partilhas de ficheiros estão a ser detetadas](./media/backup-file-shares/discover-file-shares.png)

5. Partir do **partilhas de ficheiros** , selecione um ou mais das partilhas de ficheiros que pretende criar cópias de segurança e clique em **OK**.

6. Depois de escolher as Partilhas de Ficheiros, o menu de Cópia de segurança muda para a **Política de cópia de segurança**. Neste menu, selecione uma política de cópia de segurança existente ou crie uma nova e, em seguida, clique em **Ativar a Cópia de Segurança**.

   ![Selecione uma política de cópia de segurança ou crie um novo](./media/backup-file-shares/apply-backup-policy.png)

    Depois de estabelecer uma política de cópia de segurança, será captado um instantâneo das Partilhas de Ficheiros à hora agendada e o ponto de recuperação é retido para o período de escolhido.

## <a name="create-an-on-demand-backup"></a>Criar uma cópia de segurança a pedido
Depois de configurar a política de cópia de segurança, desejará criar uma cópia de segurança a pedido para garantir que seus dados estão protegidos até o próximo backup agendado.


### <a name="to-create-an-on-demand-backup"></a>Para criar uma cópia de segurança a pedido

1. Abra o cofre dos Serviços de Recuperação que contém os pontos de recuperação das partilhas de ficheiros e clique em **Itens de Cópia de Segurança**. É apresentada a lista de tipos de Itens de Cópia de Segurança.

   ![Lista de itens de cópia de segurança](./media/backup-file-shares/list-of-backup-items.png)

2. Na lista, selecione **Armazenamento do Azure (Ficheiros do Azure)** . É apresentada a lista de partilhas de ficheiros do Azure.

   ![Lista de partilhas de ficheiros do Azure](./media/backup-file-shares/list-of-azure-files-backup-items.png)

3. Na lista de partilhas de ficheiros do Azure, selecione a partilha de ficheiros que quer. Abre-se o menu de Item de Cópia de Segurança para a partilha de ficheiros selecionada.

   ![Partilhar o menu de Item de cópia de segurança para o ficheiro selecionado](./media/backup-file-shares/backup-item-menu.png)

4. No menu de Item de Cópia de Segurança, clique em **Fazer Cópia de Segurança Agora**. Por se tratar de uma tarefa de cópia de segurança a pedido, não há nenhuma política de retenção associada ao ponto de recuperação. Abre-se a caixa de diálogo **Fazer Cópia de Segurança Agora**. Especifique o último dia em que quer reter o ponto de recuperação.

   ![Escolha a data de retenção do ponto de recuperação](./media/backup-file-shares/backup-now-menu.png)


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, utilizou o portal do Azure para:

> [!div class="checklist"]
> * Configurar um cofre dos Serviços de Recuperação para fazer cópias de segurança de Ficheiros do Azure
> * Executar uma tarefa de cópia de segurança a pedido para criar um ponto de restauro

Avance para o artigo seguinte para restaurar a partir de uma cópia de segurança de uma partilha de ficheiros do Azure.

> [!div class="nextstepaction"]
> [Restaurar a partir de cópia de segurança de partilhas de ficheiros do Azure](./backup-azure-files.md#restore-from-backup-of-azure-file-share)
 
