---
title: Executar um teste de recuperação após desastre de VMs do Azure para uma região secundária do Azure com o serviço Azure Site Recovery
description: Saiba como executar um teste de recuperação após desastre de VMs do Azure para uma região secundária do Azure para VMs IaaS do Azure através do serviço Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: f85dd3abae8f6e4b3ccc10654e6da8363e80b3d3
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72968281"
---
# <a name="run-a-disaster-recovery-drill-for-azure-vms-to-a-secondary-azure-region"></a>Executar um teste de recuperação após desastre de VMs do Azure para uma região secundária do Azure

O serviço [Azure Site Recovery](site-recovery-overview.md) contribui para a sua estratégia de continuidade comercial e recuperação após desastre (BCDR) ao manter as suas aplicações empresariais em funcionamento e disponíveis durante falhas planeadas e não planeadas. O Site Recovery gere e orquestra a recuperação após desastre de computadores no local e máquinas virtuais (VMs) do Azure, incluindo replicação, ativação pós-falha e recuperação.

Este tutorial mostra como executar um teste de recuperação após desastre para uma VM do Azure, de uma região do Azure para outra, com uma ativação pós-falha de teste. Um teste valida a sua estratégia de replicação sem perda de dados ou tempo de inatividade e não afeta o seu ambiente de produção. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Verificar os pré-requisitos
> * Executar uma ativação pós-falha de teste para uma única VM

> [!NOTE]
> Este tutorial destina-se a orientar o utilizador através de passos para efetuar um teste de recuperação após desastre com poucos passos; caso deseje saber mais sobre os vários aspetos associados a um teste de recuperação após desastre, incluindo as considerações de rede, a automatização ou a resolução de problemas, veja os documentos em "Procedimentos" para VMs do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Antes de executar uma ativação pós-falha de teste, recomendamos que verifique as propriedades da VM para garantir que funciona tudo conforme o esperado.  Aceda às propriedades da VM nos **Itens replicados**. O painel **Informações Básicas** mostra informações sobre estados e definições de computadores.
- **Recomendamos que utilize uma rede de VMs do Azure na ativação pós-falha de teste**, e não a rede predefinida que foi configurada quando ativou a replicação.
- Dependendo das configurações de rede de origem para cada NIC, opcionalmente, você pode especificar a **sub-rede, o endereço IP, o IP público, o grupo de segurança de rede ou o Load balancer interno** para anexar a cada NIC em configurações de failover de teste em computação & rede anterior para conduzir a análise de DR.


## <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste

1. Em **Definições** > **Itens Replicados**, clique na VM **+Testar Ativação Pós-falha**.

2. Em **Ativação Pós-falha**, selecione um ponto de recuperação para utilizar na ativação pós-falha:

   - **Processado mais recentemente**: faz a ativação pós-falha da VM para o ponto de recuperação mais recente processado pelo serviço do Site Recovery. O carimbo de data/hora é apresentado. Com esta opção, não é despendido tempo a processar os dados, pelo que oferece um RTO (Objetivo de Tempo de Recuperação) baixo
   - **Consistente com a aplicação mais recente**: esta opção faz a ativação pós-falha de todas as VMs para o último ponto de recuperação consistente com a aplicação. O carimbo de data/hora é apresentado.
   - **Personalizado**: selecione qualquer ponto de recuperação.

3. Selecione a rede virtual de destino à qual as VMs do Azure na região secundária serão ligadas, após a ativação pós-falha ocorrer.

    > [!NOTE]
    > A lista suspensa para selecionar a rede virtual do Azure não estará visível se as configurações de failover de teste estiverem pré-configuradas para o item replicado.

4. Para iniciar a ativação pós-falha, clique em **OK**. Para acompanhar o progresso, clique em VM para abrir as respetivas propriedades. Também pode clicar na tarefa **Ativação Pós-falha de Teste** no nome do cofre > **Definições** > **Tarefas** > **Tarefas do Site Recovery**.
5. Após a conclusão da ativação pós-falha, a VM do Azure de réplica é apresentada no portal do Azure > **Máquinas Virtuais**. Certifique-se de que a VM está em execução, tem as dimensões adequadas e está ligada à rede devida.
6. Para eliminar as VMs que foram criadas durante a ativação pós-falha de teste, clique em **Limpar ativação pós-falha de teste** no item replicado ou no plano de recuperação. Em **Notas**, registe e guarde todas as observações associadas à ativação pós-falha de teste.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Executar uma ativação pós-falha ](azure-to-azure-tutorial-failover-failback.md)
