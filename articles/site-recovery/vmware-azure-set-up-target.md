---
title: Preparar o ambiente de destino para replicação de VMware para o Azure | Documentos da Microsoft
description: Este artigo descreve como preparar o ambiente do Azure para replicação de VMS de VMware para o Azure de destino.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/03/2019
ms.author: mayg
ms.openlocfilehash: 238e7a26be67fcfd2a0b79a87409e5c0d57e0cbf
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57338370"
---
# <a name="prepare-the-target-environment-for-disaster-recovery-of-vmware-vms-or-physical-servers-to-azure"></a>Preparar o ambiente de destino para recuperação após desastre de VMs de VMware ou servidores físicos no Azure

Este artigo descreve como preparar o ambiente do Azure para iniciar a replicação de máquinas virtuais VMware ou servidores físicos para o Azure de destino.

## <a name="prerequisites"></a>Pré-requisitos

O artigo supõe:
- Criou um cofre dos serviços de recuperação [portal do Azure](http://portal.azure.com "portal do Azure") para proteger as suas máquinas de origem
- Tiver configurado seu ambiente no local para replicar a origem [máquinas virtuais VMware](vmware-azure-set-up-source.md) ou [servidores físicos](physical-azure-set-up-source.md) para o Azure.

## <a name="prepare-target"></a>Preparar o destino

Depois de concluir o **passo 1: Selecione objetivo de proteção** e **passo 2: Preparar origem**, é direcionado para **passo 3: Destino**

![Preparar o destino](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Subscrição:** No menu pendente, selecione a subscrição que pretende replicar as máquinas virtuais ou servidores físicos para o.
2. **Modelo de implementação:** Selecione o modelo de implementação (clássico ou do Resource Manager)

Com base no modelo de implementação escolhida, é executada uma validação para garantir que tem pelo menos uma rede virtual na subscrição de destino para replicação e ativação pós-falha sua máquina virtual ou servidor físico para.

Assim que as validações concluir com êxito, clique em OK para ir para o passo seguinte.

Se não tiver uma rede virtual, pode criar uma ao clicar o **+ rede** botão na parte superior da página.

## <a name="next-steps"></a>Passos Seguintes
[Configurar definições de replicação](vmware-azure-set-up-replication.md).
