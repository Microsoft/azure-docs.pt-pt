---
title: Configurar a recuperação após desastre de uma VM do Azure IaaS para uma região secundária do Azure
description: Este início rápido fornece os passos necessários para recuperação após desastre de uma VM do Azure IaaS entre regiões do Azure, com o serviço Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: quickstart
ms.date: 03/12/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: f7adcb7d4516e9013bf87306fff77b566885e018
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57855287"
---
# <a name="set-up-disaster-recovery-to-a-secondary-azure-region-for-an-azure-vm"></a>Configurar a recuperação após desastre para uma região secundária do Azure de uma VM do Azure        

O serviço [Azure Site Recovery](site-recovery-overview.md) contribui para a sua estratégia de continuidade comercial e recuperação após desastre (BCDR) ao manter as suas aplicações empresariais em funcionamento e durante falhas planeadas e não planeadas. O Site Recovery gere e orquestra a recuperação após desastre de computadores no local e máquinas virtuais (VMs) do Azure, incluindo replicação, ativação pós-falha e recuperação.

Este início rápido descreve como configurar a recuperação após desastre para uma VM do Azure, ao replicá-los noutra região do Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!NOTE]
> Este artigo é como uma passo a passo rápido dos novos utilizadores. Ele usa o caminho mais simples, com a personalização de opções e o mínimo de predefinição.  Para uma revisão mais completa de instruções [nosso tutorial](azure-to-azure-tutorial-enable-replication.md).

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="enable-replication-for-the-azure-vm"></a>Ativar a replicação para a VM do Azure

1. No portal do Azure, clique em **Máquinas virtuais** e selecione a VM que pretende replicar.
2. Nas **Operações**, clique em **Recuperação após desastre**.
3. Em **Configurar recuperação após desastre** > **Região de destino**, selecione a região de destino para a qual irá replicar.
4. Para este Início Rápido, aceite as outras predefinições.
5. Clique em **Ativar replicação**. Esta ação inicia uma tarefa para ativar a replicação para a VM.

    ![ativar replicação](media/azure-to-azure-quickstart/enable-replication1.png)

## <a name="verify-settings"></a>Verificar as definições

Após a tarefa de replicação ser concluída, pode ver o estado da replicação, modificar a replicação e testar a implementação.

1. No menu VM, clique em **Recuperação após desastre**.
2. Pode verificar o estado de funcionamento da replicação, os pontos de recuperação que foram criados e as regiões de origem e destino num mapa.

   ![Estado de replicação](media/azure-to-azure-quickstart/replication-status.png)

## <a name="clean-up-resources"></a>Limpar recursos

A VM na região primária para de replicar quando desativa a replicação na mesma:

- As definições de replicação da origem são limpas automaticamente. A extensão de recuperação de sites instalada na VM como parte da replicação não é removida e têm de ser removida manualmente. 
- Interrompe a faturação do site Recovery para a VM.

Parar a replicação da seguinte forma

1. Selecione a VM.
2. Na **Recuperação após desastre**, clique em **Desativar Replicação**.

   ![Desativar a replicação](media/azure-to-azure-quickstart/disable2-replication.png)

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, replicou uma única VM numa região secundária. Agora, tente replicar um várias VMs do Azure com um plano de recuperação.

> [!div class="nextstepaction"]
> [Configurar a recuperação após desastre para VMs do Azure](azure-to-azure-tutorial-enable-replication.md)
