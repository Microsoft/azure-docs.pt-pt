---
title: Criar recuperação de desastres azure VM para uma região secundária com recuperação do site Azure
description: Rapidamente instale a recuperação de desastres para outra região de Azure para um Azure VM, utilizando o serviço de recuperação do local azure.
ms.topic: quickstart
ms.date: 03/27/2020
ms.custom: mvc
ms.openlocfilehash: e26c2a1f24a88dc979f4ec68de65afc618740c00
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80371888"
---
# <a name="quickstart-set-up-disaster-recovery-to-a-secondary-azure-region-for-an-azure-vm"></a>Quickstart: Instale recuperação de desastres numa região secundária de Azure para um Azure VM

O serviço de recuperação de [sites Azure](site-recovery-overview.md) contribui para a continuidade do seu negócio e estratégia de recuperação de desastres (BCDR), mantendo as suas aplicações de negócio online durante as interrupções planeadas e não planeadas. A Recuperação do Site gere e orquestra a recuperação de desastres de máquinas no local e máquinas virtuais Azure (VM), incluindo replicação, failover e recuperação.

Este quickstart descreve como configurar a recuperação de desastres para um VM Azure replicando-o para uma região secundária de Azure. Em geral, as definições predefinidas são utilizadas para permitir a replicação.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, precisa de uma subscrição Azure e de um VM.

- Se não tiver uma conta Azure com uma subscrição ativa, pode [criar uma conta gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Recomenda-se um VM com um mínimo de 1 GB de RAM. [Saiba mais](/azure/virtual-machines/windows/quick-create-portal) sobre como criar um VM.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="enable-replication-for-the-azure-vm"></a>Ativar a replicação para a VM do Azure

Os seguintes passos permitem a replicação vm para um local secundário.

1. No portal Azure, a partir do menu **home** > **virtual de máquinas,** selecione um VM para replicar.
1. Em **Operações** selecione recuperação de **desastres**.
1. A partir da região **Basics** > **Target,** selecione a região alvo.
1. Para visualizar as definições de replicação, selecione **Rever + Iniciar a replicação**. Se precisar de alterar eventuais predefinições, selecione **Definições Avançadas**.
1. Para iniciar o trabalho que permite a replicação vM selecione **A replicação Iniciar**.

   :::image type="content" source="media/azure-to-azure-quickstart/enable-replication1.png" alt-text="Ativar a replicação.":::

## <a name="verify-settings"></a>Verificar as definições

Após o trabalho de replicação terminar, pode verificar o estado de replicação, modificar as definições de replicação e testar a implementação.

1. No menu do portal Azure, selecione **máquinas Virtuais** e selecione o VM que replica.
1. Em **Operações** selecione recuperação de **desastres**.
1. Para ver os detalhes da replicação a partir da **visão geral** selecione **Essentials**. Mais detalhes são mostrados na **Saúde e estado,** prontidão de **failover**, e no mapa **de visualização** de Infraestruturas.

   :::image type="content" source="media/azure-to-azure-quickstart/replication-status.png" alt-text="Estado de replicação.":::

## <a name="clean-up-resources"></a>Limpar recursos

Para parar a replicação do VM na região primária, deve desativar a replicação:

- As definições de replicação da origem são limpas automaticamente.
- A extensão de recuperação do local instalada no VM durante a replicação não é removida.
- A faturação de recuperação do site para o VM para.

Para desativar a replicação, faça estes passos:

1. No menu do portal Azure, selecione **máquinas Virtuais** e selecione o VM que replica.
1. Em **Operações** selecione recuperação de **desastres**.
1. A partir da **visão geral,** selecione **Desativação Replicação**.
1. Para desinstalar a extensão de recuperação do site, vá às**extensões**de **definições** > do VM .

   :::image type="content" source="media/azure-to-azure-quickstart/disable2-replication.png" alt-text="Desativar a replicação.":::

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, replicou uma única VM numa região secundária. Em seguida, configurar a replicação para vários VMs Azure.

> [!div class="nextstepaction"]
> [Configurar recuperação de desastres para VMs Azure](azure-to-azure-tutorial-enable-replication.md)
