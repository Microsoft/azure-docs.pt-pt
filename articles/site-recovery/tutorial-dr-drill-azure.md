---
title: Executar um teste de recuperação após desastre para máquinas no local para o Azure com o Azure Site Recovery | Microsoft Docs
description: Saiba mais sobre como executar um teste de recuperação após desastre no local para o Azure com o Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: b93fb92c9170f3e0fb7bd6ee754dde5df729e299
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60925769"
---
# <a name="run-a-disaster-recovery-drill-to-azure"></a>Executar um teste de recuperação após desastre para o Azure

Este artigo descreve como executar um teste de recuperação após desastre para uma máquina no local para o Azure com o [do Azure Site Recovery](site-recovery-overview.md) serviço. Um teste valida a estratégia de replicação sem perda de dados.


Este é o quarto tutorial de uma série que mostra-lhe como configurar a recuperação após desastre para o Azure para máquinas no local.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar uma rede isolada para ativação pós-falha de teste
> * Preparar a ligação para a VM do Azure após a ativação pós-falha
> * Execute uma ativação pós-falha para uma única máquina.

> [!NOTE]
> Tutoriais mostram-lhe o caminho de implantação mais simples para um cenário. Utilizam opções predefinidas sempre que possível e não mostram todas as definições e caminhos possíveis. Se quiser saber mais sobre os passos de exploração de recuperação após desastre mais detalhadamente, [rever este artigo](site-recovery-test-failover-to-azure.md).

## <a name="before-you-start"></a>Antes de começar

Conclua os tutoriais anteriores:

1. Certifique-se de que [configurar o Azure](tutorial-prepare-azure.md) para recuperação de desastre no local de VMs de VMware, VMs Hyper-V e máquinas físicas para o Azure.
2. Preparar o ambiente [VMware](vmware-azure-tutorial-prepare-on-premises.md) ou [Hyper-V](hyper-v-prepare-on-premises-tutorial.md) ambiente para recuperação após desastre. Se estiver configurando a recuperação após desastre para servidores físicos, reveja os [matriz de suporte](vmware-physical-secondary-support-matrix.md).
3. Configurar a recuperação após desastre para [VMs de VMware](vmware-azure-tutorial.md), [VMs de Hyper-V](hyper-v-azure-tutorial.md), ou [máquinas físicas](physical-azure-disaster-recovery.md).
 

## <a name="verify-vm-properties"></a>Verificar as propriedades da VM

Antes de executar uma ativação pós-falha de teste, verifique as propriedades da VM e verifique se a [VM Hyper-V](hyper-v-azure-support-matrix.md#replicated-vms) ou a [VM VMware](vmware-physical-azure-support-matrix.md#replicated-machines) está em conformidade com os requisitos do Azure.

1. Em **Itens Protegidos**, clique em **Itens Replicados** > e na VM.
2. No painel **Item replicado**, existe um resumo das informações, do estado de funcionamento e dos pontos de recuperação mais recentes disponíveis da VM. Clique em **Propriedades** para ver mais detalhes.
3. Em **Computação e Rede**, pode modificar o nome do Azure, o grupo de recursos, o tamanho de destino, o conjunto de disponibilidade e as definições do disco gerido.
4. Pode ver e modificar as definições de rede, incluindo a rede/sub-rede na qual a VM do Azure será localizada após a ativação pós-falha e o endereço IP que será atribuído à mesma.
5. Em **Discos**, pode ver informações sobre o sistema operativo e os discos de dados na VM.

## <a name="create-a-network-for-test-failover"></a>Criar uma rede para ativação pós-falha de teste

Recomendamos que, para a ativação pós-falha de teste, escolha uma rede isolada da rede do site de recuperação de produção específica nas definições **Computação e Rede** para cada VM. Por predefinição, quando cria uma rede virtual do Azure, esta fica isolada das outras redes. A rede de teste deve imitar a sua rede de produção:

- A rede de teste deve ter o mesmo número de sub-redes que a sua rede de produção. As sub-redes devem ter os mesmos nomes.
- A rede de teste deve utilizar o mesmo intervalo de endereços IP.
- Atualize o DNS da rede de teste com o endereço IP especificado para a VM de DNS nas definições **Computação e Rede**. Leia as [considerações sobre a ativação pós-falha do Active Directory](site-recovery-active-directory.md#test-failover-considerations) para obter mais detalhes.

## <a name="run-a-test-failover-for-a-single-vm"></a>Executar uma ativação pós-falha de teste para uma única VM

Quando executa uma ativação pós-falha de teste, ocorre o seguinte:

1. É executada uma verificação dos pré-requisitos, para garantir que estão satisfeitas todas as condições necessárias para a ativação pós-falha.
2. A ativação pós-falha processa os dados, para que possa ser criada uma VM do Azure. Se selecionar o último ponto de recuperação, será criado um ponto de recuperação a partir dos dados.
3. É criada uma VM do Azure com base nos dados processados no passo anterior.

Execute a ativação pós-falha de teste da seguinte forma:

1. Em **Definições** > **Itens Replicados**, clique na VM > **+Testar Ativação Pós-falha**.
2. Selecione o ponto de recuperação **Processado mais recentemente** para este tutorial. Este procedimento efetua a ativação pós-falha da VM para o ponto no tempo mais recente disponível. O carimbo de data/hora é apresentado. Com esta opção, não é despendido tempo a processar os dados, pelo que oferece um RTO (objetivo de tempo de recuperação) baixo.
3. Em **Ativação Pós-Falha de Teste**, selecione a rede do Azure de destino para as VMs do Azure que serão ligadas após a ocorrência da ativação pós-falha.
4. Clique em **OK** para iniciar a ativação pós-falha. Pode clicar na VM e abrir as respetivas propriedades para acompanhar o progresso. Também pode clicar na tarefa **Ativação Pós-falha de Teste** no nome do cofre > **Definições** > **Tarefas** >
   **Tarefas do Site Recovery**.
5. Após a conclusão da ativação pós-falha, a VM do Azure de réplica é apresentada no portal do Azure > **Máquinas Virtuais**. Verifique que a VM tem o tamanho adequado, está ligada à rede certa e está em execução.
6. Deverá conseguir ligar-se à VM replicada no Azure agora.
7. Para eliminar as VMs do Azure criadas durante a ativação pós-falha de teste, clique em **Limpar ativação pós-falha de teste** na VM. Em **Notas**, registe e guarde todas as observações associadas à ativação pós-falha de teste.

Em alguns cenários, a ativação pós-falha requer processamento adicional, que demora cerca de oito a dez minutos a concluir. Poderá reparar em tempos de ativação pós-falha superiores para máquinas do Linux VMware, VMs VMware que não têm o serviço DHCP ativado e VMs VMware que não têm os controladores de arranque storvsc, vmbus, storflt, intelide e atapi.

## <a name="connect-after-failover"></a>Ligar após a ativação pós-falha

Se pretender ligar a VMs do Azure com RDP/SSH após a ativação pós-falha, [preparar a ligação](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover). Se tiver quaisquer problemas de conectividade após a ativação pós-falha, siga os [resolução de problemas](site-recovery-failover-to-azure-troubleshoot.md) guia.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Executar uma ativação pós-falha e reativação pós-falha para VMs de VMware](vmware-azure-tutorial-failover-failback.md).
> [Executar uma ativação pós-falha e reativação pós-falha para VMs de Hyper-V](hyper-v-azure-failover-failback-tutorial.md).
> [Executar uma ativação pós-falha e reativação pós-falha para máquinas físicas](physical-to-azure-failover-failback.md)
