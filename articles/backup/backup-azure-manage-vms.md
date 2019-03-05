---
title: Gerir e monitorizar as cópias de segurança de VM do Azure com o serviço de cópia de segurança do Azure
description: Saiba como gerir e monitorizar a cópia de segurança de VM do Azure com o serviço de cópia de segurança do Azure.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: sogup
ms.openlocfilehash: 47acccf0cdb246683314322ed73f21446e3a9345
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57310034"
---
# <a name="manage-azure-vm-backups"></a>Gerir as cópias de segurança de VMs do Azure

Este artigo descreve como gerir VMs do Azure, uma cópia de segurança com o [serviço de cópia de segurança do Azure](backup-overview.md) cópias de segurança e resume as informações de alertas de cópia de segurança disponíveis no dashboard do portal.

No portal do Azure, o dashboard do Cofre de serviços de recuperação fornece acesso a informações sobre o cofre, incluindo:

* A mais recente cópia de segurança, que também é o ponto de restauro mais recente.
* a política de cópia de segurança
* O tamanho total de todos os instantâneos de cópia de segurança
* O número de VMs ativada para a cópia de segurança

Pode gerir cópias de segurança utilizando o dashboard e por desagregação para baixo para VMs individuais. Cópia de segurança da máquina para começar abrindo o Cofre no dashboard.

![Vista completa com controlo de deslize](./media/backup-azure-manage-vms/bottom-slider.png)

