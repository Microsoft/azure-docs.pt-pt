---
title: Fazer a ativação pós-falha e a reativação pós-falha de VMs do VMware e de servidores físicos durante a recuperação após desastre para o Azure com o Site Recovery | Microsoft Docs
description: Saiba como fazer failover de VMs VMware e servidores físicos para o Azure e como executar failback para o site local, durante a recuperação de desastres no Azure usando Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 08/22/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 852193e137eab10d1e46c5ba6ae6636d530095be
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972200"
---
# <a name="fail-over-and-fail-back-vmware-vms"></a>Fazer failover e failback de VMs VMware

Este artigo descreve como fazer failover de uma VM (máquina virtual) VMware local para [Azure site Recovery](site-recovery-overview.md).

Este é o quinto tutorial de uma série que mostra como configurar a recuperação de desastres para o Azure para computadores locais.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Verifique se as propriedades da VM do VMware estão em conformidade com os requisitos do Azure.
> * Execute um failover no Azure.

> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário. Eles usam as opções padrão sempre que possível e não mostram todas as configurações e caminhos possíveis. Se você quiser saber mais sobre failover em detalhes, confira VMs de failover [e servidores físicos](site-recovery-failover.md).

## <a name="before-you-start"></a>Antes de começar

Conclua os tutoriais anteriores:

1. Verifique se você configurou o [Azure](tutorial-prepare-azure.md) para a recuperação de desastre local de VMs VMware, VMS do Hyper-V e máquinas físicas para o Azure.
2. Prepare seu ambiente do [VMware](vmware-azure-tutorial-prepare-on-premises.md) ou [Hyper-V](hyper-v-prepare-on-premises-tutorial.md) local para recuperação de desastres. Se você estiver configurando a recuperação de desastre para servidores físicos, examine a [matriz de suporte](vmware-physical-secondary-support-matrix.md).
3. Configure a recuperação de desastre para [VMs VMware](vmware-azure-tutorial.md), [VMs Hyper-V](hyper-v-azure-tutorial.md)ou [máquinas físicas](physical-azure-disaster-recovery.md).
4. Execute uma [análise de recuperação de desastre](tutorial-dr-drill-azure.md) para certificar-se de que tudo está funcionando conforme o esperado.

## <a name="failover-and-failback"></a>Ativação pós-falha e reativação pós-falha

A ativação pós-falha e a reativação pós-falha têm quatro fases:

1. **Failover para o Azure:** Quando o site primário local ficar inativo, faça o failover dos computadores no Azure. Após o failover, as VMs do Azure são criadas a partir de dados replicados.
2. **Proteger novamente as VMs do Azure:** No Azure, proteja novamente as VMs do Azure para que elas comecem a replicar de volta para VMs VMware locais. A VM local é desativada durante a nova proteção, para ajudar a garantir a consistência dos dados.
3. **Failover para local:** Quando seu site local estiver em execução, execute um failover para fazer failback do Azure.
4. **Proteger novamente as VMs locais:** Após o failback dos dados, proteja novamente as VMs locais para as quais você fez o failback, para que elas comecem a replicar para o Azure.

## <a name="verify-vm-properties"></a>Verificar as propriedades da VM

