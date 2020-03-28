---
title: Falhar sobre VMware VMs para Azure com recuperação do site
description: Saiba como falhar com vMware VMs para Azure em Recuperação de Site Azure
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/16/2019
ms.custom: MVC
ms.openlocfilehash: 8501bb1a998eb08984a118bfa5d52d1e3f3e4f84
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75498076"
---
# <a name="fail-over--vmware-vms"></a>Falhar sobre VMware VMs

Este artigo descreve como falhar sobre uma máquina virtual VMware (VM) no local para Azure com recuperação do [site Azure](site-recovery-overview.md).

Este é o quinto tutorial de uma série que mostra como configurar a recuperação de desastres para Azure para máquinas no local.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Verifique se as propriedades VMware VM estão em conformidade com os requisitos do Azure.
> * Falhar sobre vMs específicos para Azure.

> [!NOTE]
> Os tutoriais mostram-lhe o caminho de implantação mais simples para um cenário. Usam opções predefinidas sempre que possível e não mostram todas as configurações e caminhos possíveis. Se quiser aprender sobre falhas em detalhe, consulte [Fail over VMs e servidores físicos](site-recovery-failover.md).

[Aprenda sobre](failover-failback-overview.md#types-of-failover) diferentes tipos de falhas. Se quiser falhar em vários VMs num plano de recuperação, reveja [este artigo.](site-recovery-failover.md)

## <a name="before-you-start"></a>Antes de começar

Complete os tutoriais anteriores:

1. Certifique-se de que [montou o Azure](tutorial-prepare-azure.md) para a recuperação de desastres no local de VMware VMs, VMs Hiper-V e máquinas físicas para o Azure.
2. Prepare o seu ambiente [VMware](vmware-azure-tutorial-prepare-on-premises.md) no local para a recuperação de desastres. 
3. Configurar a recuperação de desastres para [VMware VMs](vmware-azure-tutorial.md).
4. Faça um exercício de recuperação de [desastres](tutorial-dr-drill-azure.md) para ter certeza de que está tudo funcionando como esperado.

## <a name="verify-vm-properties"></a>Verificar as propriedades da VM

Antes de executar uma falha, verifique as propriedades VM para se certificar de que os VMs cumprem os [requisitos do Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

Verifique as propriedades da seguinte forma:

1. Em **itens protegidos,** selecione **Itens Replicados**e, em seguida, selecione o VM que pretende verificar.

2. No painel **Item replicado**, existe um resumo das informações, do estado de funcionamento e dos pontos de recuperação mais recentes disponíveis da VM. Selecione **Propriedades** para ver mais detalhes.

3. Na **Compute e na Rede,** pode modificar estas propriedades conforme necessário:
    * Nome azure
    * Grupo de recursos
    * Tamanho do alvo
    * [Conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md)
    * Definições de disco geridos

4. Pode visualizar e modificar as definições de rede, incluindo:

    * A rede e a subrede em que o Azure VM será localizado após a falha.
    * O endereço IP que lhe será atribuído.

5. Em **Discos**, pode ver informações sobre o sistema operativo e os discos de dados na VM.

## <a name="run-a-failover-to-azure"></a>Executar uma ativação pós-falha para o Azure

1. Em **Definições** > **Itens replicados,** selecione o VM que pretende falhar e, em seguida, selecione **Failover**.
2. Em **Failover,** selecione um Ponto de **Recuperação** para falhar. Pode utilizar uma das opções seguintes:
   * **Mais recente**: esta opção processa primeiro todos os dados enviados para o Site Recovery. Fornece o menor Objetivo de Ponto de Recuperação (RPO) porque o VM Azure que é criado após a falha tem todos os dados que foram replicados para a Recuperação do Local quando a falha foi desencadeada.
   * **Mais recente processado**: Esta opção falha o VM até ao mais recente ponto de recuperação processado pela Recuperação do Site. Esta opção fornece um RTO baixo (Objetivo do Tempo de Recuperação) porque não é gasto tempo a processar dados não processados.
   * **Mais recente aplicação consistente**: Esta opção falha o VM no mais recente ponto de recuperação consistente de aplicações processado pela Recovery do Site.
   * **Personalizado**: Esta opção permite especificar um ponto de recuperação.

3. Selecione **a máquina de desligar antes** de iniciar a falha para tentar desligar os VMs de origem antes de acionar a falha. A falha continua mesmo que a paralisação falhe. Pode acompanhar o progresso da falha na página **Jobs.**

Em alguns cenários, o failover requer um processamento adicional que leva cerca de 8 a 10 minutos para ser concluído. Pode notar tempos de falha de teste mais longos para:

* VMware VMs executando uma versão de serviço de Mobilidade com mais de 9.8.
* Servidores físicos.
* VMware Linux VMs.
* VMs hiper-V protegidos como servidores físicos.
* VMware VMs que não têm o serviço DHCP ativado.
* VMware VMs que não têm os seguintes controladores de arranque: storvsc, vmbus, storflt, intelide, atapi.

> [!WARNING]
> Não cancele uma falha em andamento. Antes de iniciar a ativação pós-falha, a replicação de VM é interrompida. Se cancelar uma ativação pós-falha que esteja em curso, a mesma para, mas a VM não será replicada outra vez.

## <a name="connect-to-failed-over-vm"></a>Ligar a VM falhado

1. Se pretender ligar-se aos VMs Azure após a falha utilizando o Protocolo de Ambiente de Trabalho Remoto (RDP) e secure Shell (SSH), [verifique se os requisitos foram cumpridos]((ailover-failback-overview.md#connect-to-azure-after-failover).
2. Depois de failover, vá ao [connecting](../virtual-machines/windows/connect-logon.md) VM e valide ligando-o.
3. Use o ponto de **recuperação da alteração** se pretender utilizar um ponto de recuperação diferente após a falha. Depois de cometer a falha no próximo passo, esta opção deixará de estar disponível.
4. Após validação, selecione **Comprometer-se** a finalizar o ponto de recuperação do VM após a falha.
5. Depois de se comprometer, todos os outros pontos de recuperação disponíveis são eliminados. Este passo completa a falha.

>[!TIP]
> Se encontrar algum problema de conectividade após a falha, siga o guia de resolução de [problemas](site-recovery-failover-to-azure-troubleshoot.md).

## <a name="next-steps"></a>Passos seguintes

Após a falha, reproteja os VMs Azure para as instalações. Depois, depois de os VMs serem reprotegidos e replicando-se para o local, volte de Azure quando estiver pronto.

> [!div class="nextstepaction"]
> [VMs de Reproteção Azure](vmware-azure-reprotect.md)
> [falham de volta de Azure](vmware-azure-failback.md)
