---
title: Fazer failover e failback de VMs do Hyper-V replicadas para um data center secundário durante a recuperação de desastre com Azure Site Recovery | Microsoft Docs
description: Saiba como fazer failover de VMs do Hyper-V para seu site local secundário e failback para o site primário, durante a recuperação de desastre com o Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: f93c9bd679272f76665a6c8e4a0c611327699839
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813697"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-your-secondary-on-premises-site"></a>Fazer failover e failback de VMs do Hyper-V replicadas para seu site local secundário

O serviço de [Azure site Recovery](site-recovery-overview.md) gerencia e orquestra a replicação, o failover e o failback de máquinas locais e VMs (máquinas virtuais) do Azure.

Este artigo descreve como fazer failover de uma VM Hyper-V gerenciada em uma nuvem System Center Virtual Machine Manager (VMM) para um site secundário do VMM. Depois de feita a ativação pós-falha, vai fazer a reativação pós-falha para o seu site no local quando estiver disponível. Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Fazer failover de uma VM do Hyper-V de uma nuvem do VMM primária para uma nuvem do VMM secundária
> * Proteger novamente do site secundário para o primário e fazer failback
> * Opcionalmente, inicie a replicação do primário para o secundário novamente

## <a name="failover-and-failback"></a>Ativação pós-falha e reativação pós-falha

O failover e o failback têm três estágios:

1. **Failover para o site secundário**: Reprovam computadores do site primário para o secundário.
2. **Failback do site secundário**: Replique as VMs do secundário para o primário e execute um failover planejado para realizar o failback.
3. Após o failover planejado, opcionalmente, inicie a replicação do site primário para o secundário novamente.


## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de ter concluído uma [análise de recuperação de desastre](hyper-v-vmm-test-failover.md) para verificar se tudo está funcionando conforme o esperado.
- Para concluir o failback, verifique se os servidores primários e secundários do VMM estão conectados a Site Recovery.



## <a name="run-a-failover-from-primary-to-secondary"></a>Executar um failover do primário para o secundário

Você pode executar um failover regular ou planejado para VMs do Hyper-V.

- Use um failover regular para interrupções inesperadas. Quando você executar esse failover, Site Recovery criará uma VM no site secundário e a ligará. A perda de dados pode ocorrer dependendo dos dados pendentes que não foram sincronizados.
- Um failover planejado pode ser usado para manutenção ou durante a interrupção esperada. Essa opção fornece nenhuma perda de dados. Quando um failover planejado é disparado, as VMs de origem são desligadas. Os dados não sincronizados são sincronizados e o failover é disparado. 
- 
  Este procedimento descreve como executar um failover regular.


1. Em **Definições** > **Itens replicados** clique na VM > **Ativação Pós-falha**.
1. Selecione **desligar o computador antes do início do failover** se desejar que site Recovery tente fazer um desligamento das VMs de origem antes de disparar o failover. Site Recovery também tentará sincronizar os dados locais que ainda não foram enviados para o site secundário antes de disparar o failover. Observe que o failover continua mesmo se o desligamento falhar. Pode seguir o progresso da ativação pós-falha na página **Trabalhos**.
2. Agora você deve ser capaz de ver a VM na nuvem de VMM secundária.
3. Depois de verificar a VM, **confirme** o failover. São eliminados todos os pontos de recuperação disponíveis.

> [!WARNING]
> **Não cancelar um failover em andamento**: Antes de iniciar a ativação pós-falha, a replicação de VM é interrompida. Se cancelar uma ativação pós-falha que esteja em curso, a mesma para, mas a VM não será replicada outra vez.  


## <a name="reverse-replicate-and-failover"></a>Replicação reversa e failover

Inicie a replicação do site secundário para o primário e faça failback para o site primário. Depois que as VMs estiverem em execução no site primário novamente, você poderá replicá-las para o site secundário.  

 
1. Clique na VM > clique em **replicação inversa**.
2. Quando o trabalho for concluído, clique na VM > em **failover**, verifique a direção do failover (da nuvem secundária do VMM) e selecione os locais de origem e de destino. 
4. Iniciar a ativação pós-falha. Pode seguir o progresso da ativação pós-falha no separador **Trabalhos**.
5. Na nuvem primária do VMM, verifique se a VM está disponível.
6. Se você quiser iniciar a replicação da VM primária novamente para o site secundário novamente, clique em **replicação inversa**.

## <a name="next-steps"></a>Passos seguintes
[Examine a etapa](hyper-v-vmm-disaster-recovery.md) para replicar VMs do Hyper-V para um site secundário.