Antes de executar um failover, verifique as propriedades da VM para certificar-se de que as VMs atendam [aos requisitos do Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

Verifique as propriedades da seguinte maneira:

1. Em **itens protegidos**, selecione **itens replicados**e, em seguida, selecione a VM que você deseja verificar.

2. No painel **Item replicado**, existe um resumo das informações, do estado de funcionamento e dos pontos de recuperação mais recentes disponíveis da VM. Selecione **Propriedades** para exibir mais detalhes.

3. Em **computação e rede**, você pode modificar essas propriedades conforme necessário:
    * Nome do Azure
    * Resource group
    * Tamanho de destino
    * [Conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md)
    * Configurações de disco gerenciado

4. Você pode exibir e modificar as configurações de rede, incluindo:

    * A rede e a sub-rede em que a VM do Azure será localizada após o failover.
    * O endereço IP que será atribuído a ele.

5. Em **Discos**, pode ver informações sobre o sistema operativo e os discos de dados na VM.

## <a name="run-a-failover-to-azure"></a>Executar uma ativação pós-falha para o Azure

1. Em **configurações** > **itens replicados**, selecione a VM que você deseja fazer failover e, em seguida, selecione **failover**.
2. Em **Ativação pós-falha**, selecione um **Ponto de Recuperação** para o qual irá realizar a ativação pós-falha. Pode utilizar uma das opções seguintes:
   * **Mais recente**: Essa opção primeiro processa todos os dados enviados para Site Recovery. Ele fornece o RPO (objetivo de ponto de recuperação) mais baixo porque a VM do Azure criada após o failover tem todos os dados que foram replicados para Site Recovery quando o failover foi disparado.
   * **Mais recente processado**: Essa opção falha na VM para o último ponto de recuperação processado pelo Site Recovery. Essa opção fornece um RTO baixo (objetivo de tempo de recuperação) porque nenhum tempo é gasto processando dados não processados.
   * **Consistente com o aplicativo mais recente**: Essa opção falha na VM para o ponto de recuperação consistente com o aplicativo mais recente processado pelo Site Recovery.
   * **Personalizado**: Essa opção permite que você especifique um ponto de recuperação.

3. Selecione **desligar o computador antes do início do failover** para tentar desligar as VMs de origem antes de disparar o failover. O failover continua mesmo se o desligamento falhar. Pode seguir o progresso da ativação pós-falha na página **Trabalhos**.

Em alguns cenários, o failover requer processamento adicional que leva cerca de 8 a 10 minutos para ser concluído. Você pode notar tempos de failover de teste mais longos para:

* VMs VMware que executam uma versão do serviço de mobilidade com mais de 9,8.
* Servidores físicos.
* VMs VMware Linux.
* VMs do Hyper-V protegidas como servidores físicos.
* VMs VMware que não têm o serviço DHCP habilitado.
* VMs VMware que não têm os seguintes drivers de inicialização: storvsc, VMBus, storflt, intelide, ATAPI.

> [!WARNING]
> Não cancele um failover em andamento. Antes de iniciar a ativação pós-falha, a replicação de VM é interrompida. Se cancelar uma ativação pós-falha que esteja em curso, a mesma para, mas a VM não será replicada outra vez.

## <a name="connect-to-failed-over-vm"></a>Conectar-se à VM com failover

1. Se você quiser se conectar às VMs do Azure após o failover usando protocolo RDP (RDP) e Secure Shell (SSH), [Verifique se os requisitos](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)foram atendidos.
2. Após o failover, acesse a VM e valide-a conectando- [se](../virtual-machines/windows/connect-logon.md) a ela.
3. Use **alterar ponto de recuperação** se desejar usar um ponto de recuperação diferente após o failover. Depois de confirmar o failover na próxima etapa, essa opção não estará mais disponível.
4. Após a validação, selecione **confirmar** para finalizar o ponto de recuperação da VM após o failover.
5. Após a confirmação, todos os outros pontos de recuperação disponíveis serão excluídos. Esta etapa conclui o failover.

>[!TIP]
> Se você encontrar problemas de conectividade após o failover, siga o [Guia de solução de problemas](site-recovery-failover-to-azure-troubleshoot.md).

## <a name="next-steps"></a>Passos Seguintes

Após o failover, proteja novamente as VMs do Azure para o local. Em seguida, depois que as VMs forem protegidas novamente e replicando para o site local, faça failback do Azure quando estiver pronto.

> [!div class="nextstepaction"]
> [Proteger](vmware-azure-reprotect.md)
> novamente as VMs do Azure[failback do Azure](vmware-azure-failback.md)
