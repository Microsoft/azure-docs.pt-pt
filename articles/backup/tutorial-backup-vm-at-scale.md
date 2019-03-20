---
title: Fazer cópia de segurança de máquinas virtuais do Azure em escala
description: Criar cópias de segurança de várias máquinas virtuais para o Azure em simultâneo
services: backup
keywords: virtual machine backup; virtual machine back up; back up vm; backup vm; backup Azure vm; backup and disaster recovery
author: rayne-wiselman
ms.author: raynew
ms.date: 01/31/2019
ms.topic: tutorial
ms.service: backup
ms.custom: mvc
ms.openlocfilehash: 99f5b09d0b5dfc144dca7f19efff3f0656a82b35
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58093250"
---
# <a name="use-azure-portal-to-back-up-multiple-virtual-machines"></a>Utilizar o portal do Azure para criar cópias de segurança de várias máquinas virtuais

Quando cria cópias de segurança de dados no Azure, esses dados são armazenados num recurso do Azure chamado “Cofre dos Serviços de Recuperação”. O recurso Cofre dos Serviços de Recuperação está disponível no menu Definições da maioria dos serviços do Azure. A vantagem de ter o Cofre dos Serviços de Recuperação integrado no menu Definições de quase todos os serviços do Azure é que permite criar cópias de segurança dos dados muito facilmente. No entanto, trabalhar com cada base de dados ou máquina virtual da sua empresa individualmente é entediante. E se quiser criar uma cópia de segurança dos dados de todas as máquinas virtuais de um departamento ou de uma determinada localização? É fácil criar cópias de segurança de várias máquinas virtuais mediante a criação de uma política de cópia de segurança e aplicá-la às máquinas virtuais pretendidas. Este tutorial explica como:

> [!div class="checklist"]
> * Criar um cofre dos Serviços de Recuperação 
> * Definir uma política de cópia de segurança
> * Aplicar a política de cópia de segurança para proteger várias máquinas virtuais
> * Acionar um trabalho de cópia de segurança a pedido para as máquinas virtuais protegidas

## <a name="log-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

O cofre dos Serviços de Recuperação contém os dados para os quais foi criada a cópia de segurança, bem como a política de cópia de segurança aplicada às máquinas virtuais protegidas. A cópia de segurança das máquinas virtuais é um processo local. Não pode criar cópias de segurança de máquinas virtuais de uma localização para um cofre dos Serviços de Recuperação noutra localização. Por isso, para cada localização do Azure com máquinas virtuais para as quais criar cópias de segurança, tem de existir, pelo menos, um cofre dos Serviços de Recuperação nessa localização.

1. No menu da esquerda, selecione **Todos os serviços** e, na lista de serviços, escreva *Serviços de Recuperação*. À medida que escreve, a lista de recursos filtra. Quando vir os cofres dos Serviços de Recuperação na lista, selecione-o para abrir o respetivo menu.

    ![Abrir o menu Cofre dos Serviços de Recuperação](./media/tutorial-backup-vm-at-scale/full-browser-open-rs-vault.png) <br/>

2. No menu **Cofre dos Serviços de Recuperação**, clique em **Adicionar** para abrir o respetivo menu.

    ![Abrir o menu do cofre](./media/tutorial-backup-vm-at-scale/provide-vault-detail-2.png)

3. No menu do cofre dos Serviços de Recuperação,

    - escreva *myRecoveryServicesVault* em **Name** (Nome).
    - O ID da subscrição atual aparece em **Subscrição**. Se tiver subscrições adicionais, pode escolher outra subscrição para o cofre novo.
    - Em **Grupo de recursos**, selecione **Utilizar existente** *myResourceGroup*. Se *myResourceGroup* não existir, selecione **Criar novo** e escreva *myResourceGroup*.
    - No menu pendente **Localização**, escolha *Europa Ocidental*.
    - Clique em **Criar** para criar o cofre dos Serviços de Recuperação.

Os cofres dos Serviços de Recuperação têm de estar na mesma localização das máquinas virtuais que estão a ser protegidas. Se tiver máquinas virtuais em várias regiões, crie um cofre dos Serviços de Recuperação em cada região. Este tutorial cria um cofre dos Serviços de Recuperação na *Europa Ocidental*, uma vez que *myVM* (a máquina virtual criada com o início rápido) foi criada aí.

Pode demorar vários minutos até que o cofre dos Serviços de Recuperação seja criado. Monitorize as notificações de estado na área superior direita do portal. Quando o cofre for criado, aparecerá na lista de cofres dos Serviços de Recuperação.

