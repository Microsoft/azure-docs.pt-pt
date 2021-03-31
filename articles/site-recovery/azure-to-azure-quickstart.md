---
title: Criar a recuperação de desastres da Azure VM para uma região secundária com recuperação do local de Azure
description: Rapidamente instale a recuperação de desastres para outra região de Azure para um Azure VM, utilizando o serviço de Recuperação do Local Azure.
ms.topic: quickstart
ms.date: 03/27/2020
ms.custom: mvc
ms.openlocfilehash: 8681ccaac30402927005e5e8802d7da2bddc2c5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86135699"
---
# <a name="quickstart-set-up-disaster-recovery-to-a-secondary-azure-region-for-an-azure-vm"></a>Quickstart: Criar recuperação de desastres para uma região secundária de Azure para um Azure VM

O serviço [de Recuperação de Sítios Azure](site-recovery-overview.md) contribui para a estratégia de continuidade e recuperação de desastres (BCDR) mantendo as suas aplicações de negócio on-line durante as paragens planeadas e não planeadas. A Recuperação do Site gere e orquestra a recuperação de desastres de máquinas no local e máquinas virtuais Azure (VM), incluindo replicação, failover e recuperação.

Este quickstart descreve como configurar a recuperação de desastres para um VM Azure, replicando-o numa região secundária de Azure. Em geral, são utilizadas definições predefinidas para permitir a replicação.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, precisa de uma assinatura Azure e um VM.

- Se não tiver uma conta Azure com uma subscrição ativa, pode [criar uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Recomenda-se um VM com um mínimo de 1 GB de RAM. [Saiba mais](../virtual-machines/windows/quick-create-portal.md) sobre como criar um VM.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="enable-replication-for-the-azure-vm"></a>Ativar a replicação para a VM do Azure

Os passos seguintes permitem a replicação de VM para um local secundário.

1. No portal Azure, a partir do menu **de**  >  **máquinas Home Virtual,** selecione um VM para replicar.
1. Em **Operações** selecione **recuperação de desastres**.
1. Da região **Basics**  >  **Target,** selecione a região alvo.
1. Para visualizar as definições de replicação, selecione **Rever + Iniciar a replicação**. Se precisar de alterar quaisquer predefinições, selecione **Definições Avançadas**.
1. Para iniciar o trabalho que permite a replicação VM **selecione Iniciar a replicação**.

   :::image type="content" source="media/azure-to-azure-quickstart/enable-replication1.png" alt-text="Ativar a replicação.":::

## <a name="verify-settings"></a>Verificar as definições

Após o fim do trabalho de replicação, pode verificar o estado de replicação, modificar as definições de replicação e testar a implementação.

1. No menu do portal Azure, selecione **máquinas Virtuais** e selecione o VM que replicou.
1. Em **Operações** selecione **recuperação de desastres**.
1. Para ver os detalhes de replicação da **visão geral,** selecione **Essentials**. Mais detalhes são mostrados na **Saúde e estado,** **prontidão failover,** e no mapa de **vista da Infraestrutura.**

   :::image type="content" source="media/azure-to-azure-quickstart/replication-status.png" alt-text="Estado de replicação.":::

## <a name="clean-up-resources"></a>Limpar os recursos

Para parar a replicação do VM na região primária, deve desativar a replicação:

- As definições de replicação da origem são limpas automaticamente.
- A extensão de Recuperação do Local instalada no VM durante a replicação não é removida.
- A faturação de recuperação do local para as paragens de VM.

Para desativar a replicação, faça estes passos:

1. No menu do portal Azure, selecione **máquinas Virtuais** e selecione o VM que replicou.
1. Em **Operações** selecione **recuperação de desastres**.
1. A partir do **Resumo,** selecione **Replicação de Desativação.**
1. Para desinstalar a extensão de recuperação do site, aceda às extensões de **definições** do VM  >  .

   :::image type="content" source="media/azure-to-azure-quickstart/disable2-replication.png" alt-text="Desativar a replicação.":::

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, replicou uma única VM numa região secundária. Em seguida, confiem de replicação para vários VMs Azure.

> [!div class="nextstepaction"]
> [Preparar a recuperação de desastres para os VMs do Azure](azure-to-azure-tutorial-enable-replication.md)
