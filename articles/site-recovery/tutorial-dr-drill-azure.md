---
title: Executar uma análise de recuperação de desastre no Azure com Azure Site Recovery
description: Saiba como executar uma análise de recuperação de desastre do local para o Azure, com Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 5bd9f5316f8b8799633de8c0c84c61424c0e4f4a
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954425"
---
# <a name="run-a-disaster-recovery-drill-to-azure"></a>Executar um teste de recuperação após desastre para o Azure

Este artigo descreve como executar uma análise de recuperação de desastre para um computador local para o Azure usando o serviço [Azure site Recovery](site-recovery-overview.md) . Um teste valida a estratégia de replicação sem perda de dados.


Este é o quarto tutorial de uma série que mostra como configurar a recuperação de desastres para o Azure para computadores locais.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar uma rede isolada para ativação pós-falha de teste
> * Preparar a ligação para a VM do Azure após a ativação pós-falha
> * Execute um failover de teste para um único computador.

> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário. Utilizam opções predefinidas sempre que possível e não mostram todas as definições e caminhos possíveis. Se você quiser saber mais sobre as etapas de análise de recuperação de desastre em mais detalhes, [Leia este artigo](site-recovery-test-failover-to-azure.md).

## <a name="before-you-start"></a>Antes de começar

Conclua os tutoriais anteriores:

1. Verifique se você [configurou o Azure](tutorial-prepare-azure.md) para a recuperação de desastre local de VMs VMware, VMS do Hyper-V e máquinas físicas para o Azure.
2. Prepare seu ambiente do [VMware](vmware-azure-tutorial-prepare-on-premises.md) ou [Hyper-V](hyper-v-prepare-on-premises-tutorial.md) local para recuperação de desastres. Se você estiver configurando a recuperação de desastre para servidores físicos, examine a [matriz de suporte](vmware-physical-secondary-support-matrix.md).
3. Configure a recuperação de desastre para [VMs VMware](vmware-azure-tutorial.md), [VMs Hyper-V](hyper-v-azure-tutorial.md)ou [máquinas físicas](physical-azure-disaster-recovery.md).
 

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

## <a name="connect-after-failover"></a>Conectar após o failover

Se você quiser se conectar a VMs do Azure usando RDP/SSH após o failover, [Prepare-se para conectar](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover). Se você encontrar problemas de conectividade após o failover, siga o guia de [solução de problemas](site-recovery-failover-to-azure-troubleshoot.md) .

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Execute um failover e um failback para VMs VMware](vmware-azure-tutorial-failover-failback.md)
> [executar um failover e um failback para VMs do Hyper-V](hyper-v-azure-failover-failback-tutorial.md)
> [executar um failover e um failback para computadores físicos](physical-to-azure-failover-failback.md)
