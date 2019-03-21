---
title: Gerir e monitorizar as cópias de segurança de VM do Azure com o serviço de cópia de segurança do Azure
description: Saiba como gerir e monitorizar as cópias de segurança de VM do Azure com o serviço de cópia de segurança do Azure.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: sogup
ms.openlocfilehash: aa953440f03137f3359276bc9e06cb0c73f0ab4a
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295781"
---
# <a name="manage-azure-vm-backups"></a>Gerir as cópias de segurança de VMs do Azure

Este artigo descreve como gerir máquinas de virtuais (VMs) do Azure que são submetidas a backup utilizando o [serviço de cópia de segurança do Azure](backup-overview.md). O artigo também resume as informações de cópia de segurança que pode encontrar no dashboard do cofre.


No portal do Azure, o dashboard do Cofre de serviços de recuperação fornece acesso para o Cofre de informações, incluindo:

* A mais recente cópia de segurança, que também é o ponto de restauro mais recente.
* A política de cópia de segurança.
* O tamanho total de todos os instantâneos de cópia de segurança.
* O número de VMs que estão ativadas para cópias de segurança.

Pode gerir as cópias de segurança através do dashboard e pela exploração para baixo para VMs individuais. Para começar a cópias de segurança da máquina, abra o Cofre no dashboard.

![Vista de dashboard completo com controlo de deslize](./media/backup-azure-manage-vms/bottom-slider.png)

## <a name="view-vms-on-the-dashboard"></a>VMs do modo de exibição no dashboard

