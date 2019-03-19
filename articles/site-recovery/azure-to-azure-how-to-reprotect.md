---
title: Voltar a proteger efetuar a ativação pós-falha de VMs do Azure para a região primária do Azure com o Azure Site Recovery | Documentos da Microsoft
description: Descreve como voltar a proteger as VMs do Azure numa região secundária, após a ativação pós-falha de uma região primária, com o Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: b1676f53125694eeff3a39adf51dc854c197d756
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58110499"
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>Voltar a proteger efetuar a ativação pós-falha de VMs do Azure para a região primária


Quando [efetuar a ativação pós-falha](site-recovery-failover.md) VMs do Azure de uma região para outro e usando [do Azure Site Recovery](site-recovery-overview.md), o arranque de VMs de cópia de segurança na região secundária, num estado desprotegido. Se a reativação pós-falha de VMs para a região primária, tem de fazer o seguinte:

- Voltar a proteger as VMs na região secundária, para que comecem a replicar para a região primária.
- Após a conclusão de nova proteção e as VMs estão a replicar, pode realizar a ativação-los ao longo do secundário para a região primária.

> [!WARNING]
> Se tiver [migrados](migrate-overview.md#what-do-we-mean-by-migration) máquinas dos principais para a região secundária, movida a VM para outro grupo de recursos ou eliminado a VM do Azure, não é possível voltar a proteger a VM ou realizar a ativação pós-falha.


## <a name="prerequisites"></a>Pré-requisitos
1. A ativação pós-falha da VM dos principais para a região secundária tem de ser consolidada.
2. O site de destino principal deve estar disponível e deverá conseguir aceder ou criar recursos nessa região.

## <a name="reprotect-a-vm"></a>Voltar a proteger uma VM

1. Na **cofre** > **itens replicados**, com o botão direito a ativação pós-falha VM e selecione **voltar a proteger**. A direção de nova proteção deve mostrar a partir do secundário para o primário.

   ![Voltar a proteger](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

2. Reveja os conjuntos de disponibilidade, armazenamento, rede e grupo de recursos. Em seguida, clique em **OK**. Se existirem quaisquer recursos marcados como novo, eles são criados como parte do processo de nova proteção.
3. A tarefa de nova proteção realiza o seeding do site de destino com os dados mais recentes. Depois que terminar, a replicação de diferenças ocorre. Em seguida, pode efetuar a ativação pós-falha para o site primário. Pode selecionar a conta de armazenamento ou a rede que pretende utilizar durante a voltar a proteger, utilizando a opção de personalizar.

   ![Opção de personalizar](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

### <a name="customize-reprotect-settings"></a>Personalizar as definições de voltar a proteger

Pode personalizar as seguintes propriedades do destino VMe durante a nova proteção.

![Personalizar](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Propriedade |Notas  |
|---------|---------|
|Grupo de recursos de destino     | Modifique o grupo de recursos de destino no qual a VM é criada. Como a parte da nova proteção, a VM de destino é eliminado. Pode escolher um novo grupo de recursos no qual pretende criar a VM após a ativação pós-falha.        |
|Rede virtual de destino     | A rede de destino não pode ser alterada durante a tarefa de reproteção. Para alterar a rede, Refazer o mapeamento de rede.         |
|Armazenamento de destino (VM secundária não utilizam discos geridos)     | Pode alterar a conta de armazenamento que a VM utiliza após a ativação pós-falha.         |
|(A VM secundária utiliza discos geridos) de discos geridos de réplica    | Site Recovery cria discos geridos de réplica na região primária para espelhar os discos geridos da VM secundário.         |
|Armazenamento de cache     | Pode especificar uma conta de armazenamento de cache a utilizar durante a replicação. Por predefinição, é possível criar uma nova conta de armazenamento de cache, se não existir.         |
|Conjunto de Disponibilidade     |Se a VM na região secundária fizer parte de um conjunto de disponibilidade, pode escolher um conjunto de disponibilidade para a VM de destino na região primária. Por predefinição, o Site Recovery tenta localizar o conjunto na região primária de disponibilidade existente e utilizá-lo. Durante a personalização, pode especificar um novo conjunto de disponibilidade.         |


### <a name="what-happens-during-reprotection"></a>O que acontece durante a nova proteção?

Por predefinição, ocorre o seguinte:

1. É criada uma conta de armazenamento de cache na região onde a VM ativação pós-falha está em execução.
2. Se a conta de armazenamento de destino (a conta de armazenamento original na região primária) não existir, é criado um novo. O nome da conta de armazenamento atribuída é o nome da conta de armazenamento utilizado pela VM secundária, o sufixo com "asr".
3. Se a sua VM utilizar discos geridos, geridos de réplica são criados discos na região primária para armazenar os dados replicados a partir de discos da VM secundário.
4. Se o conjunto de disponibilidade de destino não existir, uma nova é criada como parte do trabalho de voltar a proteger, se necessário. Se tiver personalizado as definições da nova proteção, em seguida, o conjunto selecionado é utilizado.

Quando acionar uma tarefa de reproteção e o destino que VM existe, ocorre o seguinte:

1. Lado do destino que VM está desativada se ele é executado.
2. Se a VM estiver a utilizar discos geridos, uma cópia de discos originais são criados com "-ASRReplica' sufixo. Os discos originais são eliminados. O "-ASRReplica' cópias são utilizadas para replicação.
3. Se a VM estiver a utilizar discos não geridos, os discos de dados da VM de destino são desligados e utilizados para replicação. Uma cópia do disco do SO é criada e anexada na VM. O disco do SO original é desligado e é utilizado na replicação.
4. Apenas as alterações entre o disco de origem e o disco de destino são sincronizadas. Os diferenciais são calculados comparando a ambos os discos e, em seguida, transferidas. Esta ação irá demorar algumas horas a concluir.
5. Depois de concluída a sincronização, a replicação delta começa e cria um ponto de recuperação em conformidade com a política de replicação.

Quando aciona uma tarefa de reproteção e discos VM de destino e não existem, ocorre o seguinte:
1. Se a VM estiver a utilizar discos geridos, os discos de réplica são criados com "-ASRReplica' sufixo. O "-ASRReplica' cópias são utilizadas para replicação.
2. Se a VM estiver a utilizar discos não geridos, os discos de réplica são criados na conta de armazenamento de destino.
3. Os discos todos são copiados a partir ao longo de região para a nova região de destino.
4. Depois de concluída a sincronização, a replicação delta começa e cria um ponto de recuperação em conformidade com a política de replicação.

## <a name="next-steps"></a>Passos Seguintes

Depois da VM está protegida, pode iniciar uma ativação pós-falha. A ativação pós-falha encerra a VM na região secundária e cria e inicializa a VM na região primária, com um pequeno período de indisponibilidade. Recomendamos que escolha um período de tempo em conformidade, e que execute uma ativação pós-falha de teste antes de iniciar uma ativação pós-falha completa para o site primário. [Saiba mais](site-recovery-failover.md) sobre a ativação pós-falha.
