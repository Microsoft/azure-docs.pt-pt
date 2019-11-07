---
title: Preparar o destino de replicação de VM VMware no Azure Site Recovery
description: Este artigo descreve como preparar seu ambiente do Azure de destino para a replicação de VM do VMware para o Azure.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/03/2019
ms.author: mayg
ms.openlocfilehash: c721234f2e1d806d51d31f3466e441bf8360f6b8
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693163"
---
# <a name="prepare-the-target-environment-for-disaster-recovery-of-vmware-vms-or-physical-servers-to-azure"></a>Preparar o ambiente de destino para recuperação de desastre de VMs VMware ou servidores físicos para o Azure

Este artigo descreve como preparar seu ambiente do Azure de destino para iniciar a replicação de máquinas virtuais VMware ou servidores físicos para o Azure.

## <a name="prerequisites"></a>Pré-requisitos

O artigo pressupõe:
- Você criou um cofre dos serviços de recuperação no [portal do Azure](https://portal.azure.com "Portal do Azure") para proteger seus computadores de origem
- Você configurou seu ambiente local para replicar as [máquinas virtuais VMware](vmware-azure-set-up-source.md) de origem ou os [servidores físicos](physical-azure-set-up-source.md) para o Azure.

## <a name="prepare-target"></a>Preparar destino

Depois de concluir a **etapa 1: selecionar meta de proteção** e **etapa 2: preparar origem**, você será levado para a **etapa 3: destino**

![Preparar destino](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Assinatura:** No menu suspenso, selecione a assinatura na qual você deseja replicar suas máquinas virtuais ou servidores físicos.
2. **Modelo de implantação:** Selecione o modelo de implantação (clássico ou Gerenciador de recursos)

Com base no modelo de implantação escolhido, uma validação é executada para garantir que você tenha pelo menos uma rede virtual na assinatura de destino para replicar e fazer failover de sua máquina virtual ou servidor físico para o.

Depois que as validações forem concluídas com êxito, clique em OK para ir para a próxima etapa.

Se você não tiver uma rede virtual, poderá criar uma clicando no botão **+ rede** na parte superior da página.

## <a name="next-steps"></a>Passos seguintes
[Defina as configurações de replicação](vmware-azure-set-up-replication.md).
