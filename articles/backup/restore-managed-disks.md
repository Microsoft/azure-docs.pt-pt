---
title: Restaurar Discos Geridos Azure
description: Saiba como restaurar os Discos Geridos Azure a partir do portal Azure.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 043a10a7359c95529ff1c4dcc181ea4aba75cb5f
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/18/2021
ms.locfileid: "98558380"
---
# <a name="restore-azure-managed-disks-in-preview"></a>Restaurar Discos Geridos Azure (na pré-visualização)

>[!IMPORTANT]
>A Azure Disk Backup está em pré-visualização sem um acordo de nível de serviço, e não é recomendado para cargas de trabalho de produção. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Para disponibilidade da região, consulte a [matriz de suporte.](disk-backup-support-matrix.md)
>
>[Preencha este formulário](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) para se inscrever para a pré-visualização.

Este artigo explica como restaurar os [Discos Geridos Azure](https://docs.microsoft.com/azure/virtual-machines/managed-disks-overview) a partir de um ponto de restauração criado pela Azure Backup.

Atualmente, a opção de recuperação de Original-Location (OLR) de restauro substituindo o disco de origem existente de onde as cópias de segurança foram tomadas não é suportada. Pode restaurar a partir de um ponto de recuperação para criar um novo disco no mesmo grupo de recursos que o do disco de origem de onde as cópias de segurança foram tomadas ou em qualquer outro grupo de recursos. Isto é conhecido como Alternate-Location Recovery (ALR) e isto ajuda a manter tanto o disco de origem como o disco restaurado (novo).

Neste artigo, vai aprender a:

- Restaurar para criar um novo disco

- Acompanhe o estado da operação de restauro

## <a name="restore-to-create-a-new-disk"></a>Restaurar para criar um novo disco

Backup Vault usa Identidade Gerida para aceder a outros recursos Azure. Para restaurar a partir de cópia de segurança, a identidade gerida do cofre de backup requer um conjunto de permissões no grupo de recursos onde o disco deve ser restaurado.

O cofre de backup utiliza um sistema atribuído à identidade gerida, que é restrito a um por recurso e está ligado ao ciclo de vida deste recurso. Pode conceder permissões à identidade gerida utilizando o controlo de acesso baseado em funções Azure (Azure RBAC). A identidade gerida é um principal de serviço de um tipo especial que só pode ser usado com recursos Azure. Saiba mais sobre [identidades geridas.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

São necessários os seguintes pré-requisitos para a realização de uma operação de restauro:

1. Atribua a função **do Operador de Restauro** do Disco à identidade gerida do Cofre de Cópia de Segurança no grupo de Recursos onde o disco será restaurado pelo serviço de backup Azure.

    >[!NOTE]
    > Pode escolher o mesmo grupo de recursos que o do disco de origem de onde são tomadas cópias de segurança ou para qualquer outro grupo de recursos dentro da mesma ou uma subscrição diferente.

    1. Vá ao grupo de recursos onde o disco deve ser restaurado. Por exemplo, o grupo de recursos é *TargetRG*.

    1. Ir ao **controlo de acesso (IAM)** e selecionar **atribuições de funções adicionar**

    1. No painel de contexto certo, selecione **O Operador de Restauro do Disco** na lista de dropdown **role.** Selecione a identidade gerida do cofre de reserva e **guarde**.

        >[!TIP]
        >Digite o nome do cofre de reserva para selecionar a identidade gerida do cofre.

        ![Selecione a função de operador de restauro de disco](./media/restore-managed-disks/disk-restore-operator-role.png)

1. Verifique se a identidade gerida do cofre de reserva tem o conjunto certo de atribuições de funções no grupo de recursos onde o disco será restaurado.

    1. Vá para **o cofre de backup - > Identidade** e selecione **atribuições de funções Azure**

        ![Selecione atribuições de funções Azure](./media/restore-managed-disks/azure-role-assignments.png)

    1. Verifique se a função, o nome do recurso e o tipo de recurso aparecem corretamente.

        ![Verifique função, nome de recurso e tipo de recurso](./media/restore-managed-disks/verify-role.png)

    >[!NOTE]
    >Embora as atribuições de funções sejam refletidas corretamente no portal, pode levar aproximadamente 15 minutos para que a permissão seja aplicada na identidade gerida do cofre de reserva.
    >
    >Durante as cópias de segurança programadas ou uma operação de backup a pedido, o Azure Backup armazena as imagens incrementais do disco no Grupo de Recursos Instantâneos fornecidas durante a configuração da cópia de segurança do disco. A Azure Backup utiliza estas imagens incrementais durante a operação de restauro. Se as imagens forem eliminadas ou retiradas do Grupo de Recursos Instantâneos ou se as atribuições de funções de abóbada de cópias de segurança forem revogadas no Grupo de Recursos Instantâneos, a operação de restauro falhará.

Assim que os pré-requisitos forem cumpridos, siga estes passos para executar a operação de restauro.

1. No [portal Azure,](https://portal.azure.com/)vá ao **Centro de Backup**. Selecione **as instâncias de backup** na secção **Gerir.** A partir da lista de casos de backup, selecione a instância de backup do disco para a qual deseja executar a operação de restauro.

    ![Lista de casos de backup](./media/restore-managed-disks/backup-instances.png)

    Alternadamente, pode executar esta operação a partir do cofre de reserva que usou para configurar a cópia de segurança para o disco.

1. No ecrã **de instância de cópia de segurança,** selecione o ponto de restauro que pretende utilizar para executar a operação de restauro e selecione **Restaurar**.

    ![Selecione ponto de restauro](./media/restore-managed-disks/select-restore-point.png)

1. No fluxo de trabalho **Restaurar,** rever as informações **básicas** e selecionar o separador ponto de **recuperação** e selecionar **Seguinte: Restaurar os parâmetros**.

    ![Rever bases e selecionar informações sobre pontos de recuperação](./media/restore-managed-disks/review-information.png)

1. No **separador parâmetros 'Restaurar',** selecione o grupo de recursos **target** e **target** onde pretende restaurar a cópia de segurança. Forneça o nome do disco a ser restaurado. Selecione **Seguinte: Revisão + restauro**.

    ![Restaurar parâmetros](./media/restore-managed-disks/restore-parameters.png)

    >[!TIP]
    >Os discos que estão a ser apoiados pela Azure Backup utilizando a solução de Backup do Disco também podem ser apoiados pela Azure Backup utilizando a solução de backup Azure VM com o cofre dos Serviços de Recuperação. Se tiver uma proteção configurada do VM Azure ao qual este disco está ligado, também pode utilizar a operação de restauro Azure VM. Pode optar por restaurar o VM, ou discos e ficheiros ou pastas a partir do ponto de recuperação da instância de backup correspondente do Azure VM. Para mais informações, consulte [a cópia de segurança da Azure VM](https://docs.microsoft.com/azure/backup/about-azure-vm-restore).

1. Uma vez que a validação seja bem sucedida, **selecione Restaurar** para iniciar a operação de restauro.

    ![Iniciar operação de restauro](./media/restore-managed-disks/initiate-restore.png)

    >[!NOTE]
    > A validação pode demorar alguns minutos a ser concluída antes de poder desencadear a operação de restauro. A validação pode falhar se:
    >
    > - um disco com o mesmo nome fornecido no **nome do disco restaurado** já existe no grupo de recursos **Target**
    > - a identidade gerida do cofre de backup não tem atribuições de funções válidas no **grupo de recursos Target**
    > - as atribuições de funções de identidade geridas pelo cofre de backup são revogadas no **grupo de recursos Snapshot** onde são armazenados instantâneos incrementais
    > - Se os instantâneos incrementais forem eliminados ou deslocados do grupo de recursos instantâneos

A Restauração criará um novo disco a partir do ponto de recuperação selecionado no grupo de recursos-alvo que foi fornecido durante a operação de restauro. Para utilizar o disco restaurado numa máquina virtual existente, terá de realizar mais passos:

- Se o disco restaurado for um disco de dados, pode anexar um disco existente a uma máquina virtual. Se o disco restaurado for o disco OS, pode trocar o disco OS de uma máquina virtual a partir do portal Azure sob o painel **de máquina virtual** - > menu **Discos** na secção **Definições.**

    ![Trocar discos de SISTEMA](./media/restore-managed-disks/swap-os-disks.png)

- Para as máquinas virtuais do Windows, se o disco restaurado for um disco de dados, siga as instruções para separar o disco de [dados original](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk#detach-a-data-disk-using-the-portal) da máquina virtual. Em [seguida, prenda o disco restaurado](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) à máquina virtual. Siga as instruções para [trocar o disco DE](https://docs.microsoft.com/azure/virtual-machines/windows/os-disk-swap) da máquina virtual pelo disco restaurado.

- Para as máquinas virtuais Linux, se o disco restaurado for um disco de dados, siga as instruções para separar o disco de [dados original](https://docs.microsoft.com/azure/virtual-machines/linux/detach-disk#detach-a-data-disk-using-the-portal) da máquina virtual. Em [seguida, prenda o disco restaurado](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal#attach-an-existing-disk) à máquina virtual. Siga as instruções para [trocar o disco DE](https://docs.microsoft.com/azure/virtual-machines/linux/os-disk-swap) da máquina virtual pelo disco restaurado.

Recomenda-se que revogue a atribuição de funções do **Operador de Restauro** de Disco a partir da identidade gerida do cofre de backup no grupo de recursos **Target** após a conclusão com sucesso da operação de restauro.

## <a name="track-a-restore-operation"></a>Acompanhe uma operação de restauro

Depois de ativar a operação de restauro, o serviço de backup cria um trabalho para rastrear. O Azure Backup apresenta notificações sobre o trabalho no portal. Para ver o progresso do trabalho restaurado:

1. Vá ao ecrã **de identificação do Backup.** Mostra o painel de trabalhos com operação e estado nos últimos sete dias.

    ![Painel de empregos](./media/restore-managed-disks/jobs-dashboard.png)

1. Para visualizar o estado da operação de restauro, **selecione Ver tudo** para mostrar trabalhos em curso e passados desta instância de backup.

    ![Selecione Ver tudo](./media/restore-managed-disks/view-all.png)

1. Reveja a lista de backups e restaurar os empregos e o seu estado. Selecione um trabalho na lista de empregos para ver detalhes do trabalho.

    ![Lista de empregos](./media/restore-managed-disks/list-of-jobs.png)

## <a name="next-steps"></a>Passos seguintes

- [BACKUP DE DISCO Azure FAQ](disk-backup-faq.md)
