---
title: Executar uma análise de recuperação de desastre de VM do Azure com Azure Site Recovery
description: Saiba como executar uma análise de recuperação de desastre em uma região secundária para VMs do Azure usando o serviço de Azure Site Recovery.
services: site-recovery
ms.topic: tutorial
ms.date: 01/16/2020
ms.custom: mvc
ms.openlocfilehash: b2ce157f0f192135ab0507e4aae4c0a282bda1ea
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76166181"
---
# <a name="run-a-disaster-recovery-drill-to-a-secondary-region-for-azure-vms"></a>Executar uma análise de recuperação de desastre em uma região secundária para VMs do Azure

O serviço [Azure Site Recovery](site-recovery-overview.md) contribui para a sua estratégia de continuidade comercial e recuperação após desastre (BCDR) ao manter as suas aplicações empresariais em funcionamento e disponíveis durante falhas planeadas e não planeadas. O Site Recovery gere e orquestra a recuperação após desastre de computadores no local e máquinas virtuais (VMs) do Azure, incluindo replicação, ativação pós-falha e recuperação.

Este tutorial mostra como executar um teste de recuperação após desastre para uma VM do Azure, de uma região do Azure para outra, com uma ativação pós-falha de teste. Um teste valida a sua estratégia de replicação sem perda de dados ou tempo de inatividade e não afeta o seu ambiente de produção. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Verificar os pré-requisitos
> * Executar uma ativação pós-falha de teste para uma única VM

> [!NOTE]
> Este tutorial ajuda você a executar uma análise de recuperação de desastre com etapas mínimas. Para saber mais sobre as várias funções relacionadas à realização de uma análise de recuperação de desastres, consulte a documentação de [replicação](azure-to-azure-how-to-enable-replication.md), [rede](azure-to-azure-about-networking.md), [automação](azure-to-azure-powershell.md)ou [solução de problemas](azure-to-azure-troubleshoot-errors.md)de VMs do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Verifique os seguintes itens antes de fazer este tutorial:

- Antes de executar um failover de teste, recomendamos que você verifique as propriedades da VM para certificar-se de que ela esteja configurada para recuperação de desastre. Acesse as **Propriedades** da **VM** > **recuperação de desastre** > para exibir as propriedades de replicação e failover.
- **Recomendamos que utilize uma rede de VMs do Azure na ativação pós-falha de teste**, e não a rede predefinida que foi configurada quando ativou a replicação.
- Dependendo das configurações de rede de origem para cada NIC, você pode especificar a **sub-rede**, o **endereço IP privado**, o **IP público**, o **grupo de segurança de rede**ou o **balanceador de carga** para anexar a cada NIC em configurações de failover de teste em **computação e rede** antes de fazer uma análise de recuperação de desastre.

## <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste

Este exemplo mostra como usar um cofre de serviços de recuperação para fazer um failover de teste de VM.

1. Selecione um cofre e vá para **itens protegidos** > **itens replicados** e selecione uma VM.
1. Em **failover de teste**, selecione um ponto de recuperação a ser usado para o failover:
   - **Mais recente**: processa todos os dados em site Recovery e fornece o RTO mais baixo (objetivo de tempo de recuperação).
   - **Processado mais recentemente**: faz a ativação pós-falha da VM para o ponto de recuperação mais recente processado pelo Site Recovery. O carimbo de data/hora é apresentado. Com essa opção, nenhum tempo é gasto processando dados, portanto, ele fornece um RTO baixo.
   - **Consistente com a aplicação mais recente**: esta opção faz a ativação pós-falha de todas as VMs para o último ponto de recuperação consistente com a aplicação. O carimbo de data/hora é apresentado.
   - **Personalizado**: failover para um ponto de recuperação específico. O personalizado só está disponível quando você faz failover de uma única VM e não para failover com um plano de recuperação.
1. Selecione a rede virtual do Azure de destino à qual as VMs do Azure na região secundária serão conectadas após o failover.

   > [!NOTE]
   > Se as configurações de failover de teste forem pré-configuradas para o item replicado, o menu suspenso para selecionar uma rede virtual do Azure não será visível.

1. Para iniciar o failover, selecione **OK**. Para acompanhar o progresso do cofre, acesse **monitoramento** > **trabalhos de site Recovery** e selecione o trabalho de failover de **teste** .
1. Depois que o failover for concluído, a VM do Azure de réplica aparecerá nas **máquinas virtuais**do portal do Azure. Certifique-se de que a VM está em execução, tem as dimensões adequadas e está ligada à rede devida.
1. Para excluir as VMs que foram criadas durante o failover de teste, selecione **limpar failover de teste** no item replicado ou no plano de recuperação. Em **Notas**, registe e guarde todas as observações associadas à ativação pós-falha de teste.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Executar uma ativação pós-falha ](azure-to-azure-tutorial-failover-failback.md)
