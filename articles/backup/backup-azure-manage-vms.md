---
title: Gerir e monitorizar backups Azure VM
description: Aprenda a gerir e monitorizar as cópias de segurança Azure VM utilizando o serviço Azure Backup.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 6e49d1eed81d15970519299fb6f662c650116d6e
ms.sourcegitcommit: 8017209cc9d8a825cc404df852c8dc02f74d584b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84248588"
---
# <a name="manage-azure-vm-backups-with-azure-backup-service"></a>Gerir backups Azure VM com serviço de backup Azure

Este artigo descreve como gerir máquinas virtuais Azure (VMs) que são apoiadas através do [serviço Azure Backup](backup-overview.md). O artigo também resume as informações de reserva que pode encontrar no painel de abóbada.

No portal Azure, o painel de abóbada dos Serviços de Recuperação fornece acesso a informações sobre o cofre, incluindo:

* O último backup, que é também o último ponto de restauro.
* A política de reserva.
* O tamanho total de todos os instantâneos de reserva.
* O número de VMs que estão habilitados para cópias de segurança.

Pode gerir as cópias de segurança utilizando o painel de instrumentos e perfurando para VMs individuais. Para começar as cópias de segurança da máquina, abra o cofre no painel de instrumentos.

![Vista completa do painel com slider](./media/backup-azure-manage-vms/bottom-slider.png)

## <a name="view-vms-on-the-dashboard"></a>Ver VMs no painel de instrumentos

