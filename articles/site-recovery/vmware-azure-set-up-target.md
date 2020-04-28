---
title: Prepare o alvo de replicação VMware VM na Recuperação do Site Azure
description: Este artigo descreve como preparar o seu ambiente-alvo Azure para a replicação VMware VM para o Azure.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/03/2019
ms.author: mayg
ms.openlocfilehash: c721234f2e1d806d51d31f3466e441bf8360f6b8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73693163"
---
# <a name="prepare-the-target-environment-for-disaster-recovery-of-vmware-vms-or-physical-servers-to-azure"></a>Prepare o ambiente-alvo para a recuperação de VMware vMs ou servidores físicos para o Azure

Este artigo descreve como preparar o seu ambiente-alvo Azure para começar a replicar máquinas virtuais VMware ou servidores físicos para o Azure.

## <a name="prerequisites"></a>Pré-requisitos

O artigo assume:
- Criou um Cofre de Serviços de Recuperação no [portal Azure](https://portal.azure.com "Portal do Azure") para proteger as suas máquinas de origem
- Instalou o seu ambiente no local para replicar a fonte [de máquinas virtuais VMware](vmware-azure-set-up-source.md) ou [servidores físicos](physical-azure-set-up-source.md) para o Azure.

## <a name="prepare-target"></a>Preparar o alvo

Depois de completar o **Passo 1: Selecione o objetivo** de proteção e o passo **2: Preparar a Fonte,** é levado para o **passo 3: Target**

![Preparar o alvo](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Subscrição:** A partir do menu suspenso, selecione a Subscrição a que pretende replicar as suas máquinas virtuais ou servidores físicos.
2. **Modelo de implantação:** Selecione o modelo de implementação (Classic ou Resource Manager)

Com base no modelo de implementação escolhido, é executada uma validação para garantir que tem pelo menos uma rede virtual na subscrição do alvo para replicar e falhar sobre a sua máquina virtual ou servidor físico para.

Assim que as validações estiverem concluídas com sucesso, clique em OK para passar ao passo seguinte.

Se não tiver uma rede virtual, pode criar uma clicando no botão **+ Rede** na parte superior da página.

## <a name="next-steps"></a>Passos seguintes
[Configurar as definições](vmware-azure-set-up-replication.md)de replicação .
