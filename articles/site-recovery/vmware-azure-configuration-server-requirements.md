---
title: Requisitos do servidor de recuperação de desastres VMware na Recuperação do Site Azure
description: Este artigo descreve suporte e requisitos ao implementar o servidor de configuração para recuperação de desastres VMware para Azure com Azure Site Recovery
ms.service: site-recovery
ms.topic: article
ms.date: 09/09/2019
ms.openlocfilehash: aec08aa4f18c69195be91b7544b74fee100f6f6b
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580191"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Requisitos do servidor de configuração para recuperação após desastre de VMware para o Azure

Implementa um servidor de configuração no local quando utiliza [a Recuperação do Site Azure](site-recovery-overview.md) para a recuperação de desastres de VMware VMs e servidores físicos para o Azure.

- O servidor de configuração coordena as comunicações entre o VMware e o Azure no local. Também gere a replicação de dados.
- [Saiba mais](vmware-azure-architecture.md) sobre os componentes e processos do servidor de configuração.

## <a name="configuration-server-deployment"></a>Implementação do servidor de configuração

Para a recuperação de desastres de VMware VMs para Azure, implementa o servidor de configuração como VMware VM.

- A Recuperação do Site fornece um modelo OVA que descarrega a partir do portal Azure e importa para o vCenter Server para configurar o VM do servidor de configuração.
- Quando implementa o servidor de configuração utilizando o modelo OVA, o VM cumpre automaticamente os requisitos listados neste artigo.
- Recomendamos vivamente que crie o servidor de configuração utilizando o modelo OVA. No entanto, se estiver a configurar a recuperação de desastres para VMware VMs e não puder utilizar o modelo OVA, pode implantar o servidor de configuração utilizando [estas instruções fornecidas](physical-azure-set-up-source.md).
- Se estiver a implantar o servidor de configuração para a recuperação de desastres das máquinas físicas no local para o Azure, siga as instruções [deste artigo](physical-azure-set-up-source.md). 

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="next-steps"></a>Passos seguintes
Confiúdo de recuperação de [VMware VMs](vmware-azure-tutorial.md) para Azure.
