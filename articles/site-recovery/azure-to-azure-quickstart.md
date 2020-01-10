---
title: Configurar a recuperação de desastre de VM do Azure para uma região secundária com Azure Site Recovery
description: Configure rapidamente a recuperação de desastres para outra região do Azure para uma VM do Azure, usando o serviço Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: quickstart
ms.date: 01/08/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: de4d3ce11e23d7ec4f6ad26852e7d7d01eebe590
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75780016"
---
# <a name="set-up-disaster-recovery-to-a-secondary-azure-region-for-an-azure-vm"></a>Configurar a recuperação após desastre para uma região secundária do Azure de uma VM do Azure

O serviço [Azure Site Recovery](site-recovery-overview.md) contribui para a sua estratégia de continuidade comercial e recuperação após desastre (BCDR) ao manter as suas aplicações empresariais em funcionamento e durante falhas planeadas e não planeadas. O Site Recovery gere e orquestra a recuperação após desastre de computadores no local e máquinas virtuais (VMs) do Azure, incluindo replicação, ativação pós-falha e recuperação.

Este guia de início rápido descreve como configurar a recuperação de desastre para uma VM do Azure replicando-a para uma região diferente do Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!NOTE]
> Este artigo é uma rápida explicação para novos usuários. Ele usa o caminho mais simples, com opções padrão e personalização mínima. Para obter uma explicação completa, consulte o tutorial [habilitar a replicação](azure-to-azure-tutorial-enable-replication.md).

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com).

## <a name="enable-replication-for-the-azure-vm"></a>Ativar a replicação para a VM do Azure

1. No menu portal do Azure, selecione **máquinas virtuais**ou pesquise e selecione *máquinas virtuais* em qualquer página. Selecione a VM que você deseja replicar.
2. Em **Operações**, selecione **Recuperação após desastre**.
3. Em **Configurar recuperação após desastre** > **Região de destino**, selecione a região de destino para a qual irá replicar.
4. Para este Início Rápido, aceite as outras predefinições.
5. Selecione **revisar + iniciar replicação**. Em seguida, selecione **Iniciar replicação** para iniciar um trabalho para habilitar a replicação para a VM.

   ![ativar replicação](media/azure-to-azure-quickstart/enable-replication1.png)

## <a name="verify-settings"></a>Verificar as definições

Após a tarefa de replicação ser concluída, pode ver o estado da replicação, modificar a replicação e testar a implementação.

1. No menu portal do Azure, selecione **máquinas virtuais**ou pesquise e selecione *máquinas virtuais* em qualquer página. Selecione a VM que você deseja verificar.
2. Em **Operações**, selecione **Recuperação após desastre**.

   Pode verificar o estado de funcionamento da replicação, os pontos de recuperação que foram criados e as regiões de origem e destino num mapa.

   ![Estado de replicação](media/azure-to-azure-quickstart/replication-status.png)

## <a name="clean-up-resources"></a>Limpar recursos

A VM na região primária para de replicar quando desativa a replicação na mesma:

- As definições de replicação da origem são limpas automaticamente. A extensão de Site Recovery instalada na VM como parte da replicação não é removida e deve ser removida manualmente.
- Site Recovery a cobrança pela VM é interrompida.

Pare a replicação da seguinte forma:

1. No menu portal do Azure, selecione **máquinas virtuais**ou pesquise e selecione *máquinas virtuais* em qualquer página. Selecione a VM que você deseja modificar.
2. Em **recuperação de desastre**, selecione **desabilitar replicação**.

   ![Desativar a replicação](media/azure-to-azure-quickstart/disable2-replication.png)

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, replicou uma única VM numa região secundária. Agora, tente replicar várias VMs do Azure usando um plano de recuperação.

> [!div class="nextstepaction"]
> [Configurar a recuperação de desastre para VMs do Azure](azure-to-azure-tutorial-enable-replication.md)
