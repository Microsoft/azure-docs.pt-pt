---
title: Ativação pós-falha e reativação pós-falha de VMs de Hyper-V replicado para um Datacenter secundário durante a recuperação após desastre com o Azure Site Recovery | Documentos da Microsoft
description: Saiba como a ativação pós-falha de VMs de Hyper-V para o seu site no local secundário e a reativação pós-falha para um site primário, durante a recuperação após desastre com o Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: raynew
ms.openlocfilehash: 3890283f70bfd922d0c436f76b38d7f81d7b8c6f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58107023"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-your-secondary-on-premises-site"></a>Ativação pós-falha e reativação pós-falha de VMs de Hyper-V replicados para o seu site no local secundário

O [do Azure Site Recovery](site-recovery-overview.md) serviço gere e orquestra a replicação, ativação pós-falha e reativação pós-falha de máquinas no local e máquinas virtuais do Azure (VMs).

Este artigo descreve como fazer a ativação pós-falha de uma VM de Hyper-V geridas numa nuvem do System Center Virtual Machine Manager (VMM), para um site VMM secundário. Depois de feita a ativação pós-falha, vai fazer a reativação pós-falha para o seu site no local quando estiver disponível. Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Ativação pós-falha numa VM de Hyper-V de uma nuvem VMM principal a uma nuvem VMM secundária
> * Voltar a proteger do site secundário para o primário e a reativação pós-falha
> * Opcionalmente, iniciar a replicação do primário para o secundário novamente

## <a name="failover-and-failback"></a>Ativação pós-falha e reativação pós-falha

A ativação pós-falha e reativação pós-falha tem três fases:

1. **Ativação pós-falha para o site secundário**: Pós-falha de máquinas do site primário para o secundário.
2. **Reativação pós-falha do site secundário**: Replicar VMs de secundária para primária e execute uma ativação pós-falha planeada para a reativação pós-falha.
3. Após a ativação pós-falha planeada, opcionalmente, inicie a replicação do site primário para o secundário novamente.


## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que concluiu um [teste de recuperação após desastre](hyper-v-vmm-test-failover.md) para verificar se tudo o que está a funcionar conforme esperado.
- Para concluir a reativação pós-falha, certifique-se de que os servidores primários e secundários do VMM estão ligados ao Site Recovery.



## <a name="run-a-failover-from-primary-to-secondary"></a>Executar uma ativação pós-falha do primário para o secundário

Pode executar uma ativação pós-falha planeada ou regular para VMs de Hyper-V.

- Utilize uma ativação pós-falha regular para falhas inesperadas. Quando executa esta ativação pós-falha, o Site Recovery cria uma VM no site secundário e liga-se. Pode ocorrer perda de dados consoante colocar pendentes os dados que ainda não foram sincronizados.
- Uma ativação pós-falha planeada pode ser utilizada para manutenção ou durante um período de inatividade esperado. Esta opção fornece zero perda de dados. Quando é acionada uma ativação pós-falha planeada, as VMs de origem são encerradas. Dados não sincronizados são sincronizados e a ativação pós-falha é acionada. 
- 
  Este procedimento descreve como executar uma ativação pós-falha normal.


1. Em **Definições** > **Itens replicados** clique na VM > **Ativação Pós-falha**.
1. Selecione **encerrar a máquina antes de iniciar a ativação pós-falha** se pretender que o Site Recovery para tentar fazer um encerramento de VMs de origem antes de acionar a ativação pós-falha. Recuperação de site também tentará sincronizar os dados no local que ainda não tem sido enviados para o site secundário, antes de acionar a ativação pós-falha. Tenha em atenção que a ativação pós-falha continua, mesmo que o encerramento falhe. Pode seguir o progresso da ativação pós-falha na página **Trabalhos**.
2. Deve ser capaz de ver a VM na nuvem do VMM secundária.
3. Depois de verificar a VM **consolidar** a ativação pós-falha. São eliminados todos os pontos de recuperação disponíveis.

> [!WARNING]
> **Não cancelar uma ativação pós-falha em curso**: Antes de iniciar a ativação pós-falha, a replicação de VM é interrompida. Se cancelar uma ativação pós-falha que esteja em curso, a mesma para, mas a VM não será replicada outra vez.  


## <a name="reverse-replicate-and-failover"></a>A replicação inversa e ativação pós-falha

Iniciar a replicação do site secundário para o primário e reativação pós-falha para o site primário. Depois das VMs estão em execução no site primário novamente, pode replicá-los para o site secundário.  

 
1. Clique na VM > clique em **inverter replicar**.
2. Quando a tarefa estiver concluída, clique na VM > no **ativação pós-falha**, certifique-se a direção de ativação pós-falha (a partir de nuvem VMM secundária) e selecione as localizações de origem e de destino. 
4. Iniciar a ativação pós-falha. Pode seguir o progresso da ativação pós-falha no separador **Trabalhos**.
5. Na cloud principal do VMM, verifique que a VM está disponível.
6. Se quiser iniciar a replicação novamente a VM principal para o site secundário, clique em **inverter replicar**.

## <a name="next-steps"></a>Passos Seguintes
[Reveja o passo](hyper-v-vmm-disaster-recovery.md) para replicar VMs de Hyper-V para um site secundário.
