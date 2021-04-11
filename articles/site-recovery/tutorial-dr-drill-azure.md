---
title: Faça um exercício de recuperação de desastres para Azure com recuperação do local de Azure
description: Aprenda a executar um exercício de recuperação de desastres de no local para Azure, com a Recuperação do Local de Azure.
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.custom: MVC
ms.openlocfilehash: 246295017e2b05de10ccfd2154a1490ae0ece9c3
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579106"
---
# <a name="run-a-disaster-recovery-drill-to-azure"></a>Executar um teste de recuperação após desastre para o Azure

Este artigo descreve como executar um exercício de recuperação de desastres para uma máquina no local para Azure usando o serviço [de recuperação do local de Azure.](site-recovery-overview.md) Um teste valida a estratégia de replicação sem perda de dados.


Este é o quarto tutorial de uma série que mostra como configurar a recuperação de desastres para Azure para máquinas no local.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar uma rede isolada para ativação pós-falha de teste
> * Preparar a ligação para a VM do Azure após a ativação pós-falha
> * Executar um teste de falha para uma única máquina.

> [!NOTE]
> Os tutoriais mostram-lhe o caminho de implantação mais simples para um cenário. Utilizam opções predefinidas sempre que possível e não mostram todas as definições e caminhos possíveis. Se quiser saber mais pormenorizadamente os passos da broca de recuperação de desastres, [reveja este artigo](site-recovery-test-failover-to-azure.md).

## <a name="before-you-start"></a>Antes de começar

Complete os tutoriais anteriores:

1. Certifique-se de que [criou o Azure](tutorial-prepare-azure.md) para a recuperação de desastres no local de VMware VMs, VMs hiper-V e máquinas físicas para Azure.
2. Prepare o ambiente [VMware](vmware-azure-tutorial-prepare-on-premises.md) ou [Hiper-V](hyper-v-prepare-on-premises-tutorial.md) no local para a recuperação de desastres. Se estiver a configurar a recuperação de desastres para servidores físicos, reveja a [matriz de suporte](vmware-physical-secondary-support-matrix.md).
3. Configurar a recuperação de desastres para [VMware VMs,](vmware-azure-tutorial.md) [Hiper-VMs,](hyper-v-azure-tutorial.md)ou [máquinas físicas](physical-azure-disaster-recovery.md).
 

## <a name="verify-vm-properties"></a>Verificar as propriedades da VM

Antes de executar uma ativação pós-falha de teste, verifique as propriedades da VM e verifique se a [VM Hyper-V](hyper-v-azure-support-matrix.md#replicated-vms) ou a [VM VMware](vmware-physical-azure-support-matrix.md#replicated-machines) está em conformidade com os requisitos do Azure.

1. Em **Itens Protegidos**, clique em **Itens Replicados** > e na VM.
2. No painel **Item replicado**, existe um resumo das informações, do estado de funcionamento e dos pontos de recuperação mais recentes disponíveis da VM. Clique em **Propriedades** para ver mais detalhes.
3. No **Compute and Network,** pode modificar o nome Azure, grupo de recursos, tamanho alvo, conjunto de disponibilidade e definições de disco gerido.
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

1. Em **Definições**  >  **Itens Replicados,** clique no VM > **+Test Failover**.
2. Selecione o ponto de recuperação **Processado mais recentemente** para este tutorial. Este procedimento efetua a ativação pós-falha da VM para o ponto no tempo mais recente disponível. O carimbo de data/hora é apresentado. Com esta opção, não é despendido tempo a processar os dados, pelo que oferece um RTO (objetivo de tempo de recuperação) baixo.
3. Em **Ativação Pós-Falha de Teste**, selecione a rede do Azure de destino para as VMs do Azure que serão ligadas após a ocorrência da ativação pós-falha.
4. Clique em **OK** para iniciar a ativação pós-falha. Pode clicar na VM e abrir as respetivas propriedades para acompanhar o progresso. Também pode clicar na tarefa **Ativação Pós-falha de Teste** no nome do cofre > **Definições** > **Tarefas** >
   **Tarefas do Site Recovery**.
5. Após a conclusão da ativação pós-falha, a VM do Azure de réplica é apresentada no portal do Azure > **Máquinas Virtuais**. Verifique que a VM tem o tamanho adequado, está ligada à rede certa e está em execução.
6. Deverá conseguir ligar-se à VM replicada no Azure agora.
7. Para eliminar as VMs do Azure criadas durante a ativação pós-falha de teste, clique em **Limpar ativação pós-falha de teste** na VM. Em **Notas,** registem e guarde quaisquer observações associadas ao teste.

Em alguns cenários, a ativação pós-falha requer processamento adicional, que demora cerca de oito a dez minutos a concluir. Poderá reparar em tempos de ativação pós-falha superiores para máquinas do Linux VMware, VMs VMware que não têm o serviço DHCP ativado e VMs VMware que não têm os controladores de arranque storvsc, vmbus, storflt, intelide e atapi.

## <a name="connect-after-failover"></a>Conecte-se após falha

Se pretender ligar-se aos VMs Azure utilizando RDP/SSH após a falha, [prepare-se para ligar](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover). Se encontrar problemas de conectividade após o failover, siga o guia [de resolução de problemas.](site-recovery-failover-to-azure-troubleshoot.md)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Executar um failover e failback para VMware VMs](vmware-azure-tutorial-failover-failback.md) 
>  [Executar um failover e failback para Hiper-V VMs](hyper-v-azure-failover-failback-tutorial.md) 
>  [Executar um failover e failback para máquinas físicas](physical-to-azure-failover-failback.md)
