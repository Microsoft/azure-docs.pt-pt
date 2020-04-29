---
title: Gerir e monitorizar backups Azure VM
description: Aprenda a gerir e monitorizar as cópias de segurança Do Azure VM utilizando o serviço de backup Azure.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: dd4e9dc199048b3faf3da0cadfdf60bdcb26c5bc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79248154"
---
# <a name="manage-azure-vm-backups-with-azure-backup-service"></a>Gerir backups Azure VM com serviço de backup Azure

Este artigo descreve como gerir as máquinas virtuais Azure (VMs) que são apoiadas utilizando o [serviço de backup Azure](backup-overview.md). O artigo também resume as informações de reserva que pode encontrar no painel de instrumentos do cofre.

No portal Azure, o painel de abóbadas dos Serviços de Recuperação fornece acesso a informações sobre cofres, incluindo:

* O mais recente reforço, que é também o mais recente ponto de restauro.
* A política de reserva.
* O tamanho total de todas as fotos de reserva.
* O número de VMs que estão habilitados para cópias de segurança.

Pode gerir backups utilizando o painel de instrumentos e perfurando para VMs individuais. Para começar as cópias de segurança da máquina, abra o cofre no tablier.

![Vista completa do painel com slider](./media/backup-azure-manage-vms/bottom-slider.png)

## <a name="view-vms-on-the-dashboard"></a>Ver VMs no painel de instrumentos

