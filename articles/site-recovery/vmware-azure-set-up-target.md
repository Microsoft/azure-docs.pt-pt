---
title: Preparar o alvo de replicação VMware VM na Recuperação do Local de Azure
description: Este artigo descreve como preparar o ambiente Azure alvo para a replicação VMware VM para Azure.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/03/2019
ms.author: mayg
ms.openlocfilehash: c721234f2e1d806d51d31f3466e441bf8360f6b8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "73693163"
---
# <a name="prepare-the-target-environment-for-disaster-recovery-of-vmware-vms-or-physical-servers-to-azure"></a>Preparar o ambiente-alvo para a recuperação de desastres de VMware VMs ou servidores físicos para Azure

Este artigo descreve como preparar o ambiente Azure alvo para começar a replicar máquinas virtuais VMware ou servidores físicos para a Azure.

## <a name="prerequisites"></a>Pré-requisitos

O artigo assume:
- Criou um Cofre de Serviços de Recuperação no [portal Azure](https://portal.azure.com "Portal do Azure") para proteger as suas máquinas de origem
- Configurar o ambiente no local para replicar as [máquinas virtuais vMware](vmware-azure-set-up-source.md) de origem ou [servidores físicos](physical-azure-set-up-source.md) para o Azure.

## <a name="prepare-target"></a>Preparar alvo

Depois de completar o **Passo 1: Selecione a meta de proteção** e **passo 2: Prepare a fonte,** é levado para o **passo 3: Alvo**

![Preparar alvo](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Assinatura:** A partir do menu suspenso, selecione a Subscrição para a quais pretende replicar as suas máquinas virtuais ou servidores físicos.
2. **Modelo de implantação:** Selecione o modelo de implementação (Classic ou Resource Manager)

Com base no modelo de implementação escolhido, é executada uma validação para garantir que tem pelo menos uma rede virtual na subscrição-alvo para replicar e falhar a sua máquina virtual ou servidor físico para.

Assim que as validações estiverem concluídas com sucesso, clique em OK para ir ao passo seguinte.

Se não tiver uma rede virtual, pode criar uma clicando no botão **+ Rede** no topo da página.

## <a name="next-steps"></a>Passos seguintes
[Configurar as definições de replicação](vmware-azure-set-up-replication.md).
