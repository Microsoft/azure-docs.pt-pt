---
title: Configurar failover/failback para um site secundário de Hiper-V com recuperação do site Azure
description: Aprenda a falhar sobre os VMs Hiper-V para o seu local secundário no local e falhe no local primário, durante a recuperação de desastres com a Recuperação do Site Azure.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: d31355bcb0ce42874c19988738ba06138c7a0b7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74082590"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-your-secondary-on-premises-site"></a>Fail over and fail back Hyper-V V VMs replicados para o seu site secundário no local

O serviço [azure de recuperação](site-recovery-overview.md) de locais gere e orquestra a replicação, a falha e o relançamento das máquinas no local, e as máquinas virtuais Azure (VMs).

Este artigo descreve como falhar sobre um VM Hiper-V gerido numa nuvem de Gestor de Máquinavirtual do System Center (VMM), para um site de VMM secundário. Depois de feita a ativação pós-falha, vai fazer a reativação pós-falha para o seu site no local quando estiver disponível. Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Falhar sobre um VM Hiper-V de uma nuvem vmm primária para uma nuvem de VMM secundária
> * Reproteja do local secundário para as primárias, e falhe de volta
> * Opcionalmente começar a replicar do primário para o secundário novamente

## <a name="failover-and-failback"></a>Ativação pós-falha e reativação pós-falha

Failover e failback tem três fases:

1. **Fail over to secondary site**: Fail machines over from the primary site to the secondary.
2. **Recue do local secundário**: Replicar VMs do secundário para o primário, e executar uma falha planeada para falhar.
3. Após a falha planeada, opcionalmente comece a replicar do local primário para o secundário novamente.


## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de ter completado um exercício de recuperação de [desastres](hyper-v-vmm-test-failover.md) para verificar se está tudo a funcionar como esperado.
- Para completar o failback, certifique-se de que os servidores VMM primários e secundários estão ligados à Recuperação do Site.



## <a name="run-a-failover-from-primary-to-secondary"></a>Executar uma falha do primário para o secundário

Você pode executar uma falha regular ou planejada para VMs Hiper-V.

- Use uma falha regular para interrupções inesperadas. Quando executa este failover, a Recuperação do Site cria um VM no local secundário, e alimenta-o. A perda de dados pode ocorrer dependendo de dados pendentes que não foram sincronizados.
- Uma falha planeada pode ser usada para manutenção, ou durante a paragem esperada. Esta opção fornece zero perda de dados. Quando uma falha planeada é desencadeada, as VMs de origem são desligadas. Os dados não sincronizados são sincronizados e a falha é desencadeada. 
- 
  Este procedimento descreve como executar uma falha regular.


1. Em **Definições,** > **os itens replicados** clique no VM > **Failover**.
1. Selecione **a máquina de desligar antes** de iniciar a falha se pretender que a Recuperação do Site tente fazer uma paragem dos VMs de origem antes de acionar a falha. A Recuperação do Site também tentará sincronizar dados no local que ainda não foram enviados para o local secundário, antes de desencadear a falha. Note que a falha continua mesmo que o encerramento falhe. Pode acompanhar o progresso da falha na página **Jobs.**
2. Agora deve poder ver o VM na nuvem secundária de VMM.
3. Depois de verificar o VM, **cometa** a falha. São eliminados todos os pontos de recuperação disponíveis.

> [!WARNING]
> **Não cancelar uma ativação pós-falha em curso**: antes do início da ativação pós-falha, a replicação da VM é parada. Se cancelar uma ativação pós-falha que esteja em curso, a mesma para, mas a VM não será replicada outra vez.  


## <a name="reverse-replicate-and-failover"></a>Reverter a replicação e a falha

Comece a replicar-se do local secundário para o primário, e falhe de volta ao local primário. Depois de os VMs voltarem a funcionar no local primário, pode replicá-los para o local secundário.  

 
1. Clique no VM > clique em **Reverter Replicate**.
2. Uma vez concluída a obra, clique no VM >In **Failover**, verifique a direção de falha (a partir da nuvem de VMM secundária) e selecione as localizações de origem e alvo. 
4. Iniciar a ativação pós-falha. Pode seguir o progresso da ativação pós-falha no separador **Trabalhos**.
5. Na nuvem vmm primária, verifique se o VM está disponível.
6. Se quiser voltar a replicar o VM primário para o local secundário, clique em **Reverse Replicate**.

## <a name="next-steps"></a>Passos seguintes
[Reveja o passo](hyper-v-vmm-disaster-recovery.md) para replicar Os VMs hiper-V para um local secundário.
