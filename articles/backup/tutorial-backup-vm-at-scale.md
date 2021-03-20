---
title: Tutorial - Apoiar várias máquinas virtuais Azure
description: Neste tutorial, aprenda a criar um cofre dos Serviços de Recuperação, defina uma política de backup e, simultaneamente, apoie várias máquinas virtuais.
ms.date: 07/26/2020
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: a9517ffc1e37d50f7c0e57b9ed53fb8bcf55fd70
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89180580"
---
# <a name="use-azure-portal-to-back-up-multiple-virtual-machines"></a>Utilizar o portal do Azure para criar cópias de segurança de várias máquinas virtuais

Quando cria cópias de segurança de dados no Azure, esses dados são armazenados num recurso do Azure chamado “Cofre dos Serviços de Recuperação”. O recurso Cofre dos Serviços de Recuperação está disponível no menu Definições da maioria dos serviços do Azure. O benefício de ter o cofre dos Serviços de Recuperação integrado no menu Definições da maioria dos serviços Azure é a facilidade de fazer backup de dados. No entanto, trabalhar individualmente com cada base de dados ou máquina virtual no seu negócio é aborrecido. E se quiser criar uma cópia de segurança dos dados de todas as máquinas virtuais de um departamento ou de uma determinada localização? É fácil apoiar várias máquinas virtuais criando uma política de backup e aplicando essa política às máquinas virtuais desejadas. Este tutorial explica como:

> [!div class="checklist"]
>
> * Criar um cofre dos Serviços de Recuperação 
> * Definir uma política de cópias de segurança
> * Aplicar a política de cópia de segurança para proteger várias máquinas virtuais
> * Acionar um trabalho de cópia de segurança a pedido para as máquinas virtuais protegidas

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

O cofre dos Serviços de Recuperação também contém os dados para os quais foi criada a cópia de segurança, bem como a política de cópia de segurança aplicada às máquinas virtuais protegidas. A cópia de segurança das máquinas virtuais é um processo local. Não se pode fazer uma rede virtual de um local para um cofre dos Serviços de Recuperação noutro local. Por isso, para cada localização do Azure com máquinas virtuais para as quais criar cópias de segurança, tem de existir, pelo menos, um cofre dos Serviços de Recuperação nessa localização.

1. No menu esquerdo, selecione **Todos os serviços**.

    ![Selecionar Todos os serviços](./media/tutorial-backup-vm-at-scale/click-all-services.png)

1. Na caixa de diálogo **Todos os serviços**, introduza *Serviços de Recuperação*. A lista de recursos é filtrada de acordo com a sua entrada. Na lista de recursos, selecione **Cofres dos Serviços de Recuperação**.

    ![Introduzir e escolher cofres dos Serviços de Recuperação](./media/tutorial-backup-vm-at-scale/all-services.png)

    É apresentada a lista dos cofres dos Serviços de Recuperação na subscrição.

1. No dashboard **Cofres dos Serviços de Recuperação**, selecione **Adicionar**.

    ![Adicionar um cofre dos Serviços de Recuperação](./media/tutorial-backup-vm-at-scale/add-button-create-vault.png)

1. No menu do cofre dos Serviços de Recuperação,

    * escreva *myRecoveryServicesVault* em **Name** (Nome).
    * O ID da subscrição atual aparece em **Subscription** (Subscrição). Se tiver subscrições adicionais, pode escolher outra subscrição para o novo cofre.
    * Em **Resource group** (Grupo de recursos), selecione **Use existing** (Utilizar existente) *myResourceGroup*. Se *o myResourceGroup* não existir, selecione **Criar novo** e digite *myResourceGroup*.
    * No menu pendente **Location** (Localização), escolha *West Europe* (Europa Ocidental).

    ![Valores do cofre dos Serviços de Recuperação](./media/tutorial-backup-vm-at-scale/review-and-create.png)

    Os cofres dos Serviços de Recuperação têm de estar na mesma localização das máquinas virtuais que estão a ser protegidas. Se tiver máquinas virtuais em várias regiões, crie um cofre dos Serviços de Recuperação em cada região. Este tutorial cria um cofre de Serviços de Recuperação na *Europa Ocidental* porque foi aí que o *myVM* (a máquina virtual criada com o arranque rápido) foi criado.

1. Quando estiver pronto para criar o cofre dos Serviços de Recuperação, selecione **Criar**.

    ![Criar o cofre dos Serviços de Recuperação](./media/tutorial-backup-vm-at-scale/click-create-button.png)

1. Pode demorar algum tempo a criar o cofre dos Serviços de Recuperação. Monitorize as notificações de estado na área **Notificações** no canto superior direito do portal. Depois de o cofre ser criado, é visível na lista de cofres dos Serviços de Recuperação. Se não vir o seu cofre, selecione **Atualizar**.

     ![Atualizar a lista de cofres de cópia de segurança](./media/tutorial-backup-vm-at-scale/refresh-button.png)

