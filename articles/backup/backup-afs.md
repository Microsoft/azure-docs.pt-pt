---
title: Faça o back up ações de ficheiros Azure no portal Azure
description: Saiba como usar o portal Azure para apoiar as ações de ficheiros da Azure no cofre dos Serviços de Recuperação
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: 76bf8e00dede5f227cb862f9c9474844e349e298
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85391163"
---
# <a name="back-up-azure-file-shares"></a>Fazer cópia de segurança das partilhas de ficheiros do Azure

Este artigo explica como usar o portal Azure para fazer o back up [Azure file shares](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

Neste artigo, aprenderá a:

* Crie um cofre dos Serviços de Recuperação.
* Descubra as ações de ficheiros e configuure backups.
* Faça um trabalho de reserva a pedido para criar um ponto de restauro.

## <a name="prerequisites"></a>Pré-requisitos

* Identifique ou crie um [cofre dos Serviços de Recuperação](#create-a-recovery-services-vault) na mesma região que a conta de armazenamento que acolhe a parte do ficheiro.
* Certifique-se de que a partilha de ficheiros está presente num dos [tipos de conta de armazenamento suportado](azure-file-share-support-matrix.md).

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-file-shares-and-configure-backup"></a>Descubra ações de ficheiros e configuure backup

1. No [portal Azure,](https://portal.azure.com/)abra o cofre dos Serviços de Recuperação que pretende utilizar para configurar a cópia de segurança para a partilha de ficheiros.

1. No painel de **abóbada dos Serviços de Recuperação,** selecione o **+Backup** do menu em cima.

   ![Cofre dos Serviços de Recuperação](./media/backup-afs/recovery-services-vault.png)

    1. No painel **de objetivos de reserva,** desacordo Onde **Azure** está a sua carga de trabalho **a correr?** **Azure**

          ![Escolha Azure como carga de trabalho](./media/backup-afs/backup-goal.png)

    2. Em O que pretende fazer de **Azure File Share** **trás?**

          ![Selecione Azure FileShare](./media/backup-afs/select-azure-file-share.png)

    3. Selecione **Backup** para registar a extensão de partilha de ficheiros Azure no cofre.

          ![Selecione Backup para associar a partilha de ficheiros Azure com cofre](./media/backup-afs/register-extension.png)

1. Depois de selecionar **backup,** o painel **de cópia** de segurança abre-se. Para selecionar a conta de armazenamento que hospeda a partilha de ficheiros que pretende proteger, clique no texto de link **Select** abaixo da caixa de texto **da Conta de Armazenamento.**

   ![Escolha o link Select](./media/backup-afs/choose-select-link.png)

1. O **Painel de Conta de Armazenamento Select** abre à direita, listando um conjunto de contas de armazenamento suportadas descobertas. Ou estão associados a este cofre ou estão presentes na mesma região que o cofre, mas ainda não estão associados a qualquer cofre dos Serviços de Recuperação.

1. A partir da lista de contas de armazenamento descobertas, selecione uma conta e selecione **OK**.

   ![Selecione a partir das contas de armazenamento descobertas](./media/backup-afs/select-discovered-storage-account.png)

1. O próximo passo é selecionar as ações de ficheiro que pretende fazer. Clique no botão **Adicionar** na secção **'Partilhas de Ficheiros' para cópia de segurança.**

   ![Selecione as ações de ficheiro para fazer o back up](./media/backup-afs/select-file-shares-to-back-up.png)

1. O painel de contexto **Select File Shares** abre à direita. O Azure procura na conta de armazenamento ações de ficheiros que podem ser apoiadas. Se recentemente adicionou as suas ações de ficheiros e não as vir na lista, dê algum tempo para que as ações de ficheiro apareçam.

1. Na lista **Select File Shares,** selecione uma ou mais das ações de ficheiro que pretende fazer. Selecione **OK**.

   ![Selecione as ações de ficheiro](./media/backup-afs/select-file-shares.png)

1. Para escolher uma política de backup para a sua partilha de ficheiros, tem três opções:

   * Escolha a política de incumprimento.<br>
   Esta opção permite-lhe ativar a cópia de segurança diária que será mantida durante 30 dias. Se não tiver uma política de backup existente no cofre, o painel de backup abre-se com as definições de política predefinidos. Se quiser escolher as definições predefinitivamente, pode clicar diretamente em **Ativar a cópia de segurança**.

   * Criar uma nova política <br>

      1. Para criar uma nova política de backup para a sua partilha de ficheiros, clique no texto de link abaixo da lista de drop-down na secção **Política de Backup.**<br>

         ![Criar nova política](./media/backup-afs/create-new-policy.png)

      1. O painel de contexto **da Política de Apoio** abre à direita. Especifique um nome de política na caixa de texto e escolha o período de retenção de acordo com o seu requisito. Apenas a opção de retenção diária é ativada por padrão. Se pretender ter retenção semanal, mensal ou anual, selecione a caixa de verificação correspondente e forneça o valor de retenção pretendido.

      1. Depois de especificar os valores de retenção e um nome de política válido, clique em OK.<br>

         ![Dar valor de nome e retenção de políticas](./media/backup-afs/policy-name.png)

   * Escolha uma das políticas de backup existentes <br>

   Para escolher uma das políticas de backup existentes para configurar a proteção, selecione a política desejada da lista de down-down da política de **backup.**<br>

   ![Escolha a política existente](./media/backup-afs/choose-existing-policy.png)

1. Clique **em Ativar a Cópia de Segurança** para começar a proteger a partilha de ficheiros.

   ![Escolha ativar a cópia de segurança](./media/backup-afs/enable-backup.png)

Depois de definir uma política de backup, uma foto das ações de ficheiro é tirada na hora programada. O ponto de recuperação também é mantido para o período escolhido.

>[!NOTE]
>O Azure Backup agora apoia políticas com retenção diária/semanal/mensal/anual para backup de partilha de ficheiros Azure.

## <a name="create-an-on-demand-backup"></a>Criar uma cópia de segurança a pedido

Ocasionalmente, pode querer gerar uma imagem de backup, ou ponto de recuperação, fora dos horários programados na política de backup. Uma razão comum para gerar uma cópia de segurança a pedido é logo após configurar a política de reserva. Com base no horário na política de backup, pode levar horas ou dias até que uma foto seja tirada. Para proteger os dados até que a política de cópia de segurança seja aplicada, inicie uma cópia de segurança a pedido. A criação de uma cópia de segurança a pedido é muitas vezes necessária antes de escruissar as alterações planeadas nas suas ações de ficheiros.

### <a name="create-a-backup-job-on-demand"></a>Criar um trabalho de reserva a pedido

1. Abra o cofre dos Serviços de Recuperação que usou para fazer o back up da sua parte do ficheiro. No painel **de visão** geral, selecione **itens de cópia de segurança** na secção **itens protegidos.**

   ![Selecione itens de backup](./media/backup-afs/backup-items.png)

1. Depois de selecionar **itens de backup,** um novo painel que lista todos os **Tipos de Gestão de Cópias** de Segurança aparece ao lado do **painel de visão** geral.

   ![Lista de tipos de gestão de backup](./media/backup-afs/backup-management-types.png)

1. Na lista **do Tipo de Gestão de Cópias** de Segurança, selecione **Azure Storage (Ficheiros Azure)**. Verá uma lista de todas as ações de ficheiros e as correspondentes contas de armazenamento apoiadas usando este cofre.

   ![Itens de backup do Azure Storage (Azure Files)](./media/backup-afs/azure-files-backup-items.png)

1. A partir da lista de ações de ficheiros Azure, selecione a partilha de ficheiros que pretende. Os detalhes **do item de reserva** aparecem. No menu **'Item de cópia de segurança',** selecione **Backup now**. Como este trabalho de reserva está a pedido, não há nenhuma política de retenção associada ao ponto de recuperação.

   ![Selecione Backup agora](./media/backup-afs/backup-now.png)

1. O **painel de apoio agora** abre. Especifique o último dia em que quer reter o ponto de recuperação. Pode ter uma retenção máxima de 10 anos para um backup a pedido.

   ![Escolha a data de retenção](./media/backup-afs/retention-date.png)

1. Selecione **OK** para confirmar o trabalho de backup a pedido que funciona.

1. Monitorize as notificações do portal para manter um registo da conclusão do trabalho de backup. Pode monitorizar o progresso do trabalho no painel de abóbadas. Selecione **trabalhos de backup**  >  **em curso**.

>[!NOTE]
>O Azure Backup bloqueia a conta de armazenamento quando configura a proteção para qualquer ação de ficheiro na conta correspondente. Isto proporciona proteção contra a supressão acidental de uma conta de armazenamento com ações de ficheiros apoiadas.

## <a name="best-practices"></a>Melhores práticas

* Não elimine as imagens criadas pela Azure Backup. A eliminação de instantâneos pode resultar na perda de pontos de recuperação e/ou falhas de restauro.

* Não remova o cadeado tirado na conta de armazenamento pela Azure Backup. Se eliminar o bloqueio, a sua conta de armazenamento será propensa a eliminação acidental e, se for eliminada, perderá as suas fotos ou cópias de segurança.

## <a name="next-steps"></a>Passos seguintes

Aprenda a:

* [Restaurar ações de ficheiros Azure](restore-afs.md)
* [Gerir backups de partilha de ficheiros Azure](manage-afs-backup.md)
