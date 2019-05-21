---
title: Fazer a ativação pós-falha e a reativação pós-falha de VMs do VMware e de servidores físicos durante a recuperação após desastre para o Azure com o Site Recovery | Microsoft Docs
description: Saiba como fazer a ativação pós-falha de VMs de VMware e servidores físicos para o Azure e a efetuar a ativação pós-falha para o site no local, durante a recuperação após desastre para o Azure utilizando a recuperação de Site.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 7a089b3e4d7b8a38f2bf88c8ccf6e269331589be
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65966290"
---
# <a name="fail-over-and-fail-back-vmware-vms"></a>Ativação pós-falha e reativação pós-falha de VMs de VMware

Este artigo descreve como efetuar a ativação pós-falha de uma máquina de virtual de VMware no local (VM) [do Azure Site Recovery](site-recovery-overview.md).

Este é o quinto tutorial de uma série que mostra-lhe como configurar a recuperação após desastre para o Azure para máquinas no local.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Certifique-se de que as propriedades da VM de VMware está em conformidade com os requisitos do Azure.
> * Execute uma ativação pós-falha para o Azure.

> [!NOTE]
> Tutoriais mostram-lhe o caminho de implantação mais simples para um cenário. Eles utilizam as opções predefinidas, sempre que possível e não mostram todas as configurações possíveis e caminhos. Se quiser saber mais sobre a ativação pós-falha em detalhes, veja [efetuar a ativação pós-falha de VMs e servidores físicos](site-recovery-failover.md).

## <a name="before-you-start"></a>Antes de começar

Conclua os tutoriais anteriores:

1. Certifique-se de que [configurar o Azure](tutorial-prepare-azure.md) para recuperação de desastre no local de VMs de VMware, VMs Hyper-V e máquinas físicas para o Azure.
2. Preparar o ambiente [VMware](vmware-azure-tutorial-prepare-on-premises.md) ou [Hyper-V](hyper-v-prepare-on-premises-tutorial.md) ambiente para recuperação após desastre. Se estiver configurando a recuperação após desastre para servidores físicos, reveja os [matriz de suporte](vmware-physical-secondary-support-matrix.md).
3. Configurar a recuperação após desastre para [VMs de VMware](vmware-azure-tutorial.md), [VMs de Hyper-V](hyper-v-azure-tutorial.md), ou [máquinas físicas](physical-azure-disaster-recovery.md).
4. Executar uma [teste de recuperação após desastre](tutorial-dr-drill-azure.md) para se certificar de que está tudo a funcionar conforme esperado.

## <a name="failover-and-failback"></a>Ativação pós-falha e reativação pós-falha

A ativação pós-falha e a reativação pós-falha têm quatro fases:

1. **Efetuar a ativação pós-falha para o Azure:** Quando o site primário no local fica inativo, pós-falha de máquinas para o Azure. Após a ativação pós-falha, as VMs do Azure são criadas a partir os dados replicados.
2. **Voltar a proteger VMs do Azure:** No Azure, voltar a proteger as VMs do Azure para que comecem a ser replicadas novamente para VMs de VMware no local. A VM no local é desativada durante a nova proteção, para ajudar a garantir a consistência dos dados.
3. **Efetuar a ativação pós-falha no local:** Quando o seu site no local está ativo e em execução, execute uma ativação pós-falha para reativação pós-falha do Azure.
4. **Voltar a proteger VMs no local:** Depois de dados falhou, novamente, voltar a proteger as VMs no local para o qual realizarão a reativação pós-falha, para que comecem a ser replicadas para o Azure.

## <a name="verify-vm-properties"></a>Verificar as propriedades da VM

