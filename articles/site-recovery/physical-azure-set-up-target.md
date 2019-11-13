---
title: Configurar o ambiente de destino para servidores físicos no Azure Site Recovery
description: Este artigo descreve como configurar o ambiente de destino do Azure para recuperação de desastre de servidores físicos usando o Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 685f04b27cd315b04ef5c45e155e825eebe2747b
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953891"
---
# <a name="prepare-target-vmware-to-azure"></a>Preparar destino (VMware para Azure)

Este artigo descreve como preparar seu ambiente do Azure para iniciar a replicação de servidores físicos (x64) que executam Windows ou Linux no Azure.

## <a name="prerequisites"></a>Pré-requisitos

O artigo pressupõe:
- Você criou um cofre dos serviços de recuperação para proteger seus servidores físicos. Você pode criar um cofre dos serviços de recuperação no [portal do Azure](https://portal.azure.com "Portal do Azure").
- Você configurou [seu ambiente local](physical-azure-disaster-recovery.md) para replicar servidores físicos no Azure.

## <a name="prepare-target"></a>Preparar destino

Depois de concluir a **etapa 1: selecionar meta de proteção** e **etapa 2: preparar origem**, você será levado para a **etapa 3: destino**

![Preparar destino](./media/physical-azure-set-up-target/prepare-target-physical-to-azure.png)

1. **Assinatura:** No menu suspenso, selecione a assinatura na qual você deseja replicar seus servidores físicos.
2. **Modelo de implantação:** Selecione o modelo de implantação (clássico ou Gerenciador de recursos)

Com base no modelo de implantação escolhido, uma validação é executada para garantir que você tenha pelo menos uma conta de armazenamento compatível e uma rede virtual na assinatura de destino para replicar e fazer failover de seus servidores físicos para o.

Depois que as validações forem concluídas com êxito, clique em OK para ir para a próxima etapa.

Se você não tiver uma rede virtual ou uma conta de armazenamento do Resource Manager compatível, poderá criar uma clicando nos botões **+ conta de armazenamento** ou **+ rede** na parte superior da página.

## <a name="next-steps"></a>Passos seguintes
[Defina as configurações de replicação](vmware-azure-set-up-replication.md).