Para ver as VMs no dashboard do cofre:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. No Hub menu, selecione **procurar**. Na lista de recursos, escreva **Serviços de Recuperação**. À medida que escreve, a lista é filtrada com base na sua entrada. Selecione **cofres dos serviços de recuperação**.

    ![Criar um cofre dos Serviços de Recuperação ](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

3. Facilidade de utilização, com o botão direito do cofre e selecione **afixar ao dashboard**.
4. Abra o dashboard do cofre.

    ![Abra o dashboard do cofre e o painel de definições](./media/backup-azure-manage-vms/full-view-rs-vault.png)

5. Sobre o **itens de cópia de segurança** mosaico, selecione **máquinas virtuais do Azure**.

    ![Abra o mosaico de itens de cópia de segurança](./media/backup-azure-manage-vms/contoso-vault-1606.png)

6. Sobre o **itens de cópia de segurança** painel, pode ver a lista de VMs protegidas. Neste exemplo, o Cofre protege uma máquina virtual: demobackup.  

    ![Ver o painel de itens de cópia de segurança](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

7. A partir do dashboard do item de cofre, modificar as políticas de cópia de segurança, execute uma paragem de cópia de segurança, a pedido ou retomar a proteção de VMs, eliminar dados de cópia de segurança, ver pontos de restauro e executar um restauro.

    ![O dashboard de itens de cópia de segurança e o painel de definições](./media/backup-azure-manage-vms/item-dashboard-settings.png)

## <a name="manage-backup-policy-for-a-vm"></a>Gerir a política de cópia de segurança para uma VM

Para gerir uma política de cópia de segurança:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/). Abra o dashboard do cofre.
2. Sobre o **itens de cópia de segurança** mosaico, selecione **máquinas virtuais do Azure**.

    ![Abra o mosaico de itens de cópia de segurança](./media/backup-azure-manage-vms/contoso-vault-1606.png)

3. Sobre o **itens de cópia de segurança** painel, pode ver a lista de VMs protegidas e o estado da última cópia de segurança com a hora de pontos de restauro mais recente.

    ![Ver o painel de itens de cópia de segurança](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

4. A partir do dashboard do item de cofre, pode selecionar uma política de cópia de segurança.

   * Para mudar as políticas, selecione uma política diferente e, em seguida, selecione **guardar**. A nova política é imediatamente aplicada ao cofre.

     ![Escolha uma política de cópia de segurança](./media/backup-azure-manage-vms/backup-policy-create-new.png)

## <a name="run-an-on-demand-backup"></a>Executar cópias de segurança a pedido
Pode executar uma cópia de segurança a pedido de uma VM depois de configurar a proteção. Considere estes detalhes:

- Se a cópia de segurança inicial estiver pendente, a cópia de segurança a pedido cria uma cópia completa da VM no cofre dos serviços de recuperação.
- Se a cópia de segurança inicial estiver concluída, uma cópia de segurança a pedido irá enviar apenas alterações de instantâneo anterior, para o Cofre dos serviços de recuperação. Ou seja, backups posteriores são sempre incrementais.
- O período de retenção para uma cópia de segurança a pedido é o valor de retenção que especifica quando acionar a cópia de segurança.

Para acionar uma cópia de segurança a pedido:

1. Sobre o [dashboard de item do cofre](#view-vms-on-the-dashboard), em **Item protegido**, selecione **Item de cópia de segurança**.

    ![A opção agora de cópia de segurança](./media/backup-azure-manage-vms/backup-now-button.png)

2. Partir **tipo de gestão de cópia de segurança**, selecione **Máquina Virtual do Azure**. O **Item de cópia de segurança (Máquina Virtual do Azure)** é apresentado o painel.
3. Selecione uma VM e selecione **cópia de segurança agora** para criar uma cópia de segurança a pedido. O **cópia de segurança agora** é apresentado o painel.
4. Na **reter cópia de segurança até** campo, especifique uma data para a cópia de segurança a ser mantido.

    ![O calendário de cópia de segurança agora](./media/backup-azure-manage-vms/backup-now-check.png)

5. Selecione **OK** para executar a tarefa de cópia de segurança.

Para controlar o progresso da tarefa, no dashboard do cofre, selecione o **tarefas de cópia de segurança** mosaico.

## <a name="stop-protecting-a-vm"></a>Parar a proteção de uma VM

Existem duas formas de parar a proteção de uma VM:

- Parar todas as tarefas de cópia de segurança futuras e eliminar todos os pontos de recuperação. Neste caso, não será possível restaurar a VM.
- Parar todas as tarefas de cópia de segurança futuras e manter os pontos de recuperação. Embora terá de pagar para manter os pontos de recuperação no cofre, poderá restaurar a VM, se necessário. Para obter mais informações, consulte [preços de cópia de segurança do Azure](https://azure.microsoft.com/pricing/details/backup/).

>[!NOTE]
>Se eliminar uma origem de dados sem parar as cópias de segurança, novas cópias de segurança irão falhar. Pontos de recuperação antigos irão expirar, de acordo com a política, mas um último ponto de recuperação sempre será mantido até que as cópias de segurança de parar e eliminar os dados.
>

Para parar a proteção para uma VM:

1. Sobre o [dashboard do item do cofre](#view-vms-on-the-dashboard), selecione **parar cópia de segurança**.
2. Escolha se pretende manter ou eliminar os dados de cópia de segurança e confirme a sua seleção, conforme necessário. Adicione um comentário se desejar. Se não tiver a certeza do nome do item, Paire o rato sobre o ponto de exclamação para ver o nome.

    ![Parar proteção](./media/backup-azure-manage-vms/retain-or-delete-option.png)

     Uma notificação permite-lhe saber que as tarefas de cópia de segurança foram paradas.

## <a name="resume-protection-of-a-vm"></a>Retomar a proteção de uma VM

Se mantiver os dados de cópia de segurança quando parar a VM, pode retomar a proteção mais tarde. Se eliminar os dados de cópia de segurança, não é possível retomar a proteção.

Para retomar a proteção para uma VM:

1. Sobre o [dashboard do item do cofre](#view-vms-on-the-dashboard), selecione **retomar cópia de segurança**.

2. Siga os passos em [gerir políticas de cópia de segurança](#manage-backup-policy-for-a-vm) para atribuir a política para a VM. Não precisa de escolher a política de proteção inicial da VM.
3. Depois de aplicar a política de cópia de segurança para a VM, verá a seguinte mensagem:

    ![Mensagem a indicar que uma VM protegida com êxito](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>eliminar dados de cópia de segurança

Pode eliminar dados de cópia de segurança de uma VM durante a **parar cópia de segurança** tarefa ou depois de concluída a tarefa de cópia de segurança. Antes de eliminar dados de cópia de segurança, considere estes detalhes:

- Poderá ser aconselhável aguardar dias ou semanas antes de eliminar os pontos de recuperação.
- Ao contrário do processo para restaurar pontos de recuperação, ao eliminar dados de cópia de segurança, não é possível escolher pontos de recuperação específicos para eliminar. Se eliminar os dados de cópia de segurança, excluir todos os pontos de recuperação associados.

Depois de parar ou desativar a tarefa de cópia de segurança da VM, pode eliminar os dados de cópia de segurança:


1. Sobre o [dashboard de item do cofre](#view-vms-on-the-dashboard), selecione **eliminar dados de cópia de segurança**.

    ![Selecionar cópia de segurança de eliminação](./media/backup-azure-manage-vms/delete-backup-buttom.png)

1. Escreva o nome do item de cópia de segurança para confirmar que pretende eliminar os pontos de recuperação.

    ![Confirme que pretende eliminar os pontos de recuperação](./media/backup-azure-manage-vms/item-verification-box.png)

1. Para eliminar os dados de cópia de segurança para o item, selecione **eliminar**. Uma mensagem de notificação permite-lhe saber que os dados de cópia de segurança foi eliminados.

## <a name="next-steps"></a>Passos Seguintes
- Saiba como [cópia de segurança de VMs do Azure, das definições da VM](backup-azure-vms-first-look-arm.md).
- Saiba como [restaurar VMs](backup-azure-arm-restore-vms.md).
- Saiba como [monitorizar cópias de segurança de VM do Azure](backup-azure-monitor-vms.md).