Quando criar um cofre dos Serviços de Recuperação, esse cofre tem o armazenamento georredundante por predefinição. De modo a proporcionar resiliência de dados, o armazenamento georredundante replica os dados várias vezes em duas regiões do Azure.

## <a name="set-backup-policy-to-protect-vms"></a>Definir a política de cópia de segurança para proteger VMs

Depois de criar o cofre dos Serviços de Recuperação, o próximo passo é configurá-lo para o tipo de dados e definir a política de cópia de segurança. A política é a agenda da frequência e do momento em que os pontos de recuperação são obtidos. A política também inclui o período de retenção para os pontos de recuperação. Neste tutorial, vamos imaginar que o seu negócio é um complexo desportivo que inclui um hotel, um estádio e restaurantes e concessões e que vai proteger os dados nas máquinas virtuais. Os passos seguintes criam uma política de cópia de segurança para os dados financeiros.

1. Na lista dos cofres dos Serviços de Recuperação, selecione **myRecoveryServicesVault** para abrir o dashboard do mesmo.

   ![Menu “abrir cenário”](./media/tutorial-backup-vm-at-scale/open-vault-from-list.png)

2. No menu do dashboard do cofre, clique em **Backup** (Cópia de Segurança) para abrir o menu de Cópia de Segurança.

3. No menu Objetivo da Cópia de Segurança, no menu pendente **Where is your workload running** (Onde está a ser executada a sua carga de trabalho), escolha o *Azure*. No menu pendente **What do you want to backup** (Do que pretende criar uma cópia de segurança), escolha *Virtual machine* (Máquina virtual) e clique em **Backup** (Criar cópia de segurança).

    Estas ações preparam o cofre dos Serviços de Recuperação para interagir com uma máquina virtual. Os cofres dos Serviços de Recuperação têm uma política predefinida que cria um ponto de restauro por dia e retém-nos durante 30 dias.

    ![Menu “abrir cenário”](./media/tutorial-backup-vm-at-scale/backup-goal.png)

4. Para criar uma política nova, no menu Backup policy (Política de cópia de segurança), no menu pendente **Choose backup policy** (Escolher política de cópia de segurança), selecione *Create New* (Criar nova).

    ![Selecionar a carga de trabalho](./media/tutorial-backup-vm-at-scale/create-new-policy.png)

5. No menu **Backup policy** (Política de cópia de segurança), em **Policy Name** (Nome da Política), escreva *Finance* (Financeiro). Introduza as seguintes alterações à política de cópia de segurança:
   - Em **Backup frequency** (Frequência da cópia de segurança), defina o fuso horário como *Hora Central*. Uma vez que o complexo desportivo está localizado no Texas, o proprietário quer que a hora seja a local. Deixe a frequência de cópia de segurança definida como Daily at 3:30AM. (Diariamente às 03:30).
   - Em **Retention of daily backup point** (Retenção do ponto de criação de cópia de segurança diário), defina o período como 90 dias.
   - Em **Retention of weekly backup point** (Retenção do ponto de criação de cópia de segurança semanal), utilize o ponto de restauro *Monday* (Segunda-feira) e retenha-o durante 52 semanas.
   - Em **Retention of monthly backup point** (Retenção do ponto de criação de cópia de segurança mensal), utilize o ponto de restauro First Sunday of the month (Primeiro domingo do mês) e retenha-o durante 36 meses.
   - Anule seleção da opção **Retention of yearly backup point** (Retenção do ponto de criação de cópia de segurança anual). O responsável pela área financeira não quer manter os dados por mais de 36 meses.
   - Clique em **OK** para criar a política de cópias de segurança.

     ![Selecionar a carga de trabalho](./media/tutorial-backup-vm-at-scale/set-new-policy.png)

     Depois de criar a política de cópia de segurança, associe-a às máquinas virtuais.

6. Na caixa de diálogo **Select virtual machines** (Selecionar máquinas virtuais), selecione *myVM* e clique em **OK** para implementar a política de cópia de segurança nas máquinas virtuais.

    São apresentadas todas as máquinas virtuais que estejam na mesma localização e que ainda não tenham sido associadas a uma política de cópia de segurança. *myVMH1* e *myVMR1* são selecionadas para serem associadas à política *Finance*.

    ![Selecionar a carga de trabalho](./media/tutorial-backup-vm-at-scale/choose-vm-to-protect.png)

    Quando a implementação estiver concluída, receberá uma notificação de que foi concluída com êxito.

## <a name="initial-backup"></a>Cópia de segurança inicial

Ativou a cópia de segurança para os cofres dos Serviços de Recuperação, mas não foi criada uma cópia de segurança inicial. Acionar a primeira cópia de segurança, para que os dados sejam protegidos, é uma boa prática do processo de recuperação após desastre.

