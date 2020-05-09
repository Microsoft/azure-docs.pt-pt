---
title: Back up Ações de arquivo Azure no portal Azure
description: Saiba como usar o portal Azure para apoiar as ações de ficheiros azure no cofre dos Serviços de Recuperação
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: a77f7fd0ec21eae60a7313a9ffa889fbef4372c6
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82978033"
---
# <a name="back-up-azure-file-shares-in-a-recovery-services-vault"></a>Back up Ações de arquivo Azure em cofre de Serviços de Recuperação

Este artigo explica como usar o portal Azure para fazer o backup de ações de [ficheiros Azure.](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)

Neste artigo, aprenderá a:

* Crie um cofre dos Serviços de Recuperação.
* Descubra as partilhas de ficheiros e configure cópias de segurança.
* Faça um trabalho de reserva a pedido para criar um ponto de restauro.

## <a name="prerequisites"></a>Pré-requisitos

* Identifique ou crie um cofre de Serviços de [Recuperação](#create-a-recovery-services-vault) na mesma região que a conta de armazenamento que acolhe a partilha de ficheiros.
* Certifique-se de que a parte do ficheiro está presente num dos tipos de conta de [armazenamento suportado](azure-file-share-support-matrix.md).

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Modificar a replicação do armazenamento

Por padrão, os cofres utilizam [armazenamento geo-redundante (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Se o cofre for o seu principal mecanismo de reserva, recomendamos que utilize GRS.
* Pode utilizar [o armazenamento redundante localmente (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) como uma opção de baixo custo.

Para modificar o tipo de replicação de armazenamento:

1. No novo cofre, selecione **Propriedades** sob a secção **Definições.**

1. Na página **Propriedades,** sob **configuração de backup,** selecione **Atualização**.

1. Selecione o tipo de replicação de armazenamento e selecione **Guardar**.

    ![Configuração de backup de atualização](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> Não pode modificar o tipo de replicação de armazenamento depois de o cofre ser configurado e conter itens de reserva. Se queres fazer isto, tens de recriar o cofre.
>

## <a name="discover-file-shares-and-configure-backup"></a>Descubra as partilhas de ficheiros e configure a cópia de segurança

1. No [portal Azure,](https://portal.azure.com/)abra o cofre dos Serviços de Recuperação que pretende utilizar para configurar a cópia de segurança para a partilha de ficheiros.

1. No painel de **abóbadas** dos Serviços de Recuperação, selecione o **+Backup** do menu em cima.

   ![Cofre dos Serviços de Recuperação](./media/backup-afs/recovery-services-vault.png)

    1. No painel **'Backup Goal',** definido Onde está a sua **Azure** carga de **trabalho em funcionamento?** **Azure**

          ![Escolha Azure como carga de trabalho](./media/backup-afs/backup-goal.png)

    2. Em O que quer fazer de **Azure File Share** **reserva?**

          ![Selecione FileShare Azure](./media/backup-afs/select-azure-file-share.png)

    3. Selecione **Backup** para registar a extensão de partilha de ficheiros Azure no cofre.

          ![Selecione Backup para associar a partilha de ficheiros Azure com cofre](./media/backup-afs/register-extension.png)

1. Depois de selecionar **Backup,** abre-se o painel **de backup.** Para selecionar a conta de armazenamento que hospeda a partilha de ficheiros que pretende proteger, clique no texto de link **Select** abaixo da caixa de texto da Conta de **Armazenamento.**

   ![Escolha o link Select](./media/backup-afs/choose-select-link.png)

1. O Painel de Conta de **Armazenamento Select** abre à direita, listando um conjunto de contas de armazenamento suportadas descobertas. Ou estão associados a este cofre ou presentes na mesma região que o cofre, mas ainda não estão associados a qualquer cofre dos Serviços de Recuperação.

1. A partir da lista de contas de armazenamento descobertas, selecione uma conta e selecione **OK**.

   ![Selecione a partir das contas de armazenamento descobertas](./media/backup-afs/select-discovered-storage-account.png)

1. O próximo passo é selecionar as ações de ficheiro que pretende fazer. Clique no botão **Adicionar** na secção **FileShares para Backup.**

   ![Selecione as ações de ficheiro para fazer o back-up](./media/backup-afs/select-file-shares-to-back-up.png)

1. O painel de contexto **Select File Shares** abre à direita. O Azure procura na conta de armazenamento por ações de ficheiros que podem ser apoiadas. Se adicionou recentemente as suas ações de ficheiro e não as vê na lista, dê algum tempo para que as ações do ficheiro apareçam.

1. A partir da lista de Partilhas de **Ficheiros Selecionados,** selecione uma ou mais das ações de ficheiro que pretende fazer. Selecione **OK**.

   ![Selecione as ações de ficheiro](./media/backup-afs/select-file-shares.png)

1. Para escolher uma política de backup para a sua partilha de ficheiros, tem três opções:

   * Escolha a política de incumprimento.<br>
   Esta opção permite-lhe ativar o backup diário que será retido durante 30 dias. Se não tiver uma política de backup existente no cofre, o painel de reserva abre-se com as definições de política padrão. Se pretender escolher as definições predefinidas, pode clicar diretamente na cópia de **segurança .**

   * Criar uma nova política <br>

      1. Para criar uma nova política de backup para a sua partilha de ficheiros, clique no texto de link abaixo da lista de drop-down na secção Política de **Backup.**<br>

         ![Criar nova política](./media/backup-afs/create-new-policy.png)

      1. O painel de contexto **da Política de Backup** abre à direita. Especifique um nome de política na caixa de texto e escolha o período de retenção de acordo com o seu requisito. Apenas a opção de retenção diária é ativada por padrão. Se quiser ter retenção semanal, mensal ou anual, selecione a caixa de verificação correspondente e forneça o valor de retenção desejado.

      1. Depois de especificar os valores de retenção e um nome de política válido, clique em OK.<br>

         ![Dar nome político e valores de retenção](./media/backup-afs/policy-name.png)

   * Escolha uma das políticas de backup existentes <br>

   Para escolher uma das políticas de backup existentes para configurar a proteção, selecione a política desejada a partir da lista de abandono da política de **backup.**<br>

   ![Escolha a política existente](./media/backup-afs/choose-existing-policy.png)

1. Clique em **Ativar a Cópia de Segurança** para começar a proteger a parte do ficheiro.

   ![Escolha ativar a cópia de segurança](./media/backup-afs/enable-backup.png)

Depois de definir uma política de backup, uma foto das ações de arquivo é tirada na hora programada. O ponto de recuperação também é mantido para o período escolhido.

>[!NOTE]
>A Azure Backup apoia agora as políticas com retenção diária/semanal/mensal/anual para cópia de segurança de partilha de ficheiros Azure.

## <a name="create-an-on-demand-backup"></a>Criar uma cópia de segurança a pedido

Ocasionalmente, é melhor gerar um instantâneo de backup, ou ponto de recuperação, fora dos horários programados na política de backup. Uma razão comum para gerar um backup a pedido é logo após ter configurado a política de backup. Com base no calendário da política de backup, pode levar horas ou dias até que uma foto seja tirada. Para proteger os dados até que a política de cópia de segurança seja aplicada, inicie uma cópia de segurança a pedido. A criação de um backup a pedido é muitas vezes necessária antes de fazer alterações planeadas nas suas ações de ficheiro.

### <a name="create-a-backup-job-on-demand"></a>Criar um trabalho de reserva a pedido

1. Abra o cofre dos Serviços de Recuperação que usou para fazer o back-up da sua parte de ficheiro. No painel de **visão geral,** selecione **itens de backup** sob a secção de itens **protegidos.**

   ![Selecione itens de backup](./media/backup-afs/backup-items.png)

1. Depois de selecionar **itens de backup,** um novo painel que lista todos os Tipos de **Gestão** de Backup aparece ao lado do painel de **visão geral.**

   ![Lista de Tipos de Gestão de Backup](./media/backup-afs/backup-management-types.png)

1. A partir da lista do Tipo de **Gestão de Backup,** selecione **Armazenamento Azure (Ficheiros Azure)**. Verá uma lista de todas as ações de ficheiros e as correspondentes contas de armazenamento apoiadas usando este cofre.

   ![Itens de backup de armazenamento azure (Ficheiros Azure)](./media/backup-afs/azure-files-backup-items.png)

1. A partir da lista de ações de ficheiros Azure, selecione a partilha de ficheiros que deseja. Os detalhes **do item de reserva** aparecem. No menu **'Backup Item',** selecione **Backup agora**. Como este trabalho de reserva está a pedido, não há nenhuma política de retenção associada ao ponto de recuperação.

   ![Selecione Backup agora](./media/backup-afs/backup-now.png)

1. O painel **de backup agora** abre. Especifique o último dia em que quer reter o ponto de recuperação. Pode ter uma retenção máxima de 10 anos para um reforço a pedido.

   ![Escolha a data de retenção](./media/backup-afs/retention-date.png)

1. Selecione **OK** para confirmar o trabalho de backup a pedido que corre.

1. Monitorize as notificações do portal para manter um registo da conclusão do trabalho de backup. Pode monitorizar o progresso do trabalho no painel do cofre. Selecione **Backup Jobs** > **Em andamento**.

>[!NOTE]
>A Azure Backup bloqueia a conta de armazenamento quando configura a proteção para qualquer parte de ficheiro na conta correspondente. Isto fornece proteção contra a eliminação acidental de uma conta de armazenamento com ações de ficheiros apoiadas.

## <a name="best-practices"></a>Melhores práticas

* Não apague as imagens criadas pela Azure Backup. A eliminação de instantâneos pode resultar na perda de pontos de recuperação e/ou falhas de restauro.

* Não retire o cadeado da conta de armazenamento pela Azure Backup. Se apagar o bloqueio, a sua conta de armazenamento será propensa a uma eliminação acidental e, se for eliminada, perderá as suas fotos ou cópias de segurança.

## <a name="next-steps"></a>Passos seguintes

Aprenda a:

* [Restaurar as ações de ficheiros da Azure](restore-afs.md)
* [Gerir cópias de segurança de partilha de ficheiros Azure](manage-afs-backup.md)