Antes de executar uma ativação pós-falha, verifique as propriedades da VM para se certificar de que as VMs cumprem [requisitos do Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

Verifique as propriedades da seguinte forma:

1. Na **itens protegidos**, selecione **itens replicados**e, em seguida, selecione a VM que pretende verificar.

2. No painel **Item replicado**, existe um resumo das informações, do estado de funcionamento e dos pontos de recuperação mais recentes disponíveis da VM. Selecione **propriedades** para ver mais detalhes.

3. Na **computação e rede**, pode modificar estas propriedades, conforme necessário:
    * Nome do Azure
    * Grupo de recursos
    * Tamanho de destino
    * [Conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md)
    * Definições de disco gerido

4. Pode ver e modificar as definições de rede, incluindo:

    * A rede e a sub-rede na qual a VM do Azure estarão localizada após a ativação pós-falha.
    * O endereço IP que será atribuído a ele.

5. Em **Discos**, pode ver informações sobre o sistema operativo e os discos de dados na VM.

## <a name="run-a-failover-to-azure"></a>Executar uma ativação pós-falha para o Azure

1. Na **configurações** > **itens replicados**, selecione a VM que pretende efetuar a ativação pós-falha e, em seguida, selecione **ativação pós-falha**.
2. Em **Ativação pós-falha**, selecione um **Ponto de Recuperação** para o qual irá realizar a ativação pós-falha. Pode utilizar uma das opções seguintes:
   * **Mais recente**: Esta opção processa primeiro todos os dados enviados para o Site Recovery. Ele fornece o objetivo de ponto de recuperação (RPO) mais baixo, porque a VM do Azure criada após a ativação pós-falha tem todos os dados que foram replicados para o Site Recovery quando a ativação pós-falha foi acionada.
   * **Processado mais recentemente**: Esta opção faz a VM através do ponto de recuperação mais recente processado pelo Site Recovery. Esta opção proporciona um RTO (objetivo de tempo de recuperação) baixo, porque não é despendido tempo a processar dados não processados.
   * **Mais recente consistente com a aplicação**: Esta opção faz a VM ao longo para o ponto mais recente recuperação consistente com a aplicação processado pelo Site Recovery.
   * **Custom**: Esta opção permite-lhe especificar um ponto de recuperação.

3. Selecione **encerrar a máquina antes de iniciar a ativação pós-falha** para tentar encerrar as VMs de origem antes de acionar a ativação pós-falha. Ativação pós-falha continua, mesmo que o encerramento falhe. Pode seguir o progresso da ativação pós-falha na página **Trabalhos**.

Em alguns cenários, a ativação pós-falha requer processamento adicional, que demora cerca de 8 a 10 minutos a concluir. Poderá reparar os tempos de ativação pós-falha para:

* Executar uma versão de serviço de mobilidade com mais de 9.8 VMs de VMware.
* Servidores físicos.
* VMs do Linux do VMware.
* VMs de Hyper-V protegido como servidores físicos.
* VMs de VMware que não têm o serviço DHCP ativado.
* VMs de VMware que não têm os controladores de arranque: storvsc, vmbus, storflt, intelide e atapi.

> [!WARNING]
> Não cancele uma ativação pós-falha em curso. Antes de iniciar a ativação pós-falha, a replicação de VM é interrompida. Se cancelar uma ativação pós-falha que esteja em curso, a mesma para, mas a VM não será replicada outra vez.

## <a name="connect-to-failed-over-vm"></a>Ligar à VM com ativação pós-falha

1. Se pretender ligar a VMs do Azure após a ativação pós-falha com o protocolo RDP (Remote Desktop) e o Secure Shell (SSH), [verificar se foram cumpridos os requisitos](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
2. Após a ativação pós-falha, vá para a VM e validar [ligar](../virtual-machines/windows/connect-logon.md) a ele.
3. Utilizar **alterar ponto de recuperação** se pretender utilizar um ponto de recuperação diferente após a ativação pós-falha. Depois de realizar a ativação pós-falha no próximo passo, esta opção já não estará disponível.
4. Após a validação, selecione **consolidar** para finalizar o ponto de recuperação da VM após a ativação pós-falha.
5. Depois de consolidação, são eliminados todos os outros pontos de recuperação disponíveis. Este passo conclui a ativação pós-falha.

>[!TIP]
> Se tiver quaisquer problemas de conectividade após a ativação pós-falha, siga os [guia de resolução de problemas](site-recovery-failover-to-azure-troubleshoot.md).

## <a name="next-steps"></a>Passos Seguintes

Após a ativação pós-falha, voltar a proteger as VMs do Azure para o local. Em seguida, depois das VMs estiverem a ser replicadas para o site no local e protegida, reativação pós-falha do Azure quando estiver pronto.

> [!div class="nextstepaction"]
> [Voltar a proteger as VMs do Azure](vmware-azure-reprotect.md)
> [reativação pós-falha do Azure](vmware-azure-failback.md)
