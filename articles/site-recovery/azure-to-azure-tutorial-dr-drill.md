---
title: Faça um exercício de recuperação de desastres Azure VM com recuperação do site Azure
description: Aprenda a executar um exercício de recuperação de desastres para uma região secundária para VMs Azure usando o serviço de recuperação de locais Azure.
services: site-recovery
ms.topic: tutorial
ms.date: 01/16/2020
ms.custom: mvc
ms.openlocfilehash: b2ce157f0f192135ab0507e4aae4c0a282bda1ea
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "76166181"
---
# <a name="run-a-disaster-recovery-drill-to-a-secondary-region-for-azure-vms"></a>Faça um exercício de recuperação de desastres para uma região secundária para VMs Azure

O serviço [Azure Site Recovery](site-recovery-overview.md) contribui para a sua estratégia de continuidade comercial e recuperação após desastre (BCDR) ao manter as suas aplicações empresariais em funcionamento e disponíveis durante falhas planeadas e não planeadas. O Site Recovery gere e orquestra a recuperação após desastre de computadores no local e máquinas virtuais (VMs) do Azure, incluindo replicação, ativação pós-falha e recuperação.

Este tutorial mostra como executar um teste de recuperação após desastre para uma VM do Azure, de uma região do Azure para outra, com uma ativação pós-falha de teste. Um teste valida a sua estratégia de replicação sem perda de dados ou tempo de inatividade e não afeta o seu ambiente de produção. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Verificar os pré-requisitos
> * Executar uma ativação pós-falha de teste para uma única VM

> [!NOTE]
> Este tutorial ajuda-o a realizar um exercício de recuperação de desastres com passos mínimos. Para saber mais sobre as várias funções relacionadas com a realização de um exercício de recuperação de desastres, consulte a documentação para a [replicação](azure-to-azure-how-to-enable-replication.md)de VMs Azure, [networking,](azure-to-azure-about-networking.md) [automação](azure-to-azure-powershell.md)ou resolução de [problemas.](azure-to-azure-troubleshoot-errors.md)

## <a name="prerequisites"></a>Pré-requisitos

Verifique os seguintes itens antes de fazer este tutorial:

- Antes de executar um teste failover, recomendamos que verifique as propriedades do VM para se certificar de que está configurado para a recuperação de desastres. Vá às**Propriedades** de**Recuperação** > de Desastres de **Operações** > da VM para ver as propriedades de replicação e failover.
- **Recomendamos que utilize uma rede Azure VM separada para a falha do teste**, e não a rede predefinida que foi criada quando ativou a replicação.
- Dependendo das configurações de rede de origem para cada NIC, pode especificar **Subnet**, **endereço IP privado,** **IP público,** grupo de segurança de **rede**ou **balanceador** de carga para anexar a cada NIC em configurações de falha de teste em **Compute e Network** antes de fazer um exercício de recuperação de desastres.

## <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste

Este exemplo mostra como usar um cofre de Serviços de Recuperação para fazer um teste VM failover.

1. Selecione um cofre e vá a **itens protegidos** > **Itens replicados** e selecione um VM.
1. No **Teste Failover,** selecione um ponto de recuperação para utilizar para a falha:
   - **Mais recente**: Processa todos os dados na Recuperação do Site e fornece o RTO mais baixo (Objetivo do Tempo de Recuperação).
   - **Processado mais recentemente**: faz a ativação pós-falha da VM para o ponto de recuperação mais recente processado pelo Site Recovery. O carimbo de data/hora é apresentado. Com esta opção, não é gasto tempo a processar dados, pelo que proporciona um RTO baixo.
   - **Consistente com a aplicação mais recente**: esta opção faz a ativação pós-falha de todas as VMs para o último ponto de recuperação consistente com a aplicação. O carimbo de data/hora é apresentado.
   - **Personalizado**: Falhe no ponto de recuperação específico. O costume só está disponível quando falha num único VM, e não por falhas com um plano de recuperação.
1. Selecione a rede virtual Azure-alvo a que os VMs Azure na região secundária se ligarão após a falha.

   > [!NOTE]
   > Se as definições de failover do teste forem pré-configuradas para o item replicado, o menu de dropdown para selecionar uma rede virtual Azure não é visível.

1. Para iniciar a falha, selecione **OK**. Para acompanhar o progresso do cofre, vá a trabalhos de **monitorização** > de**recuperação** do local e selecione o trabalho de Failover de **Teste.**
1. Após o acabamento da falha, a réplica Azure VM aparece nas **Máquinas Virtuais**do portal Azure. Certifique-se de que a VM está em execução, tem as dimensões adequadas e está ligada à rede devida.
1. Para eliminar os VMs que foram criados durante a falha do teste, **selecione falha** no teste de limpeza no item replicado ou no plano de recuperação. Em **Notas,** grave e guarde quaisquer observações associadas ao failover do teste.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Executar uma ativação pós-falha ](azure-to-azure-tutorial-failover-failback.md)