Quando criar um cofre dos Serviços de Recuperação, esse cofre tem o armazenamento georredundante por predefinição. De modo a proporcionar resiliência de dados, o armazenamento georredundante replica os dados várias vezes em duas regiões do Azure.

## <a name="set-backup-policy-to-protect-vms"></a>Definir a política de cópia de segurança para proteger VMs

Depois de criar o cofre dos Serviços de Recuperação, o próximo passo é configurá-lo para o tipo de dados e definir a política de cópia de segurança. A política é a agenda da frequência e do momento em que os pontos de recuperação são obtidos. A política também inclui o período de retenção para os pontos de recuperação. Para este tutorial, vamos assumir que o seu negócio é um complexo desportivo com um hotel, estádio e restaurantes e concessões, e você está protegendo os dados sobre as máquinas virtuais. Os passos seguintes criam uma política de cópia de segurança para os dados financeiros.

1. Na lista dos cofres dos Serviços de Recuperação, selecione **myRecoveryServicesVault** para abrir o dashboard do mesmo.

   ![Menu “abrir cenário”](./media/tutorial-backup-vm-at-scale/open-vault-from-list.png)

1. No menu do painel de instrumentos do cofre, selecione **Backup** para abrir o menu backup.

1. No menu Objetivo da Cópia de Segurança, no menu pendente **Where is your workload running** (Onde está a ser executada a sua carga de trabalho), escolha o *Azure*. A partir do **Que pretende fazer backup** drop-down, escolha máquina *Virtual* e selecione **Backup**.

    Estas ações preparam o cofre dos Serviços de Recuperação para interagir com uma máquina virtual. Os cofres dos Serviços de Recuperação têm uma política predefinida que cria um ponto de restauro por dia e retém-nos durante 30 dias.

    ![Objetivo de backup](./media/tutorial-backup-vm-at-scale/backup-goal.png)

1. Para criar uma nova política, no menu de política de backup, a partir do menu de **backup de backup,** selecione *Criar uma nova política*.

    ![Criar nova política](./media/tutorial-backup-vm-at-scale/create-new-policy.png)

1. O **painel de política de reserva** abrir-se-á. Preencha os seguintes detalhes:
   * Para o tipo **de nome** de política *Financiamento*. Introduza as seguintes alterações à política de cópia de segurança:
   * Em **Backup frequency** (Frequência da cópia de segurança), defina o fuso horário como *Hora Central*. Uma vez que o complexo desportivo está localizado no Texas, o proprietário quer que a hora seja a local. Deixe a frequência de cópia de segurança definida como Daily at 3:30AM. (Diariamente às 03:30).
   * Em **Retention of daily backup point** (Retenção do ponto de criação de cópia de segurança diário), defina o período como 90 dias.
   * Em **Retention of weekly backup point** (Retenção do ponto de criação de cópia de segurança semanal), utilize o ponto de restauro *Monday* (Segunda-feira) e retenha-o durante 52 semanas.
   * Em **Retention of monthly backup point** (Retenção do ponto de criação de cópia de segurança mensal), utilize o ponto de restauro First Sunday of the month (Primeiro domingo do mês) e retenha-o durante 36 meses.
   * Anule seleção da opção **Retention of yearly backup point** (Retenção do ponto de criação de cópia de segurança anual). O responsável pela área financeira não quer manter os dados por mais de 36 meses.
   * Selecione **OK** para criar a política de backup.

     ![Definições de política de backup](./media/tutorial-backup-vm-at-scale/set-new-policy.png)

     Depois de criar a política de cópia de segurança, associe-a às máquinas virtuais.

1. Em **Máquinas Virtuais**, selecione **Adicionar**.

     ![Adicionar máquinas virtuais](./media/tutorial-backup-vm-at-scale/add-virtual-machines.png)

1. O **painel de máquinas virtuais Select** abre-se. Selecione *myVM* e selecione **OK** para implementar a política de backup para as máquinas virtuais.

    Todas as máquinas virtuais que estão no mesmo local, e que ainda não estão associadas a uma política de backup, aparecem. *myVMH1* e *myVMR1* são selecionadas para serem associadas à política *Finance*.

    ![Escolha VMs para proteger](./media/tutorial-backup-vm-at-scale/choose-vm-to-protect.png)

1. Depois de escolhidas as máquinas virtuais, selecione **Ativar backup**.

    Quando a implementação estiver concluída, receberá uma notificação de que a implementação foi concluída com sucesso.

## <a name="initial-backup"></a>Cópia de segurança inicial

Ativaste o backup para os cofres dos Serviços de Recuperação, mas ainda não foi criado um reforço inicial. É uma recuperação de desastres as melhores práticas para desencadear a primeira cópia de segurança, para que os seus dados estão protegidos.

Para executar um trabalho de cópia de segurança a pedido:

