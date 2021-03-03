---
title: Mova uma máquina virtual Azure Automanage através de regiões
description: Saiba como mover uma máquina virtual autogerida através de regiões
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/05/2021
ms.author: alsin
ms.custom: subject-moving-resources
ms.openlocfilehash: 99371b8618756c196b75858288c5c4785272a7e8
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650470"
---
# <a name="move-an-azure-automanage-virtual-machine-to-a-different-region"></a>Mova uma máquina virtual Azure Automanage para uma região diferente
Este artigo descreve como manter a Automanagem ativada numa máquina virtual (VM) quando a desloca para uma região diferente. É melhor mover as suas máquinas virtuais para outra região por várias razões. Por exemplo, aproveitar uma nova região de Azure, para satisfazer os requisitos de política interna e de governação, ou em resposta aos requisitos de planeamento de capacidades. Os VMs que moves podem atualmente ser geridos automaticamente, e podes querer que eles permaneçam autogeridos após a tua mudança.

## <a name="prerequisites"></a>Pré-requisitos
* Certifique-se de que a sua região-alvo é [apoiada pela Automanage](./automanage-virtual-machines.md#prerequisites).
* Certifique-se de que a sua região de espaço de trabalho Log Analytics, a região de conta de automação e a sua região alvo são todas as regiões apoiadas pelos mapeamentos da região [aqui.](../automation/how-to/region-mappings.md)

## <a name="prepare-your-automanaged-vms-for-moving"></a>Prepare os seus VMs autogeridos para se mover
Desative a gestão automática dos seus VMs autogeridos. Pode fazê-lo selecionando os seus VMs na lâmina de auto-gestão e clicando em **Desativar** a gestão automática na lâmina de auto-gestão.

## <a name="move-your-automanaged-vms-and-re-enable-automanage"></a>Mova os seus VMs autogeridos e reative a autogestão
Para mais detalhes sobre como mover os seus VMs, consulte este [artigo](../resource-mover/tutorial-move-region-virtual-machines.md).

Depois de ter deslocado os seus VMs para as regiões, poderá voltar a permitir a auto-mutilação. Os detalhes estão disponíveis [aqui.](./automanage-virtual-machines.md#enabling-automanage-for-vms-in-azure-portal)

## <a name="next-steps"></a>Passos seguintes
* [Saiba mais sobre a Azure Automanage](./automanage-virtual-machines.md)
* [Ver perguntas frequentes sobre Azure Automanage](./faq.md)