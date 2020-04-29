---
title: Requisitos do servidor de recuperação de desastres vMware na Recuperação do Site Azure
description: Este artigo descreve suporte e requisitos ao implementar o servidor de configuração para recuperação de desastres VMware para Azure com recuperação do site Azure
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 0b0942b517c8dc83c048bd1203a58d9861515dfb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79257410"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Requisitos do servidor de configuração para recuperação de desastres VMware para O Azure

Implementa um servidor de configuração no local quando utiliza a Recuperação do [Site Azure](site-recovery-overview.md) para a recuperação de desastres de VMware VMs e servidores físicos para o Azure.

- O servidor de configuração coordena as comunicações entre vMware no local e Azure. Também gere a replicação de dados.
- [Saiba mais](vmware-azure-architecture.md) sobre os componentes e processos do servidor de configuração.

## <a name="configuration-server-deployment"></a>Implementação do servidor de configuração

Para a recuperação de desastres de VMware VMs para O Azure, implementa o servidor de configuração como VMware VM.

- A Recuperação do Site fornece um modelo OVA que descarrega a partir do portal Azure e importa para o VCenter Server para configurar o vM do servidor de configuração.
- Quando implementa o servidor de configuração utilizando o modelo OVA, o VM cumpre automaticamente os requisitos listados neste artigo.
- Recomendamos vivamente que instale o servidor de configuração utilizando o modelo OVA. No entanto, se estiver a configurar a recuperação de desastres para VMware VMs e não puder utilizar o modelo OVA, pode implementar o servidor de configuração utilizando [estas instruções fornecidas](physical-azure-set-up-source.md).
- Se estiver a implantar o servidor de configuração para recuperação de desastres de máquinas físicas no local para o Azure, siga as instruções [deste artigo](physical-azure-set-up-source.md). 

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="next-steps"></a>Passos seguintes
Instale a recuperação de desastres de [VMware VMs](vmware-azure-tutorial.md) para o Azure.
