---
title: Faça o back up ações de ficheiros Azure no portal Azure
description: Saiba como usar o portal Azure para apoiar as ações de ficheiros da Azure no cofre dos Serviços de Recuperação
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: ca49f1ad48ab0534b27b91ad6a5a50b393cda782
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88890353"
---
# <a name="back-up-azure-file-shares"></a>Fazer cópia de segurança das partilhas de ficheiros do Azure

Este artigo explica como fazer o back up [Azure de partilhas](../storage/files/storage-files-introduction.md) de ficheiros do portal Azure.

Neste artigo, vai aprender a:

* Crie um cofre dos Serviços de Recuperação.
* Configure o backup do cofre dos Serviços de Recuperação
* Configure o backup do painel de partilha de ficheiros
* Executar uma tarefa de cópia de segurança a pedido para criar um ponto de restauro

## <a name="prerequisites"></a>Pré-requisitos

* [Saiba mais](azure-file-share-backup-overview.md) sobre a solução de backup baseada em imagens de partilha de ficheiros Azure.
* Certifique-se de que a partilha de ficheiros está presente num dos [tipos de conta de armazenamento suportado](azure-file-share-support-matrix.md).
* Identifique ou crie um [cofre dos Serviços de Recuperação](#create-a-recovery-services-vault) na mesma região que a conta de armazenamento que acolhe a parte do ficheiro.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="configure-backup-from-the-recovery-services-vault"></a>Configure o backup do cofre dos Serviços de Recuperação

Os seguintes passos explicam como pode configurar a cópia de segurança para várias partilhas de ficheiros a partir do painel de abóbada dos Serviços de Recuperação:

1. No [portal Azure,](https://portal.azure.com/)abra o cofre dos Serviços de Recuperação que pretende utilizar para configurar a cópia de segurança para a partilha de ficheiros.

1. No painel de **abóbada dos Serviços de Recuperação,** selecione o **+Backup** do menu em cima.

   ![Cofre dos Serviços de Recuperação](./media/backup-afs/recovery-services-vault.png)

    1. No painel **de objetivos de reserva,** desacordo Onde  está a sua carga de trabalho **a correr?** 

          ![Escolha Azure como carga de trabalho](./media/backup-afs/backup-goal.png)

    2. Em O que pretende fazer de  **trás?**

          ![Selecione Azure FileShare](./media/backup-afs/select-azure-file-share.png)

    3. Selecione **Backup** para registar a extensão de partilha de ficheiros Azure no cofre.

          ![Selecione Backup para associar a partilha de ficheiros Azure com cofre](./media/backup-afs/register-extension.png)

1. Depois de selecionar **backup,** o painel **de cópia** de segurança abre-se. Para selecionar a conta de armazenamento que hospeda a partilha de ficheiros que pretende proteger, **selecione** o texto de link Select abaixo da caixa de texto **da Conta de Armazenamento.**

   ![Escolha o link Select](./media/backup-afs/choose-select-link.png)

1. O **Painel de Conta de Armazenamento Select** abre à direita, listando um conjunto de contas de armazenamento suportadas descobertas. Ou estão associados a este cofre ou estão presentes na mesma região que o cofre, mas ainda não estão associados a qualquer cofre dos Serviços de Recuperação.

1. A partir da lista de contas de armazenamento descobertas, selecione uma conta e selecione **OK**.

   ![Selecione a partir das contas de armazenamento descobertas](./media/backup-afs/select-discovered-storage-account.png)

1. O próximo passo é selecionar as ações de ficheiro que pretende fazer. Selecione o botão **Adicionar** na secção **'Partilhas de Ficheiros' para cópia de segurança.**

   ![Selecione as ações de ficheiro para fazer o back up](./media/backup-afs/select-file-shares-to-back-up.png)

1. O painel de contexto **Select File Shares** abre à direita. O Azure procura na conta de armazenamento ações de ficheiros que podem ser apoiadas. Se recentemente adicionou as suas ações de ficheiros e não as vir na lista, dê algum tempo para que as ações de ficheiro apareçam.

1. Na lista **Select File Shares,** selecione uma ou mais das ações de ficheiro que pretende fazer. Selecione **OK**.

   ![Selecione as ações de ficheiro](./media/backup-afs/select-file-shares.png)

1. Para escolher uma política de backup para a sua partilha de ficheiros, tem três opções:

   * Escolha a política de incumprimento.<br>
   Esta opção permite-lhe ativar a cópia de segurança diária que será mantida durante 30 dias. Se não tiver uma política de backup existente no cofre, o painel de backup abre-se com as definições de política predefinidos. Se quiser escolher as definições predefinitivas, pode selecionar diretamente **ativar a cópia de segurança**.

   * Criar uma nova política <br>

      1. Para criar uma nova política de backup para a sua partilha de ficheiros, selecione o texto de link abaixo da lista de drop-down na secção **Política de Backup.**<br>

         ![Criar nova política](./media/backup-afs/create-new-policy.png)

      1. O painel de contexto **da Política de Apoio** abre à direita. Especifique um nome de política na caixa de texto e escolha o período de retenção de acordo com o seu requisito. Apenas a opção de retenção diária é ativada por padrão. Se pretender ter retenção semanal, mensal ou anual, selecione a caixa de verificação correspondente e forneça o valor de retenção pretendido.

      1. Depois de especificar os valores de retenção e um nome de política válido, selecione **OK**.<br>

         ![Dar valor de nome e retenção de políticas](./media/backup-afs/policy-name.png)

   * Escolha uma das políticas de backup existentes <br>

      Para escolher uma das políticas de backup existentes para configurar a proteção, selecione a política desejada da lista de down-down da política de **backup.**<br>

      ![Escolha a política existente](./media/backup-afs/choose-existing-policy.png)

1. Selecione **Ative Backup** para começar a proteger a partilha de ficheiros.

   ![Escolha ativar a cópia de segurança](./media/backup-afs/enable-backup.png)

Depois de definir uma política de backup, uma foto das ações de ficheiro é tirada na hora programada. O ponto de recuperação também é mantido para o período escolhido.

>[!NOTE]
>O Azure Backup agora apoia políticas com retenção diária/semanal/mensal/anual para backup de partilha de ficheiros Azure.

## <a name="configure-backup-from-the-file-share-pane"></a>Configure o backup do painel de partilha de ficheiros

Os seguintes passos explicam como pode configurar a cópia de segurança para as ações individuais de ficheiros a partir do painel de partilha de ficheiros respetivo:

1. No [portal Azure,](https://portal.azure.com/)abra a conta de armazenamento que hospeda a parte de ficheiro que pretende fazer.

1. Uma vez na conta de armazenamento, selecione as **ações de ficheiro** com rótulo de azulejo . Também pode navegar para **Arquivar ações** através da tabela de conteúdos para a conta de armazenamento.

   ![Conta de armazenamento](./media/backup-afs/storage-account.png)

1. Na listagem de ações de ficheiro, deverá ver todas as ações de ficheiro presentes na conta de armazenamento. Selecione a partilha de ficheiros que pretende fazer.

   ![Lista de ações de ficheiros](./media/backup-afs/file-shares-list.png)

1. Selecione **Backup** na secção **operações** do painel de partilha de ficheiros. O **painel de apoio Configure** carregará à direita.

   ![Painel de apoio de configuração](./media/backup-afs/configure-backup.png)

1. Para a seleção do cofre dos Serviços de Recuperação, faça um dos seguintes:

    * Se já tiver um cofre, selecione o botão de rádio do cofre select existente dos Serviços **de** Recuperação e escolha um dos cofres existentes do menu De entrega do **Nome do Cofre.**

       ![Selecione o cofre existente](./media/backup-afs/select-existing-vault.png)

    * Se não tiver um cofre, selecione o novo botão de rádio do cofre **criar** serviços de recuperação. Especifique um nome para o cofre. É criado na mesma região que a partilha de ficheiros. Por predefinição, o cofre é criado no mesmo grupo de recursos que a partilha de ficheiros. Se quiser escolher um grupo de recursos diferente, selecione **Criar novo** link abaixo da queda do Tipo **de Recurso** e especifique um nome para o grupo de recursos. Selecione **OK** para continuar.

       ![Criar novo cofre](./media/backup-afs/create-new-vault.png)

      >[!IMPORTANT]
      >Se a conta de armazenamento estiver registada num cofre, ou se houver poucas ações protegidas dentro da conta de armazenamento que está a tentar proteger, o nome do cofre dos Serviços de Recuperação será pré-povoado e não poderá editá-la [Saiba mais aqui.](backup-azure-files-faq.md#why-cant-i-change-the-vault-to-configure-backup-for-the-file-share)

1. Para a seleção **de Política de Backup,** faça uma das seguintes:

    * Deixe a política de incumprimento. Irá agendar backups diários com uma retenção de 30 dias.

    * Selecione uma política de backup existente, se tiver uma, a partir do menu de entrega da Política de **Backup.**

       ![Escolha a política de backup](./media/backup-afs/choose-backup-policy.png)

    * Crie uma nova política com retenção diária/semanal/mensal/anual de acordo com a sua exigência.  

         1. Selecione o **Texto de Ligação de Uma Nova Política.**

         2. O painel de contexto **da Política de Apoio** abre à direita. Especifique um nome de política na caixa de texto e escolha o período de retenção de acordo com o seu requisito. Apenas a opção de retenção diária é ativada por padrão. Se pretender ter retenção semanal, mensal ou anual, selecione a caixa de verificação correspondente e forneça o valor de retenção pretendido.

         3. Depois de especificar os valores de retenção e um nome de política válido, selecione **OK**.

            ![Criar nova política de backup](./media/backup-afs/create-new-backup-policy.png)

1. **Selecione Ativar a cópia de segurança** para começar a proteger a partilha de ficheiros.

   ![Selecione Ativar a cópia de segurança](./media/backup-afs/select-enable-backup.png)

1. Pode rastrear o progresso da configuração nas notificações do portal ou monitorizando os trabalhos de backup debaixo do cofre que está a usar para proteger a partilha de ficheiros.

   ![Notificações do portal](./media/backup-afs/portal-notifications.png)

1. Após a conclusão da operação de cópia de segurança de configuração, selecione **Backup** na secção **operações** do painel de partilha de ficheiros. O painel de contexto que lista **o Vault Essentials** será carregado à direita. A partir daí, pode acionar as operações de backup a pedido e restaurar as operações.

   ![Essenciais do cofre](./media/backup-afs/vault-essentials.png)

## <a name="run-an-on-demand-backup-job"></a>Executar um trabalho de reserva a pedido

Ocasionalmente, pode querer gerar uma imagem de backup, ou ponto de recuperação, fora dos horários programados na política de backup. Uma razão comum para gerar uma cópia de segurança a pedido é logo após configurar a política de reserva. Com base no horário na política de backup, pode levar horas ou dias até que uma foto seja tirada. Para proteger os dados até que a política de cópia de segurança seja aplicada, inicie uma cópia de segurança a pedido. A criação de uma cópia de segurança a pedido é muitas vezes necessária antes de escruissar as alterações planeadas nas suas ações de ficheiros.

### <a name="from-the-recovery-services-vault"></a>Do cofre dos Serviços de Recuperação

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

### <a name="from-the-file-share-pane"></a>Do painel de partilha de ficheiros

1. Abra o painel de **visão geral** da partilha de ficheiros para o qual pretende obter uma cópia de segurança a pedido.

1. Selecione **Cópia de Segurança** na secção **Operação.** O painel de contexto que lista **o Vault Essentials** será carregado à direita. **Selecione Backup Now** para obter uma cópia de segurança a pedido.

   ![Selecione Backup Agora](./media/backup-afs/select-backup-now.png)

1. O **painel de apoio agora** abre. Especifique a retenção para o ponto de recuperação. Pode ter uma retenção máxima de 10 anos para um backup a pedido.

   ![Reter data de backup](./media/backup-afs/retain-backup-date.png)

1. Selecione **OK** para confirmar.

>[!NOTE]
>O Azure Backup bloqueia a conta de armazenamento quando configura a proteção para qualquer ação de ficheiro na conta correspondente. Isto proporciona proteção contra a supressão acidental de uma conta de armazenamento com ações de ficheiros apoiadas.

## <a name="best-practices"></a>Melhores práticas

* Não elimine as imagens criadas pela Azure Backup. A eliminação de instantâneos pode resultar na perda de pontos de recuperação e/ou falhas de restauro.

* Não remova o cadeado tirado na conta de armazenamento pela Azure Backup. Se eliminar o bloqueio, a sua conta de armazenamento será propensa a eliminação acidental e, se for eliminada, perderá as suas fotos ou cópias de segurança.

## <a name="next-steps"></a>Passos seguintes

Aprenda a:

* [Restaurar ações de ficheiros Azure](restore-afs.md)
* [Gerir backups de partilha de ficheiros Azure](manage-afs-backup.md)