Para ver VMs no painel de abóbada:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. No menu Hub, **selecione Procurar.** Na lista de recursos, escreva **Serviços de Recuperação**. À medida que escreve, a lista é filtrada com base na sua entrada. Selecione **cofres dos Serviços de Recuperação**.

    ![Criar um cofre dos Serviços de Recuperação ](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

3. Para facilitar a utilização, clique com o botão direito no cofre e selecione Pin para o painel de **instrumentos**.
4. Abre o painel do cofre.

    ![Abra o painel de instrumentos e definições do cofre](./media/backup-azure-manage-vms/full-view-rs-vault.png)

5. No **azulejo de itens de cópia** de segurança, selecione **Azure Virtual Machines**.

    ![Abra o azulejo de itens de reserva](./media/backup-azure-manage-vms/contoso-vault-1606.png)

6. No painel **de itens de cópia** de segurança, pode ver a lista de VMs protegidos. Neste exemplo, o cofre protege uma máquina virtual: desmobackup.  

    ![Ver o painel de itens de reserva](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

7. A partir do painel de instrumentos do número de abóbadas, modifique as políticas de backup, faça uma cópia de segurança a pedido, pare ou retome a proteção dos VMs, elimine os dados de backup, veja os pontos de restauro e faça uma restauração.

    ![O painel de itens de cópia de segurança e o painel de definições](./media/backup-azure-manage-vms/item-dashboard-settings.png)

## <a name="manage-backup-policy-for-a-vm"></a>Gerir a política de backup para um VM

Para gerir uma política de backup:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/). Abre o painel do cofre.
2. No **azulejo de itens de cópia** de segurança, selecione **Azure Virtual Machines**.

    ![Abra o azulejo de itens de reserva](./media/backup-azure-manage-vms/contoso-vault-1606.png)

3. No painel **de Itens de Cópia de Segurança,** pode ver a lista de VMs protegidos e o último estado de backup com o tempo de pontos de restauro mais recente.

    ![Ver o painel de itens de reserva](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

4. A partir do painel de instrumentos do cofre, pode selecionar uma política de backup.

   * Para mudar de política, selecione uma política diferente e, em seguida, **selecione Guardar**. A nova política é imediatamente aplicada ao cofre.

     ![Escolher uma política de cópias de segurança](./media/backup-azure-manage-vms/backup-policy-create-new.png)

## <a name="run-an-on-demand-backup"></a>Faça uma cópia de segurança a pedido

Pode executar uma cópia de segurança a pedido de um VM depois de configurar a sua proteção. Tenha estes detalhes em mente:

* Se a cópia de segurança inicial estiver pendente, a cópia de segurança a pedido cria uma cópia completa do VM no cofre dos Serviços de Recuperação.
* Se a cópia de segurança inicial estiver completa, uma cópia de segurança a pedido só enviará alterações da fotografia anterior para o cofre dos Serviços de Recuperação. Ou seja, cópias de segurança posteriores são sempre incrementais.
* O intervalo de retenção para uma cópia de segurança a pedido é o valor de retenção que especifica quando ativa a cópia de segurança.

Para desencadear uma cópia de segurança a pedido:

1. No [painel de instrumentos](#view-vms-on-the-dashboard)do cofre , em **Item Protegido,** selecione **Artigo de Reserva**.

    ![A opção Backup agora](./media/backup-azure-manage-vms/backup-now-button.png)

2. Do **Tipo de Gestão de Cópias de Segurança**, selecione **Azure Virtual Machine**. Aparece o painel de cópia de **segurança (Azure Virtual Machine).**
3. Selecione um VM e **selecione Backup Now** para criar uma cópia de segurança a pedido. O **painel de apoio agora** aparece.
4. No **campo 'Retenção De Backup' Till,** especifique uma data para a reserva ser mantida.

    ![O calendário backup agora](./media/backup-azure-manage-vms/backup-now-check.png)

5. Selecione **OK** para executar o trabalho de reserva.

Para acompanhar o progresso do trabalho, no painel de abóbada, selecione o azulejo **backup Jobs.**

## <a name="stop-protecting-a-vm"></a>Pare de proteger um VM

Há duas maneiras de parar de proteger um VM:

* **Pare a proteção e retenha os dados de backup**. Esta opção impedirá que todos os futuros trabalhos de backup protejam o seu VM; no entanto, o serviço Azure Backup irá manter os pontos de recuperação que foram apoiados.  Terá de pagar para manter os pontos de recuperação no cofre (ver [preços de reserva do Azure](https://azure.microsoft.com/pricing/details/backup/) para mais detalhes). Poderá restaurar o VM, se necessário. Se decidir retomar a proteção VM, pode utilizar a opção *de backup do Resume.*
* **Parar a proteção e eliminar dados de cópia de segurança**. Esta opção impedirá que todos os futuros trabalhos de backup protejam o seu VM e eliminarão todos os pontos de recuperação. Não poderá restaurar o VM nem utilizar a opção *de backup do Resume.*

>[!NOTE]
>Se eliminar uma fonte de dados sem parar as cópias de segurança, novas cópias de segurança falharão. Os antigos pontos de recuperação expirarão de acordo com a apólice, mas um último ponto de recuperação será sempre mantido até parar as cópias de segurança e apagar os dados.
>

### <a name="stop-protection-and-retain-backup-data"></a>Parar a proteção e reter dados de backup

Para parar a proteção e reter dados de um VM:

1. No [painel de instrumentos do número de abóbada,](#view-vms-on-the-dashboard)selecione **Stop backup**.
2. Escolha **Reter dados de backup**e confirmar a sua seleção conforme necessário. Adicione um comentário se quiser. Se não tiver certeza do nome do artigo, sobre o ponto de exclamação para ver o nome.

    ![Reter dados de backup](./media/backup-azure-manage-vms/retain-backup-data.png)

Uma notificação permite-lhe saber que os trabalhos de reserva foram interrompidos.

### <a name="stop-protection-and-delete-backup-data"></a>Parar a proteção e eliminar dados de backup

Para parar a proteção e eliminar dados de um VM:

1. No [painel de instrumentos do número de abóbada,](#view-vms-on-the-dashboard)selecione **Stop backup**.
2. Escolha **eliminar dados de backup**e confirmar a sua seleção conforme necessário. Introduza o nome do item de cópia de segurança e adicione um comentário se quiser.

    ![Eliminar dados de cópia de segurança](./media/backup-azure-manage-vms/delete-backup-data1.png)

## <a name="resume-protection-of-a-vm"></a>Retomar a proteção de um VM

Se escolher [a proteção Stop e reter](#stop-protection-and-retain-backup-data) a opção de dados de backup durante a proteção contra o VM, poderá utilizar a cópia de segurança do **Currículo**. Esta opção não está disponível se escolher [a proteção Stop e eliminar](#stop-protection-and-delete-backup-data) a opção de dados de backup ou eliminar [dados de backup](#delete-backup-data).

Para retomar a proteção de um VM:

1. No [painel de instrumentos do número de abóbada,](#view-vms-on-the-dashboard)selecione **Retomar a cópia de segurança**.

2. Siga os passos nas [políticas de backup](#manage-backup-policy-for-a-vm) para atribuir a política para o VM. Não precisa de escolher a política inicial de proteção do VM.
3. Depois de aplicar a política de backup no VM, vê a seguinte mensagem:

    ![Mensagem indicando um VM protegido com sucesso](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Eliminar dados de cópia de segurança

Existem duas formas de eliminar os dados de backup de um VM:

* A partir do painel de instrumentos do cofre, selecione Parar a cópia de segurança e seguir as instruções para parar a proteção e eliminar a opção [de dados de cópia de segurança.](#stop-protection-and-delete-backup-data)

  ![Selecione Parar backup](./media/backup-azure-manage-vms/stop-backup-buttom.png)

* A partir do painel de instrumentos do cofre, selecione Eliminar dados de backup. Esta opção está ativada se tiver optado por parar a proteção e reter a opção [de dados de backup](#stop-protection-and-retain-backup-data) durante a proteção contra o VM de paragem

  ![Selecione Eliminar backup](./media/backup-azure-manage-vms/delete-backup-buttom.png)

  * No [painel de instrumentos](#view-vms-on-the-dashboard)do cofre, selecione **Eliminar os dados de cópia de segurança**.
  * Digite o nome do item de cópia de segurança para confirmar que pretende eliminar os pontos de recuperação.

    ![Eliminar dados de cópia de segurança](./media/backup-azure-manage-vms/delete-backup-data1.png)

  * Para eliminar os dados de cópia de segurança do item, selecione **Eliminar**. Uma mensagem de notificação permite-lhe saber que os dados de cópia de segurança foram eliminados.

Para proteger os seus dados, o Azure Backup inclui a função de exclusão suave. Com a eliminação suave, mesmo depois de eliminada a cópia de segurança (todos os pontos de recuperação) de um VM, os dados de backup são conservados por mais 14 dias. Para obter mais informações, consulte [a documentação de eliminação suave.](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud)

  > [!NOTE]
  > Quando elimina os dados de cópia de segurança, elimina todos os pontos de recuperação associados. Não é possível escolher pontos de recuperação específicos para eliminar.

### <a name="backup-item-where-primary-data-source-no-longer-exists"></a>Item de backup onde a fonte de dados primários já não existe

* Se os VMs Azure configurados para a cópia de segurança do Azure forem eliminados ou movidos sem proteção, então tanto os trabalhos de backup programados como a pedido (ad-hoc) trabalharão com o erro UserErrorVmNotFoundV2. O pré-controlo de backup só será considerado crítico para os trabalhos de backup a pedido falhados (não são apresentados trabalhos programados falhados).
* Estes itens de backup permanecem ativos no sistema aderente à política de backup e retenção definida pelo utilizador. Os dados de back-up para estes VMs Azure serão mantidos de acordo com a política de retenção. Os pontos de recuperação expirados (exceto o último ponto de recuperação) são limpos de acordo com o intervalo de retenção definido na política de backup.
* Recomenda-se aos utilizadores que apaguem os itens de backup em que a fonte de dados primário já não exista para evitar qualquer custo adicional, se o item/dados de cópia de segurança dos recursos de eliminação deixar de ser necessário, uma vez que o último ponto de recuperação é mantido para sempre e o utilizador é cobrado de acordo com o preço de backup aplicável.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [apoiar os VMs Azure a partir das definições do VM](backup-azure-vms-first-look-arm.md).
* Saiba como restaurar os [VMs.](backup-azure-arm-restore-vms.md)
* Saiba como monitorizar as [cópias de segurança do Azure VM](backup-azure-monitor-vms.md).