Para ver VMs no painel do cofre:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. No menu Hub, **selecione Browse**. Na lista de recursos, escreva **Serviços de Recuperação**. À medida que escreve, a lista é filtrada com base na sua entrada. Selecione **cofres de Serviços**de Recuperação .

    ![Criar um cofre dos Serviços de Recuperação ](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

3. Para facilitar a utilização, clique no cofre e selecione **Pin para painel de instrumentos**.
4. Abra o painel do cofre.

    ![Abra o painel do painel do cofre e as definições](./media/backup-azure-manage-vms/full-view-rs-vault.png)

5. No azulejo **de backup itens,** selecione **Máquinas Virtuais Azure**.

    ![Abra o azulejo de backup itens](./media/backup-azure-manage-vms/contoso-vault-1606.png)

6. No painel **de itens de backup,** pode ver a lista de VMs protegidos. Neste exemplo, o cofre protege uma máquina virtual: demobackup.  

    ![Ver o painel de itens de backup](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

7. A partir do painel de instrumentos do item do cofre, modificar as políticas de backup, executar uma cópia de segurança a pedido, parar ou retomar a proteção dos VMs, eliminar dados de backup, ver pontos de restauro e executar um restauro.

    ![O painel de itens de backup e o painel de definições](./media/backup-azure-manage-vms/item-dashboard-settings.png)

## <a name="manage-backup-policy-for-a-vm"></a>Gerir a política de backup para um VM

Para gerir uma política de backup:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/). Abra o painel do cofre.
2. No azulejo **de backup itens,** selecione **Máquinas Virtuais Azure**.

    ![Abra o azulejo de backup itens](./media/backup-azure-manage-vms/contoso-vault-1606.png)

3. No painel de itens de backup, pode ver a lista de VMs protegidos e o último estado de backup com o tempo de **pontos** de restauro mais recentes.

    ![Ver o painel de itens de backup](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

4. A partir do painel do objeto do cofre, pode selecionar uma política de backup.

   * Para mudar de política, selecione uma política diferente e, em seguida, selecione **Guardar**. A nova política é imediatamente aplicada ao cofre.

     ![Escolher uma política de cópias de segurança](./media/backup-azure-manage-vms/backup-policy-create-new.png)

## <a name="run-an-on-demand-backup"></a>Faça um backup a pedido

Pode fazer um backup a pedido de um VM depois de configurar a sua proteção. Tenha em mente estes detalhes:

* Se o backup inicial estiver pendente, a cópia de segurança a pedido cria uma cópia completa do VM no cofre dos Serviços de Recuperação.
* Se a cópia de segurança inicial estiver completa, uma cópia de segurança a pedido apenas enviará alterações do instantâneo anterior para o cofre dos Serviços de Recuperação. Ou seja, os reforços posteriores são sempre incrementais.
* O intervalo de retenção para uma cópia de segurança a pedido é o valor de retenção que especifica quando aciona a cópia de segurança.

Para desencadear um reforço a pedido:

1. No painel de instrumentos do [cofre,](#view-vms-on-the-dashboard)em **item protegido,** selecione **'Backup Item**' .

    ![A opção Backup agora](./media/backup-azure-manage-vms/backup-now-button.png)

2. Do Tipo de **Gestão de Backup,** selecione **Máquina Virtual Azure**. Aparece o painel **de backup (Máquina Virtual Azure).**
3. Selecione um VM e selecione **Backup Agora** para criar uma cópia de segurança a pedido. O painel **de backup agora** aparece.
4. No campo **'Reter Cópia de Segurança',** especifique uma data para a reserva a manter.

    ![O calendário backup agora](./media/backup-azure-manage-vms/backup-now-check.png)

5. Selecione **OK** para executar o trabalho de reserva.

Para acompanhar o progresso do trabalho, no painel do cofre, selecione o azulejo **backup Jobs.**

## <a name="stop-protecting-a-vm"></a>Pare de proteger um VM

Há duas maneiras de parar de proteger um VM:

* **Pare a proteção e guarde os dados de backup**. Esta opção impedirá que todos os futuros trabalhos de backup protejam o seu VM; no entanto, o serviço de backup Azure manterá os pontos de recuperação que foram apoiados.  Terá de pagar para manter os pontos de recuperação no cofre (ver preços de [backup azure](https://azure.microsoft.com/pricing/details/backup/) para mais detalhes). Será capaz de restaurar o VM, se necessário. Se decidir retomar a proteção VM, pode utilizar a opção *de backup Do Currículo.*
* **Pare a proteção e elimine os dados de backup**. Esta opção impedirá que todos os futuros trabalhos de backup protejam o seu VM e apaguem todos os pontos de recuperação. Não poderá restaurar o VM nem utilizar a opção *de reserva Resume.*

>[!NOTE]
>Se eliminar uma fonte de dados sem parar cópias de segurança, novas cópias de segurança falharão. Os antigos pontos de recuperação expirarão de acordo com a apólice, mas um último ponto de recuperação será mantido sempre até parar as cópias de segurança e apagar os dados.
>

### <a name="stop-protection-and-retain-backup-data"></a>Parar a proteção e reter dados de backup

Para parar a proteção e reter dados de um VM:

1. No [painel do objeto do cofre,](#view-vms-on-the-dashboard)selecione **parar de reserva**.
2. Escolha **reter dados**de backup e confirme a sua seleção conforme necessário. Adicione um comentário se quiser. Se não tem certeza do nome do artigo, paire sobre o ponto de exclamação para ver o nome.

    ![Reter dados de backup](./media/backup-azure-manage-vms/retain-backup-data.png)

Uma notificação permite-lhe saber que os trabalhos de reserva foram interrompidos.

### <a name="stop-protection-and-delete-backup-data"></a>Parar a proteção e eliminar dados de backup

Para parar a proteção e eliminar dados de um VM:

1. No [painel do objeto do cofre,](#view-vms-on-the-dashboard)selecione **parar de reserva**.
2. Escolha **Eliminar dados**de backup e confirme a sua seleção conforme necessário. Introduza o nome do item de reserva e adicione um comentário se quiser.

    ![Eliminar dados de cópia de segurança](./media/backup-azure-manage-vms/delete-backup-data1.png)

## <a name="resume-protection-of-a-vm"></a>Retomar a proteção de um VM

Se escolheu a [proteção stop e retém a](#stop-protection-and-retain-backup-data) opção de dados de backup durante a proteção stop VM, então pode utilizar a **cópia de segurança do Resume**. Esta opção não está disponível se escolher a [proteção Stop e eliminar](#stop-protection-and-delete-backup-data) a opção de dados de backup ou [eliminar dados](#delete-backup-data)de backup .

Para retomar a proteção de um VM:

1. No [painel do objeto do cofre,](#view-vms-on-the-dashboard)selecione **backup de currículo**.

2. Siga os passos em [Gerir as políticas](#manage-backup-policy-for-a-vm) de backup para atribuir a política para o VM. Não precisas de escolher a política inicial de proteção do VM.
3. Depois de aplicar a política de backup ao VM, consulte a seguinte mensagem:

    ![Mensagem indicando um VM protegido com sucesso](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Eliminar dados de cópia de segurança

Existem duas formas de eliminar os dados de backup de um VM:

* A partir do painel de instrumentos de objeto do cofre, selecione parar a cópia de segurança e siga as instruções para [a proteção stop e elimine](#stop-protection-and-delete-backup-data) a opção de dados de backup.

  ![Selecione parar a cópia de segurança](./media/backup-azure-manage-vms/stop-backup-buttom.png)

* A partir do painel de instrumentos de objeto do cofre, selecione Eliminar dados de backup. Esta opção está ativada se tiver optado por parar a [proteção e reter](#stop-protection-and-retain-backup-data) a opção de dados de backup durante a proteção stop VM

  ![Selecione eliminar a cópia de segurança](./media/backup-azure-manage-vms/delete-backup-buttom.png)

  * No painel de instrumentos de [objeto do cofre,](#view-vms-on-the-dashboard)selecione **Eliminar dados**de backup .
  * Digite o nome do item de reserva para confirmar que pretende eliminar os pontos de recuperação.

    ![Eliminar dados de cópia de segurança](./media/backup-azure-manage-vms/delete-backup-data1.png)

  * Para eliminar os dados de cópia de segurança do artigo, **selecione Eliminar**. Uma mensagem de notificação permite-lhe saber que os dados de backup foram eliminados.

Para proteger os seus dados, o Azure Backup inclui a funcionalidade de eliminação suave. Com a eliminação suave, mesmo após a cópia de segurança (todos os pontos de recuperação) de um VM ser eliminada, os dados de backup são conservados por 14 dias adicionais. Para mais informações, consulte [a documentação de eliminação suave](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud).

  > [!NOTE]
  > Ao eliminar os dados de backup, elimina todos os pontos de recuperação associados. Não é possível escolher pontos de recuperação específicos para eliminar.

### <a name="backup-item-where-primary-data-source-no-longer-exists"></a>Artigo de backup onde a fonte de dados primários já não existe

* Se os VMs Azure configurados para cópia de segurança Azure forem eliminados ou movidos sem proteção de paragem, então ambos os trabalhos de backup programados e a pedido (ad-hoc) de reserva falharão com o erro UserErrorVmNotFoundV2. O pré-controlo de reserva só será crítico para trabalhos de backup a pedido falhados (não são apresentados empregos programados falhados).
* Estes itens de backup permanecem ativos no sistema aderindo à política de backup e retenção definida pelo utilizador. Os dados com reserva destes VMs Azure serão conservados de acordo com a política de retenção. Os pontos de recuperação expirados (exceto o último ponto de recuperação) são limpos de acordo com o intervalo de retenção estabelecido na política de backup.
* Recomenda-se aos utilizadores que apaguem os itens de backup onde a fonte de dados primária já não exista para evitar qualquer custo adicional, se o item/dados de cópia de segurança para os recursos de eliminação já não for necessário, uma vez que o último ponto de recuperação é mantido para sempre e o utilizador é cobrado de acordo com os preços de backup aplicáveis.

## <a name="next-steps"></a>Passos seguintes

* Aprenda a apoiar os [VMs Azure a partir das definições do VM](backup-azure-vms-first-look-arm.md).
* Aprenda a [restaurar os VMs.](backup-azure-arm-restore-vms.md)
* Saiba como [monitorizar as cópias de segurança do Azure VM](backup-azure-monitor-vms.md).
