---
title: Falha sobre VMware Solução VMware Azure para Azure com recuperação de site
description: Saiba como falhar sobre Azure VMware Solution VMs para Azure na Recuperação do Site Azure
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: 60c268ba837540eda86a4cbaf6e0ab1c425d90b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91814582"
---
# <a name="fail-over--azure-vmware-solution-vms"></a>Falha em VMs de Solução VMware Azure

Este artigo descreve como falhar sobre uma Solução VMware VMware Azure para Azure com [Azure Site Recovery](site-recovery-overview.md).

Este é o quinto tutorial de uma série que mostra como configurar a recuperação de desastres para Azure para VMware Solução VMware.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]

> * Verifique se as propriedades VMware Solução VMware Azure estão em conformidade com os requisitos do Azure.
> * Falha em VMs específicos para Azure.

> [!NOTE]
> Os tutoriais mostram-lhe o caminho de implantação mais simples para um cenário. Utilizam opções padrão sempre que possível e não mostram todas as definições e caminhos possíveis. Se quiser aprender em detalhe sobre o failover, consulte [Fail over VMs](site-recovery-failover.md).

[Saiba mais sobre](failover-failback-overview.md#types-of-failover) diferentes tipos de falha. Se quiser falhar em vários VMs num plano de recuperação, [reveja este artigo](site-recovery-failover.md).

## <a name="before-you-start"></a>Antes de começar

Complete os tutoriais anteriores:

1. Certifique-se de [ter preparado a Azure](avs-tutorial-prepare-azure.md) para a recuperação de desastres para Azure.
2. Siga [estes passos](avs-tutorial-prepare-avs.md) para preparar a sua implementação da Solução Azure VMware para a recuperação de desastres para Azure.
3. [Confiúdo](avs-tutorial-replication.md) de recuperação de desastres para VMS de Solução VMware Azure.
4. Faça um [exercício de recuperação](avs-tutorial-dr-drill-azure.md) de desastres para garantir que tudo está funcionando como esperado.

## <a name="verify-vm-properties"></a>Verificar as propriedades da VM

Antes de executar um failover, verifique as propriedades VM para se certificar de que os VM satisfazem os [requisitos de Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

Verifique as propriedades da seguinte forma:

1. Em **Itens Protegidos,** selecione **Itens Replicados**e, em seguida, selecione o VM que pretende verificar.

2. No painel **Item replicado**, existe um resumo das informações, do estado de funcionamento e dos pontos de recuperação mais recentes disponíveis da VM. Selecione **Propriedades** para ver mais detalhes.

3. No **Compute and Network,** pode modificar estas propriedades conforme necessário:
    * Nome azul
    * Grupo de recursos
    * Tamanho do alvo
    * [Conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md)
    * Definições de disco gerido

4. Pode visualizar e modificar as definições de rede, incluindo:

    * A rede e a sub-rede em que o Azure VM será localizado após a falha.
    * O endereço IP que lhe será atribuído.

5. Em **Discos**, pode ver informações sobre o sistema operativo e os discos de dados na VM.

## <a name="run-a-failover-to-azure"></a>Executar uma ativação pós-falha para o Azure

1. Em **Definições**  >  **Itens Replicados**, selecione o VM que pretende falhar e, em seguida, selecione **Failover**.
2. Em **Ativação pós-falha**, selecione um **Ponto de Recuperação** para o qual irá realizar a ativação pós-falha. Pode utilizar uma das opções seguintes:
   * **Mais recente**: esta opção processa primeiro todos os dados enviados para o Site Recovery. Fornece o menor Objetivo de Ponto de Recuperação (RPO) porque o VM Azure que é criado após o failover tem todos os dados que foram replicados para a Recuperação do Local quando a falha foi desencadeada.
   * **Processo mais recente**: Esta opção falha o VM até ao último ponto de recuperação processado pela Recuperação do Site. Esta opção fornece um BAIXO RTO (Objetivo do Tempo de Recuperação) porque não é gasto tempo a processar dados não processados.
   * **Aplicações mais recentes consistentes**: Esta opção falha o VM para o mais recente ponto de recuperação consistente da aplicação processado pela Recuperação do Site.
   * **Costume**: Esta opção permite especificar um ponto de recuperação.

3. Selecione **desligar a máquina antes de começar** a falhar para tentar desligar os VMs de origem antes de ativar a falha. A falha continua mesmo que a paralisação falhe. Pode acompanhar o progresso falhado na página **Jobs.**

Em alguns cenários, o failover requer um processamento adicional que leva cerca de 8 a 10 minutos para ser concluído. Pode notar tempos de insuição de teste mais longos para:

* VMware VMs executando uma versão de serviço de mobilidade com mais de 9.8.
* VMware Linux VMs.
* VMware VMs que não têm o serviço DHCP habilitado.
* VMware VMs que não têm os seguintes controladores de arranque: storvsc, vmbus, storflt, intelide, atapi.

> [!WARNING]
> Não cancele um fracasso em curso. Antes de iniciar a ativação pós-falha, a replicação de VM é interrompida. Se cancelar uma ativação pós-falha que esteja em curso, a mesma para, mas a VM não será replicada outra vez.

## <a name="connect-to-failed-over-vm"></a>Ligue-se a VM falhado

1. Se pretender ligar-se aos VMs Azure após o failover utilizando o Remote Desktop Protocol (RDP) e o Secure Shell (SSH), [verifique se os requisitos foram cumpridos](failover-failback-overview.md#connect-to-azure-after-failover).
2. Depois de falhar, vá ao VM e valide [ligando-o.](../virtual-machines/windows/connect-logon.md)
3. Utilize **o ponto de recuperação de alteração** se quiser utilizar um ponto de recuperação diferente após a falha. Depois de cometer o failover no próximo passo, esta opção deixará de estar disponível.
4. Após validação, **selecione Comprometa-se** a finalizar o ponto de recuperação do VM após o failover.
5. Depois de se comprometer, todos os outros pontos de recuperação disponíveis são eliminados. Este passo completa o fracasso.

>[!TIP]
> Se encontrar problemas de conectividade após o failover, siga o [guia de resolução de problemas](site-recovery-failover-to-azure-troubleshoot.md).

## <a name="next-steps"></a>Passos seguintes

Após o failover, reprotete os VMs Azure para a azure VMware Solution nuvem privada. Em seguida, depois de os VMs serem reprotegidos e replicarem-se na nuvem privada Azure VMware Solution, recue do Azure quando estiver pronto.


> [!div class="nextstepaction"]
> [Reprotectet Azure VMs](avs-tutorial-reprotect.md) 
>  [Recuo de Azure](avs-tutorial-failback.md)
