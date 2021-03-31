---
title: Reprotect Azure VMs para a região primária com Azure Site Recovery | Microsoft Docs
description: Descreve como reprotegir os VMs Azure após o failover, o secundário para a região primária, usando a recuperação do local de Azure.
services: site-recovery
author: Rajeswari-Mamilla
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 3b9edab6e908b4506a92c78aa8f3f53277b9c17b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91360876"
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>A operação “proteger novamente” falhou nas VMs do Azure para a região primária

Quando [falha sobre](site-recovery-failover.md) os VMs Azure de uma região para outra usando a Recuperação do [Sítio azul,](site-recovery-overview.md)os VMs iniciam-se na região secundária, em estado **desprotegido.** Se quiser chumbar os VMs na região primária, faça as seguintes tarefas:

1. Reprotegir os VMs na região secundária, para que comecem a replicar-se para a região primária.
1. Após a reprotecção completa e os VMs estão a replicar-se, podes falhar da região secundária para a região primária.

## <a name="prerequisites"></a>Pré-requisitos

- O fracasso do VM da região primária para o secundário tem de ser comprometido.
- O local principal do alvo deve estar disponível, e você deve ser capaz de aceder ou criar recursos nessa região.

## <a name="reprotect-a-vm"></a>Reprotegir um VM

