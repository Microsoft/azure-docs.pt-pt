---
title: Reproteja os VMs azure para a região primária com recuperação do sítio azure  Microsoft Docs
description: Descreve como reproteger os VMs Azure após a falha, a região secundária para a região primária, utilizando a Recuperação do Sítio Azure.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 818c053c22cfa47cac0f4f6a19349cf239d3cdec
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79258125"
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>Reproteger falhou sobre os VMs azure para a região primária

Quando [falha](site-recovery-failover.md) os VMs Azure de uma região para outra usando a Recuperação do [Sítio Azure](site-recovery-overview.md), os VMs arrancam na região secundária, num estado **desprotegido.** Se quiser repor os VMs na região primária, faça as seguintes tarefas:

1. Reproteja os VMs na região secundária, para que comecem a replicar-se na região primária.
1. Após a reproteção completa e os VMs estão se replicando, você pode falhar da região secundária para a região primária.

## <a name="prerequisites"></a>Pré-requisitos

- A falha do VM da região primária para a região secundária tem de ser comprometida.
- O local principal do alvo deve estar disponível, e você deve ser capaz de aceder ou criar recursos nessa região.

## <a name="reprotect-a-vm"></a>Reproteger um VM

1. No **Cofre** > **itens replicados,** clique à direita no VM falhado e selecione **Re-Protect**. A direção de reprotecção deve ser indicada do secundário ao primário.

   ![Reproteger](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

1. Reveja os conjuntos de recursos, rede, armazenamento e disponibilidade. Em seguida, clique em **OK**. Se houver recursos marcados como novos, são criados como parte do processo de reproteção.
1. O trabalho de reproteção sede o local alvo com os dados mais recentes. Depois do trabalho terminar, a replicação delta ocorre. Depois, podes voltar para o local principal. Pode selecionar a conta de armazenamento ou a rede que pretende utilizar durante a reproteção, utilizando a opção personalizar.

   ![Personalizar opção](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

### <a name="customize-reprotect-settings"></a>Personalizar definições de reproteção

Pode personalizar as seguintes propriedades do VM alvo durante a reproteção.

![Personalizar](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Propriedade |Notas  |
|---------|---------|
|Grupo de recursos-alvo | Modificar o grupo de recursos-alvo no qual o VM é criado. Como parte da reprotecção, o VM alvo é eliminado. Você pode escolher um novo grupo de recursos sob o qual criar o VM após a falha. |
|Rede virtual alvo | A rede de alvos não pode ser alterada durante o trabalho de reprotecção. Para alterar a rede, refaça o mapeamento da rede. |
|Armazenamento de alvo (VM secundário não utiliza discos geridos) | Pode alterar a conta de armazenamento que o VM utiliza após a falha. |
|Réplica de discos geridos (VM secundário utiliza discos geridos) | A Recuperação do Site cria discos geridos réplicas na região primária para espelhar os discos geridos pela VM secundária. |
|Armazenamento em cache | Pode especificar uma conta de armazenamento de cache a utilizar durante a replicação. Por padrão, é criada uma nova conta de armazenamento de cache, se não existir. |
|Conjunto de disponibilidade | Se o VM na região secundária fizer parte de um conjunto de disponibilidade, pode escolher um conjunto de disponibilidade para o VM-alvo na região primária. Por padrão, a Recuperação do Site tenta encontrar a disponibilidade existente definida na região primária, e usá-la. Durante a personalização, pode especificar um novo conjunto de disponibilidade. |

### <a name="what-happens-during-reprotection"></a>O que acontece durante a reproteção?

Por predefinição, ocorre o seguinte:

1. É criada uma conta de armazenamento em cache na região onde o falhado sobre a VM está em funcionamento.
1. Se a conta de armazenamento alvo (a conta de armazenamento original na região primária) não existir, uma nova é criada. O nome da conta de armazenamento atribuído é o nome da conta de armazenamento utilizada pelo VM secundário, sufixo com `asr`.
1. Se o seu VM utilizar discos geridos, os discos geridos por réplica são criados na região primária para armazenar os dados replicados a partir dos discos secundários do VM.
1. Se o conjunto de disponibilidade de destino não existir, um novo é criado como parte do trabalho de reproteção, se necessário. Se personalizar as definições de reproteção, então o conjunto selecionado é utilizado.

Quando você desencadeia um trabalho de reproteção, e o VM alvo existe, ocorre o seguinte:

1. O lado alvo VM está desligado se estiver a funcionar.
1. Se o VM estiver a utilizar discos geridos, é criada uma cópia do disco original com um sufixo `-ASRReplica`. Os discos originais são apagados. As cópias `-ASRReplica` são usadas para replicação.
1. Se o VM estiver a utilizar discos não geridos, os discos de dados do Target VM são separados e utilizados para a replicação. É criada e anexada uma cópia do disco OS no VM. O disco osso original é separado e utilizado para a replicação.
1. Apenas as alterações entre o disco de origem e o disco-alvo são sincronizadas. Os diferenciais são calculados comparando ambos os discos e depois transferidos. Verifique abaixo para encontrar o tempo estimado para completar a reproteção.
1. Após a sincronização, a replicação delta começa, e um ponto de recuperação é criado de acordo com a política de replicação.

Quando você desencadeia um trabalho de reproteção, e o vm alvo e os discos não existem, ocorre o seguinte:

1. Se o VM estiver a utilizar discos geridos, os discos de réplica são criados com `-ASRReplica` sufixo. As cópias `-ASRReplica` são usadas para replicação.
1. Se o VM estiver a utilizar discos não geridos, os discos de réplica são criados na conta de armazenamento do alvo.
1. Todos os discos são copiados da região fracassada para a nova região-alvo.
1. Após a sincronização, a replicação delta começa, e um ponto de recuperação é criado de acordo com a política de replicação.

#### <a name="estimated-time-to-do-the-reprotection"></a>Tempo estimado para fazer a reproteção

Na maioria dos casos, a Recuperação do Site Azure não replica os dados completos para a região de origem.
As seguintes condições determinam quanto é que os dados são replicados:

1. Se os dados vm de origem forem eliminados, corrompidos ou inacessíveis por alguma razão, como uma alteração/eliminação do grupo de recursos, então durante a reproteção uma replicação inicial completa irá acontecer porque não há dados disponíveis na região de origem para usar.
1. Se os dados vm de origem estiverem acessíveis, apenas os diferenciais são calculados comparando ambos os discos e depois transferidos. Verifique a tabela abaixo para obter o tempo estimado.

|Situação de exemplo | Tempo tomado para reproteger |
|---|---|
|A região de origem tem 1 VM com 1 disco padrão DE TB.<br/>Apenas são utilizados dados de 127 GB e o resto do disco está vazio.<br/>O tipo de disco é padrão com 60 mib/s de entrada.<br/>Nenhuma alteração de dados após a falha.| Tempo aproximado: 45 minutos - 1,5 horas.<br/>Durante a reproteção, a Recuperação do Local preencherá a verificação de todos os dados que demorarão 127 GB/ 45 MBs, aproximadamente 45 minutos.<br/>É necessário algum tempo para a recuperação do local à escala automática, aproximadamente 20-30 minutos.<br/>Sem acusações de Egress. |
|A região de origem tem 1 VM com 1 disco padrão DE TB.<br/>Apenas são utilizados dados de 127 GB e o resto do disco está vazio.<br/>O tipo de disco é padrão com 60 mib/s de entrada.<br/>45 GB de dados muda após a falha.| Tempo aproximado: 1 hora a 2 horas.<br/>Durante a reproteção, a Recuperação do Local preencherá a verificação de todos os dados que demorarão 127 GB/ 45 MBs, aproximadamente 45 minutos.<br/>Tempo de transferência para aplicar alterações de 45 GB que são de 45 GB/ 45 MBps, aproximadamente 17 minutos.<br/>Os encargos com a Egress seriam para alterações de dados de 45 GB, não para a verificação. |

## <a name="next-steps"></a>Passos seguintes

Depois de o VM estar protegido, pode iniciar uma falha. A falha encerra o VM na região secundária e cria e arranca o VM na região primária, com breves tempos de paragem durante este processo. Recomendamos que escolha um momento adequado para este processo e que faça uma falha no teste antes de dar início a uma falha completa no local principal. [Saiba mais](site-recovery-failover.md) sobre o fracasso da Recuperação do Site Azure.