1. No painel de instrumentos do cofre, selecione **3** em **Itens de Reserva**, para abrir o menu Itens de Reserva.

    ![Artigos de reserva](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)

    É aberto o menu **Backup Items** (Itens de Cópia de Segurança).

1. No menu **Itens de Cópia de Segurança,** selecione **Azure Virtual Machine** para abrir a lista de máquinas virtuais associadas ao cofre.

    ![Lista de máquinas virtuais](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

1. É aberta a lista **Itens de Cópia de Segurança**.

    ![Tarefa de cópia de segurança acionada](./media/tutorial-backup-vm-at-scale/initial-backup-context-menu.png)

1. Na lista **de Itens de Cópia de Segurança,** selecione as elipses... para abrir o menu Contexto. 

1. No menu Contexto, selecione **Backup Now** (Criar Cópia de Segurança Agora).

    ![Menu de contexto - selecione Backup agora](./media/tutorial-backup-vm-at-scale/context-menu.png)

    É aberto o menu Backup Now.

1. No menu Backup Now, insira o último dia para manter o ponto de recuperação e selecione **OK**.

    ![Definir o último dia o ponto de recuperação backup Agora é mantido](./media/tutorial-backup-vm-at-scale/backup-now-short.png)

    As notificações de implementação permitem-lhe saber se a tarefa de cópia de segurança foi acionada e que pode acompanhar o progresso da tarefa na página Tarefas de cópias de segurança. Dependendo do tamanho da sua máquina virtual, a criação da cópia de segurança inicial poderá demorar algum tempo.

    Quando o trabalho de cópia de segurança inicial estiver concluído, pode ver o estado do mesmo no menu Backup job (Trabalho de cópia de segurança). O trabalho de cópia de segurança a pedido criou o ponto de restauro inicial para *myVM*. Se pretender criar cópias de segurança de outras máquinas virtuais, repita estes passos para cada uma.

    ![Mosaico Tarefas de cópia de segurança](./media/tutorial-backup-vm-at-scale/initial-backup-complete.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretende continuar a trabalhar com tutoriais subsequentes, não limpe os recursos criados neste tutorial. Se não pretende continuar, use os seguintes passos para eliminar todos os recursos criados por este tutorial no portal Azure.

1. No **painel myRecoveryServicesVault,** selecione **3** em **Itens de Cópia de Segurança** para abrir o menu Itens de Cópia de Segurança.

    ![Abra o menu de itens de backup](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)

1. No menu **Itens de Cópia de Segurança,** selecione **Azure Virtual Machine** para abrir a lista de máquinas virtuais associadas ao cofre.

    ![Lista de máquinas virtuais](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

    É aberta a lista **Itens de Cópia de Segurança**.

1. No menu **Itens de Cópia de Segurança,** selecione a elipse para abrir o menu Contexto.

    ![No menu Itens de Cópia de Segurança, abra o menu de contexto](./media/tutorial-backup-vm-at-scale/context-menu-to-delete-vm.png)

1. No menu de contexto, **selecione Parar de fazer backup** para abrir o menu Stop Backup.

    ![Pare o menu de backup](./media/tutorial-backup-vm-at-scale/context-menu-for-delete.png)

1. No menu **Stop Backup**, selecione o menu pendente superior e escolha **Delete Backup Data** (Eliminar Dados de Cópia de Segurança).

1. Na caixa de diálogo **Type the name of the Backup item** (Escrever o nome do Item de cópia de segurança), escreva *myVM*.

1. Uma vez verificado o item de cópia de segurança (aparece uma marca de verificação), o botão **de backup stop** está ativado. Selecione **Stop Backup** para parar a política e elimine os pontos de restauro.

    ![Selecione Parar a cópia de segurança para eliminar o cofre](./media/tutorial-backup-vm-at-scale/provide-reason-for-delete.png)

    >[!NOTE]
    >Os itens eliminados são mantidos no estado de exclusão suave durante 14 dias. Só depois desse período é que o cofre pode ser apagado. Para obter mais informações, consulte [delete a Azure Backup Recovery Services vault](backup-azure-delete-vault.md).

1. Quando não houver mais itens no cofre, selecione **Delete**.

    ![Selecione eliminar](./media/tutorial-backup-vm-at-scale/deleting-the-vault.png)

    Assim que o cofre for apagado, voltará à lista de cofres dos Serviços de Recuperação.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, utilizou o portal do Azure para:

> [!div class="checklist"]
>
> * Criar um cofre dos Serviços de Recuperação 
> * Definir o cofre para proteger máquinas virtuais
> * Criar uma política de cópia de segurança e retenção personalizada
> * Atribuir a política para proteger várias máquinas virtuais
> * Acionar uma cópia de segurança a pedido de máquinas virtuais

Avance para o próximo tutorial para restaurar uma máquina virtual do Azure a partir do disco.

> [!div class="nextstepaction"]
> [Restore VMs using CLI](./tutorial-restore-disk.md) (Restaurar VMs com a CLI)