Para executar um trabalho de cópia de segurança a pedido:

1. No dashboard do cofre, clique em **3**, em **Backup Items**, para abrir o respetivo menu.

    ![Ícone Definições](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)

    É aberto o menu **Backup Items** (Itens de Cópia de Segurança).

2. No menu **Backup Items**, clique em **Azure Virtual Machine** para abrir a lista de máquinas virtuais associadas ao cofre.

    ![Ícone Definições](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

    É aberta a lista **Itens de Cópia de Segurança**.

    ![Tarefa de cópia de segurança acionada](./media/tutorial-backup-vm-at-scale/initial-backup-context-menu.png)

3. Na lista **Itens de Cópia de Segurança**, clique nas reticências **...** para abrir o menu Contexto.

4. No menu Contexto, selecione **Backup Now** (Criar Cópia de Segurança Agora).

    ![Menu Contexto](./media/tutorial-backup-vm-at-scale/context-menu.png)

    É aberto o menu Backup Now.

5. No menu Backup Now, introduza o último dia de retenção do ponto de recuperação e clique em **Backup**.

    ![definir o último dia em que o ponto de recuperação de Criar Cópia de Segurança Agora é mantido](./media/tutorial-backup-vm-at-scale/backup-now-short.png)

    As notificações de implementação permitem-lhe saber se a tarefa de cópia de segurança foi acionada e que pode acompanhar o progresso da tarefa na página Tarefas de cópias de segurança. Dependendo do tamanho da sua máquina virtual, a criação da cópia de segurança inicial poderá demorar algum tempo.

    Quando o trabalho de cópia de segurança inicial estiver concluído, pode ver o estado do mesmo no menu Backup job (Trabalho de cópia de segurança). O trabalho de cópia de segurança a pedido criou o ponto de restauro inicial para *myVM*. Se pretender criar cópias de segurança de outras máquinas virtuais, repita estes passos para cada uma.

    ![Mosaico Tarefas de cópia de segurança](./media/tutorial-backup-vm-at-scale/initial-backup-complete.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser continuar a trabalhar com os tutoriais subsequentes, não limpe os recursos criados neste tutorial. Se não quiser continuar, utilize os passos seguintes para eliminar todos os recursos criados por este tutorial no portal do Azure.

1. No dashboard **myRecoveryServicesVault**, clique em **3**, em **Backup Items**, para abrir respetivo menu.

    ![Ícone Definições](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)


2. No menu **Backup Items**, clique em **Azure Virtual Machine** para abrir a lista de máquinas virtuais associadas ao cofre.

    ![Ícone Definições](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

    É aberta a lista **Itens de Cópia de Segurança**.

3. No menu **Backup Items**, clique nas reticências para abrir o menu Context.

    ![Ícone Definições](./media/tutorial-backup-vm-at-scale/context-menu-to-delete-vm.png)

4. No menu de contexto, selecione **Stop backup** (Parar cópia de segurança) para abrir o respetivo menu.

    ![Ícone Definições](./media/tutorial-backup-vm-at-scale/context-menu-for-delete.png)

5. No menu **Stop Backup**, selecione o menu pendente superior e escolha **Delete Backup Data** (Eliminar Dados de Cópia de Segurança).

6. Na caixa de diálogo **Type the name of the Backup item** (Escrever o nome do Item de cópia de segurança), escreva *myVM*.

7. Após o item de cópia de segurança ser verificado (aparece uma marca de verificação), o botão **Stop backup** fica ativado. Clique em **Stop Backup** para parar a política e eliminar os pontos de restauro.

    ![clicar em Stop backup para eliminar o cofre](./media/tutorial-backup-vm-at-scale/provide-reason-for-delete.png)

8. No menu **myRecoveryServicesVault**, clique em **Delete** (Eliminar).

    ![clicar em Stop backup para eliminar o cofre](./media/tutorial-backup-vm-at-scale/deleting-the-vault.png)

    Depois de eliminar o cofre, regresse à lista de cofres dos Serviços de Recuperação.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, utilizou o portal do Azure para:

> [!div class="checklist"]
> * Criar um cofre dos Serviços de Recuperação 
> * Definir o cofre para proteger máquinas virtuais
> * Criar uma política de cópia de segurança e retenção personalizada
> * Atribuir a política para proteger várias máquinas virtuais
> * Acionar uma cópia de segurança a pedido de máquinas virtuais

Avance para o próximo tutorial para restaurar uma máquina virtual do Azure a partir do disco.

> [!div class="nextstepaction"]
> [Restore VMs using CLI](./tutorial-restore-disk.md) (Restaurar VMs com a CLI)