1. Em   >  **Itens Replicados** de Cofre, clique à direita no falhado sobre VM e selecione **Re-Protect**. A direção de reprotecção deve mostrar do secundário ao primário.

   ![O screenshot mostra uma máquina virtual com um menu contextual com re-protect selecionado.](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

1. Reveja os conjuntos de recursos, rede, armazenamento e disponibilidade. Em seguida, clique em **OK**. Se houver algum recursos marcados como novos, são criados como parte do processo de reprotecção.
1. O trabalho de reproteção semeia o local alvo com os dados mais recentes. Após o fim do trabalho, a replicação delta ocorre. Depois, podes voltar ao local principal. Pode selecionar a conta de armazenamento ou a rede que pretende utilizar durante a reproteção, utilizando a opção de personalização.

   ![Personalizar opção](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

### <a name="customize-reprotect-settings"></a>Personalize as definições de reprotector

Pode personalizar as seguintes propriedades do VM alvo durante a reproteção.

![Personalizar](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Propriedade |Notas  |
|---------|---------|
|Grupo de recursos de destino | Modificar o grupo de recursos-alvo no qual o VM é criado. Como parte da reproteção, o VM alvo é eliminado. Pode escolher um novo grupo de recursos para criar o VM após o failover. |
|Rede virtual de destino | A rede alvo não pode ser alterada durante o trabalho de reprotegido. Para alterar a rede, refaça o mapeamento da rede. |
|Armazenamento de alvos (VM secundário não utiliza discos geridos) | Pode alterar a conta de armazenamento que o VM utiliza após a falha. |
|Discos geridos por réplica (VM secundário utiliza discos geridos) | A Recuperação do Site cria discos geridos por réplicas na região primária para espelhar os discos geridos pelo VM secundário. |
|Armazenamento em cache | Pode especificar uma conta de armazenamento de cache a ser utilizada durante a replicação. Por padrão, é criada uma nova conta de armazenamento de cache, se não existir. |
|Conjunto de disponibilidade | Se o VM na região secundária fizer parte de um conjunto de disponibilidade, pode escolher um conjunto de disponibilidade definido para o VM alvo na região primária. Por predefinição, a Recuperação do Site tenta encontrar a disponibilidade existente definida na região primária e usá-la. Durante a personalização, pode especificar um novo conjunto de disponibilidade. |

### <a name="what-happens-during-reprotection"></a>O que acontece durante a reproteção?

Por padrão, ocorre o seguinte:

1. É criada uma conta de armazenamento de cache na região onde está a funcionar a falha sobre a VM.
1. Se a conta de armazenamento alvo (a conta de armazenamento original na região primária) não existir, uma nova é criada. O nome da conta de armazenamento atribuída é o nome da conta de armazenamento utilizada pelo VM secundário, sufixada com `asr` .
1. Se o seu VM utilizar discos geridos, os discos geridos por réplicas são criados na região primária para armazenar os dados replicados a partir dos discos secundários de VM.
1. Se o conjunto de disponibilidade de destino não existir, um novo é criado como parte do trabalho reprotegido, se necessário. Se tiver personalizado as definições de reproteção, o conjunto selecionado é utilizado.

Quando se ativa um trabalho de reprotegido, e o VM alvo existe, ocorre o seguinte:

1. O VM do lado alvo está desligado se estiver a funcionar.
1. Se o VM estiver a utilizar discos geridos, uma cópia do disco original é criada com um `-ASRReplica` sufixo. Os discos originais são apagados. As `-ASRReplica` cópias são usadas para replicação.
1. Se o VM estiver a utilizar discos não geridos, os discos de dados do VM-alvo são separados e utilizados para replicação. Uma cópia do disco DE é criada e anexada no VM. O disco original de SO é separado e usado para replicação.
1. Apenas as alterações entre o disco de origem e o disco-alvo são sincronizadas. Os diferenciais são calculados comparando ambos os discos e depois transferidos. Confira abaixo o tempo estimado para completar a reproteção.
1. Após a sincronização concluída, a replicação delta começa, e um ponto de recuperação é criado de acordo com a política de replicação.

Quando se dispara um trabalho de reprotegido, e o VM e os discos alvo não existem, ocorre o seguinte:

1. Se o VM estiver a utilizar discos geridos, os discos de réplica são criados com `-ASRReplica` sufixo. As `-ASRReplica` cópias são usadas para replicação.
1. Se o VM estiver a utilizar discos não geridos, os discos de réplica são criados na conta de armazenamento alvo.
1. Todos os discos são copiados da região falhada para a nova região alvo.
1. Após a sincronização concluída, a replicação delta começa, e um ponto de recuperação é criado de acordo com a política de replicação.

#### <a name="estimated-time-to-do-the-reprotection"></a>Tempo estimado para fazer a reproteção

Na maioria dos casos, a Recuperação do Sítio Azure não replica os dados completos para a região de origem.
As seguintes condições determinam a quantidade de dados replicados:

1. Se os dados VM de origem forem eliminados, corrompidos ou inacessíveis por alguma razão, como uma alteração/eliminação de grupos de recursos, então durante a reproteção, uma replicação inicial completa acontecerá porque não há dados disponíveis na região de origem para usar.
1. Se os dados VM de origem estiverem acessíveis, apenas os diferenciais são calculados comparando os discos e depois transferidos. Verifique a tabela abaixo para obter o tempo estimado.

|Situação exemplo | Tempo tomado para reprotegir |
|---|---|
|A região de origem tem 1 VM com 1 disco padrão TB.<br/>Apenas são utilizados dados de 127 GB e o resto do disco está vazio.<br/>O tipo de disco é standard com produção de 60 MBps.<br/>Nenhuma alteração de dados após a falha.| Tempo aproximado: 60-90 minutos.<br/> Durante a reproteção, a Recuperação do Site preencherá a parte de verificação de todos os dados. Isto funciona a 45MBps, pelo que o tempo total que levará é de 127 GB/45 MBps, aproximadamente 45 minutos.<br/>É necessário algum tempo para a recuperação do local para a escala automática, aproximadamente 20-30 minutos. |
|A região de origem tem 1 VM com 1 disco padrão TB.<br/>Apenas 127 dados de GB são utilizados e o resto do disco está vazio.<br/>O tipo de disco é standard com produção de 60 MBps.<br/>Os dados de 45 GB mudam após a falha.| Tempo aproximado: 2,5-3 horas.<br/> Durante a reproteção, a Recuperação do Site preencherá a parte de verificação de todos os dados. Isto funciona a 45MBps, pelo que o tempo total que levará é de 127 GB/45 MBps, aproximadamente 45 minutos.<br/>A velocidade de transferência é de aproximadamente 16% da potência, ou 9,6MBps. Portanto, o tempo de transferência para aplicar alterações de 45 GB que é de 45 GB/9.6 MBps, aproximadamente 80 minutos.<br/>É necessário algum tempo para a recuperação do local para a escala automática, aproximadamente 20-30 minutos. |
|A região de origem tem 1 VM com 1 disco padrão TB.<br/>Apenas 20 GB de dados são usados e o resto do disco está vazio.<br/>O tipo de disco é standard com produção de 60 MBps.<br/>Os dados iniciais no disco imediatamente após a falha foram de 15 GB. Houve uma alteração de dados de 5 GB após a falha. O total de dados povoados é, portanto, de 20 GB.| Tempo aproximado: 1-1,5 horas.<br/>Uma vez que os dados preenchidos no disco são inferiores a 10% do tamanho do disco, realizamos uma replicação inicial completa.<br/> A velocidade de transferência é de aproximadamente 16% da potência, ou 9,6MBps. Portanto, o tempo de transferência para aplicar alterações de 20 GB que é de 20 GB/9.6 MBps, aproximadamente 36 minutos.<br/>É necessário algum tempo para a recuperação do local para a escala automática, aproximadamente 20-30 minutos. |
|A região de origem tem 1 VM com 1 disco premium TB.<br/>Apenas são utilizados dados de 127 GB e o resto do disco está vazio.<br/>O tipo de disco é premium com produção de 200 MBps.<br/>Nenhuma alteração de dados após a falha.| Tempo aproximado: 2 horas.<br/>Durante a reproteção, a Recuperação do Site preencherá a parte de verificação de todos os dados. Isto funciona a 25MBps (até 16% da produção do disco), pelo que o tempo total que levará é de 127 GB/25 MBps, aproximadamente 87 minutos.<br/>É necessário algum tempo para a recuperação do local para a escala automática, aproximadamente 20-30 minutos. |
|A região de origem tem 1 VM com 1 disco premium TB.<br/>Apenas 127 dados de GB são utilizados e o resto do disco está vazio.<br/>O tipo de disco é premium com produção de 200 MBps.<br/>Os dados de 45 GB mudam após a falha.| Tempo aproximado: 2,5-3 horas.<br/>Durante a reproteção, a Recuperação do Site preencherá a parte de verificação de todos os dados. Isto funciona a 25MBps (até 16% da produção do disco), pelo que o tempo total que levará é de 127 GB/25 MBps, aproximadamente 87 minutos.</br>A velocidade de transferência é de aproximadamente 16% da potência, ou 32MBps. Portanto, o tempo de transferência para aplicar alterações de 45 GB que é de 45 GB/32 MBps, aproximadamente 24 minutos.<br/>É necessário algum tempo para a recuperação do local para a escala automática, aproximadamente 20-30 minutos. |
|A região de origem tem 1 VM com 1 disco premium TB.<br/>Apenas 20 GB de dados são usados e o resto do disco está vazio.<br/>O tipo de disco é premium com produção de 200 MBps.<br/>Os dados iniciais no disco imediatamente após a falha foram de 15 GB. Houve uma alteração de dados de 5 GB após a falha. O total de dados povoados é, portanto, de 20 GB| Tempo aproximado: 30-45 minutos.<br/>Uma vez que os dados preenchidos no disco são inferiores a 10% do tamanho do disco, realizamos uma replicação inicial completa.<br/>A velocidade de transferência é de aproximadamente 16% da potência, ou 32MBps. Portanto, o tempo de transferência para aplicar alterações de 20 GB que é de 20 GB/32 MBps, aproximadamente 11 minutos.<br/>É necessário algum tempo para a recuperação do local para a escala automática, aproximadamente 20-30 minutos |

Quando o VM é re-protegido depois de ter falhado na região primária (isto é, se o VM for re-protegido da região primária para a região DR), o VM-alvo e os NIC(s) associados são eliminados.

Quando o VM é re-protegido da região DR para a região primária, não apagamos os VM primários e os NIC(s associados).

## <a name="next-steps"></a>Passos seguintes

Depois de o VM estar protegido, pode iniciar uma falha. O failover encerra o VM na região secundária e cria e arranca o VM na região primária, com breves tempos de paragem durante este processo. Recomendamos que escolha um momento adequado para este processo e que faça um teste de failover antes de iniciar uma falha total no local principal. [Saiba mais](site-recovery-failover.md) sobre o failover da Recuperação do Site Azure.