## <a name="view-vms-in-the-dashboard"></a>Ver VMs num dashboard

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. No menu Hub, clique em **Procurar** e na lista de recursos, escreva **Serviços de Recuperação**. À medida que começa a escrever, a lista filtra com base na sua entrada. Clique em **Cofre dos Serviços de Recuperação**.

    ![Passo 1 da Criação de um Cofre dos Serviços de Recuperação](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

3. Facilidade de utilização, faça duplo clique no cofre na lista de cofres > **afixar ao dashboard**.
4. Abra o dashboard do cofre.
    ![Abra o dashboard do cofre e o painel de definições](./media/backup-azure-manage-vms/full-view-rs-vault.png)

5. Sobre o **itens de cópia de segurança** mosaico, clique em **máquinas virtuais do Azure**.

    ![Mosaico de abrir itens de cópia de segurança](./media/backup-azure-manage-vms/contoso-vault-1606.png)

6. Na **itens de cópia de segurança** painel, pode ver a última tarefa de cópia de segurança para cada item. Neste exemplo, existe uma máquina virtual, demovm-markgal, protegida por este cofre.  

    ![Mosaico de itens de cópia de segurança](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

7. No dashboard do item do cofre, pode criar ou modificar as políticas de cópia de segurança, ver pontos de restauro, execute uma paragem de cópia de segurança, a pedido e retomar a proteção de VMs, eliminar pontos de recuperação e executar um restauro.

    ![Dashboard de itens de cópia de segurança com o painel de definições](./media/backup-azure-manage-vms/item-dashboard-settings.png)

## <a name="manage-backup-policies"></a>Gerir políticas de cópia de segurança
1. Sobre o [dashboard de item do cofre](#view-vms-in-the-dashboard), clique em **todas as definições** .

    ![Painel de política de cópia de segurança](./media/backup-azure-manage-vms/all-settings-button.png)
2. Na **configurações**, clique em **política de cópia de segurança**.
3. Partir do **escolher política de cópia de segurança** menu:

   * Para alterar as políticas, selecione uma política diferente e clique em **guardar**. A nova política é imediatamente aplicada ao cofre.
   * Para criar uma política, selecione **criar novo**. [Saiba mais](backup-azure-arm-vms-prepare.md#configure-a-backup-policy)

     ![Cópia de segurança da máquina virtual](./media/backup-azure-manage-vms/backup-policy-create-new.png)


## <a name="run-an-on-demand-backup"></a>Executar cópias de segurança a pedido

Pode demorar uma demanda cópia de segurança de uma VM, uma vez que é configurado para proteção.

- Se a cópia de segurança inicial estiver pendente, a cópia de segurança a pedido cria uma cópia completa da máquina virtual no cofre dos serviços de recuperação.
- Se a cópia de segurança inicial estiver concluída, uma cópia de segurança a pedido irá enviar apenas alterações de instantâneo anterior, para o Cofre dos serviços de recuperação. Ou seja, cópias de segurança subsequentes são sempre incrementais.
- O intervalo de retenção para uma cópia de segurança a pedido é o valor de retenção especificado no momento do acionamento de tarefa de cópia de segurança.

Para acionar uma cópia de segurança a pedido:

1. Na [dashboard de item do cofre](#view-vms-in-the-dashboard), clique em **Item de cópia de segurança** sob o **Item protegido** secção.

    ![Cópia de segurança agora botão](./media/backup-azure-manage-vms/backup-now-button.png)

2. Clique em **Máquina Virtual do Azure** partir **tipo de gestão de cópia de segurança**. O **Item de cópia de segurança (Máquina Virtual do Azure)** é apresentado o painel.
3. Selecione uma VM e clique em **cópia de segurança agora** para criar uma cópia de segurança a pedido. O **painel cópia de segurança agora** aparece.
4. Na **reter cópia de segurança até** opção, especifique uma data para cópia de segurança a ser mantido.

    ![Cópia de segurança agora botão](./media/backup-azure-manage-vms/backup-now-check.png)

5. Clique em **OK**, para executar a tarefa de cópia de segurança.

Para acompanhar o progresso para a tarefa, no dashboard do cofre, clique nas **tarefas de cópia de segurança** mosaico.

## <a name="stop-protecting-a-vm"></a>Parar a proteção de uma VM

Existem duas formas de parar a proteção de VMs:

- Parar todas as tarefas de cópia de segurança futuras e eliminar todos os pontos de recuperação. Não será possível restaurar a VM neste caso.
- Parar todas as tarefas de cópia de segurança futuras, mas reter os pontos de recuperação. Existe um custo associado a reter os pontos de recuperação no cofre. No entanto, o benefício de reter os pontos de recuperação é que possível restaurar a VM se for necessário. [Saiba mais](https://azure.microsoft.com/pricing/details/backup/) sobre os detalhes dos preços.

>[!NOTE]
>
* Se **parar cópia de segurança** com **reter dados de cópia de segurança**, não irão expirar os pontos de recuperação de acordo com a política de cópia de segurança, uma vez que a coleta de lixo (GC) não é executado para as origens de dados Inativas. É-lhe cobrada as instâncias protegidas e o armazenamento consumido. Os pontos de recuperação só serão possível limpar depois de retomar a cópia de segurança (voltar a ativar a proteção) de acordo com a política ou elimine manualmente os dados de cópia de segurança.
* Se eliminar uma origem de dados sem parar cópia de segurança, novas cópias de segurança começam a falhar. Novamente, os pontos de recuperação antigos irão expirar acordo com a política, mas um último ponto de recuperação sempre será mantido até parar cópia de segurança e eliminar os dados.

Para parar a proteção para uma máquina virtual:

1. Sobre o [dashboard de item do cofre](#view-vms-in-the-dashboard), clique em **parar cópia de segurança**.
2. Escolha se pretende manter ou eliminar os dados de cópia de segurança e confirmar conforme necessário. Confirme conforme necessário e, opcionalmente, forneça um comentário. Se não tiver a certeza do nome do item, Paire o rato sobre o ponto de exclamação para ver o nome.

    ![Parar proteção](./media/backup-azure-manage-vms/retain-or-delete-option.png)

 Uma mensagem de notificação permite-lhe saber que as tarefas de cópia de segurança foram paradas.

## <a name="resume-protection-of-a-vm"></a>Retomar a proteção de uma VM

Se retidos dados de cópia de segurança quando a VM foi parada, pode retomar a proteção. Caso o tenha eliminado dados de cópia de segurança não é possível retomar.

Para retomar a proteção da máquina virtual:

1. Sobre o [dashboard de item do cofre](#view-vms-in-the-dashboard), clique em **retomar cópia de segurança**.

2. Siga os passos em [gerir políticas de cópia de segurança](#manage-backup-policies) para atribuir a política para a máquina virtual. Pode escolher uma política diferente que a política com a qual máquina virtual foi protegida inicialmente.
3. Depois da política de cópia de segurança é aplicada à máquina virtual, verá a seguinte mensagem.

    ![VM protegida com êxito](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Eliminar Dados de cópia de segurança

Pode eliminar os dados de cópia de segurança associados a uma VM durante a **parar cópia de segurança** tarefa, ou em qualquer altura após a cópia de segurança tarefa for concluída.

- Até pode ser vantajoso aguardar dias ou semanas antes de eliminar os pontos de recuperação.
- Ao contrário de restaurar pontos de recuperação, ao eliminar dados de cópia de segurança, não pode escolher pontos de recuperação específicos para eliminar. Se optar por eliminar os dados de cópia de segurança, tem de eliminar todos os pontos de recuperação associados ao item.

Este procedimento pressupõe que a tarefa de cópia de segurança para a VM foi parada ou desabilitada.


1. Sobre o [dashboard de item do cofre](#view-vms-in-the-dashboard), clique em **Eliminar cópia de segurança**.

    ![Tipo de VM](./media/backup-azure-manage-vms/delete-backup-buttom.png)

2. Escreva o nome do item para confirmar que pretende eliminar os pontos de recuperação.

    ![Parar de verificação](./media/backup-azure-manage-vms/item-verification-box.png)

3. Para eliminar os dados de cópia de segurança para o item atual, clique em ![botão Parar de cópia de segurança](./media/backup-azure-manage-vms/delete-button.png)

    Uma mensagem de notificação permite-lhe saber que os dados de cópia de segurança foi eliminados.

## <a name="next-steps"></a>Passos Seguintes
- [Saiba mais sobre](backup-azure-vms-first-look-arm.md) backup VMs do Azure a partir das definições de VM.
- [Saiba mais sobre](backup-azure-arm-restore-vms.md) restaurar VMs.
- [Saiba mais sobre](backup-azure-monitor-vms.md) monitorização cópias de segurança de VM do Azure.
