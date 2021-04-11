---
title: Configurar failover/failback para um site secundário de Hiper-V com recuperação do site Azure
description: Aprenda a falhar sobre os Hiper-VMs para o seu local secundário no local e falhe de volta ao local primário, durante a recuperação de desastres com a Recuperação do Local de Azure.
services: site-recovery
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.openlocfilehash: 72b23e37a365287cc8a850f960137fdb7ec08497
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581118"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-your-secondary-on-premises-site"></a>Falha e falha hiper-V VMs replicados no seu local secundário no local

O serviço [de recuperação do local Azure](site-recovery-overview.md) gere e orquestra a replicação, failover e o failback de máquinas no local, e máquinas virtuais Azure (VMs).

Este artigo descreve como falhar sobre um VM Hiper-V gerido numa nuvem virtual manager (VMM) do System Center, para um site de VMM secundário. Depois de feita a ativação pós-falha, vai fazer a reativação pós-falha para o seu site no local quando estiver disponível. Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Falha sobre um Hiper-V VM de uma nuvem VMM primária para uma nuvem de VMM secundária
> * Reprotegir do local secundário para o primário, e falhar de volta
> * Opcionalmente começar a replicar-se do primário para o secundário novamente

## <a name="failover-and-failback"></a>Ativação pós-falha e reativação pós-falha

Failover e failback tem três fases:

1. **Falha no local secundário**: Falha as máquinas do local primário para o secundário.
2. **Falha no local secundário**: Replicar VMs do secundário ao primário, e executar uma falha planeada para falhar para trás.
3. Após o failover planeado, opcionalmente começar a replicar-se do local primário para o secundário novamente.


## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de ter concluído um [exercício de recuperação](hyper-v-vmm-test-failover.md) de desastres para verificar se está tudo funcionando como esperado.
- Para completar o failback, certifique-se de que os servidores VMM primários e secundários estão ligados à Recuperação do Site.



## <a name="run-a-failover-from-primary-to-secondary"></a>Executar um failover do primário para o secundário

Pode executar uma falha regular ou planeada para Hiper-VMs.

- Utilize uma falha regular para interrupções inesperadas. Quando executam este failover, a Recuperação do Site cria um VM no site secundário e alimenta-o. A perda de dados pode ocorrer dependendo de dados pendentes que não foram sincronizados.
- Uma falha planeada pode ser usada para manutenção, ou durante a paragem prevista. Esta opção proporciona zero perda de dados. Quando uma falha planeada é desencadeada, os VMs de origem são desligados. Os dados não sincronizados são sincronizados e a falha é desencadeada. 
- 
  Este procedimento descreve como executar uma falha regular.


1. Em **Definições**  >  **Itens replicados** clique no VM > **Failover**.
1. Selecione **desligar a máquina antes de começar a falhar** se quiser que a Recuperação do Local tente fazer uma paragem de VMs de origem antes de ativar a falha. A Recuperação do Site também tentará sincronizar dados no local que ainda não foram enviados para o site secundário, antes de desencadear a falha. Note que a falha continua mesmo que o encerramento falhe. Pode acompanhar o progresso falhado na página **Jobs.**
2. Deverá agora poder ver o VM na nuvem secundária de VMM.
3. Depois de verificar o VM, **cometa** o failover. São eliminados todos os pontos de recuperação disponíveis.

> [!WARNING]
> **Não cancelar uma ativação pós-falha em curso**: antes do início da ativação pós-falha, a replicação da VM é parada. Se cancelar uma ativação pós-falha que esteja em curso, a mesma para, mas a VM não será replicada outra vez.  


## <a name="reverse-replicate-and-failover"></a>Reverter replicar e falhar

Comece a replicar do local secundário para o primário, e falhe de volta ao local primário. Depois de os VM voltarem a funcionar no local primário, pode replicá-los para o local secundário.  

 
1. Clique no VM > clique em **Reverse Replicate**.
2. Assim que o trabalho estiver concluído, clique no VM >In **Failover**, verifique a direção de falha (a partir da nuvem VMM secundária) e selecione as localizações de origem e alvo. 
4. Iniciar a ativação pós-falha. Pode seguir o progresso da ativação pós-falha no separador **Trabalhos**.
5. Na nuvem VMM primária, verifique se o VM está disponível.
6. Se pretender voltar a replicar o VM primário no local secundário, clique em **Reverse Replicate**.

## <a name="next-steps"></a>Passos seguintes
[Reveja o passo](hyper-v-vmm-disaster-recovery.md) para replicar Hiper-VMs para um site secundário.
