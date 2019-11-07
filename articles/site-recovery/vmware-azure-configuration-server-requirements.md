---
title: Recuperação de desastre do VMware – requisitos do servidor de configuração no Azure Site Recovery
description: Este artigo descreve o suporte e os requisitos ao implantar o servidor de configuração para a recuperação de desastres do VMware no Azure com o Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 0b0942b517c8dc83c048bd1203a58d9861515dfb
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73663053"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Requisitos do servidor de configuração para a recuperação de desastres do VMware para o Azure

Você implanta um servidor de configuração local ao usar [Azure site Recovery](site-recovery-overview.md) para recuperação de desastre de VMs VMware e servidores físicos no Azure.

- O servidor de configuração coordena as comunicações entre o VMware local e o Azure. Ele também gerencia a replicação de dados.
- [Saiba mais](vmware-azure-architecture.md) sobre os componentes e processos do servidor de configuração.

## <a name="configuration-server-deployment"></a>Implantação do servidor de configuração

Para a recuperação de desastre de VMs VMware no Azure, você implanta o servidor de configuração como uma VM VMware.

- Site Recovery fornece um modelo OVA que você baixa do portal do Azure e importa para vCenter Server para configurar a VM do servidor de configuração.
- Quando você implanta o servidor de configuração usando o modelo OVA, a VM está em conformidade automaticamente com os requisitos listados neste artigo.
- É altamente recomendável que você configure o servidor de configuração usando o modelo OVA. No entanto, se você estiver configurando a recuperação de desastre para VMs VMware e não puder usar o modelo OVA, poderá implantar o servidor de configuração usando [essas instruções fornecidas](physical-azure-set-up-source.md).
- Se você estiver implantando o servidor de configuração para recuperação de desastre de máquinas físicas locais no Azure, siga as instruções neste [artigo](physical-azure-set-up-source.md). 

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="next-steps"></a>Passos seguintes
Configure a recuperação de desastre de [VMs do VMware](vmware-azure-tutorial.md) no Azure.
