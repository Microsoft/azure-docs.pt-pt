---
title: Recuo durante recuperação de desastres com recuperação do site Azure Microsoft Docs
description: Este artigo fornece uma visão geral de vários tipos de falhas e ressalvas a considerar, ao mesmo tempo que não volta às instalações durante a recuperação de desastres com o serviço de recuperação do local de Azure.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: raynew
ms.openlocfilehash: c0eaf28f9aeb4050fd35a6036a53e3e91d00f3eb
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79281837"
---
# <a name="failback-of-vmware-vms-after-disaster-recovery-to-azure"></a>Reativação pós-falha das VMs VMware após a recuperação após desastre do Azure

Depois de ter falhado com o Azure como parte do seu processo de recuperação de desastres, pode falhar no seu local. Existem dois tipos diferentes de failback que são possíveis com a Recuperação do Site Azure: 

- Falhar de volta à localização original 
- Falhar de volta a um local alternativo

Se falhar com uma máquina virtual VMware, pode falhar na mesma fonte no local, se ainda existir. Neste cenário, apenas as mudanças são replicadas de volta. Este cenário é conhecido como **recuperação original**da localização. Se a máquina virtual no local não existir, o cenário é uma **recuperação alternativa**de localização.

> [!NOTE]
> Só pode falhar de volta ao vCenter original e ao servidor de Configuração. Não é possível implementar um novo servidor de Configuração e falhar a sua utilização. Além disso, não é possível adicionar um novo vCenter ao servidor de Configuração existente e falhar no novo vCenter.

## <a name="original-location-recovery-olr"></a>Recuperação original da localização (OLR)
Se optar por voltar à máquina virtual original, as seguintes condições devem ser satisfeitas:

* Se a máquina virtual for gerida por um servidor vCenter, então o anfitrião ESX do alvo principal deve ter acesso à loja de dados da máquina virtual.
* Se a máquina virtual estiver num hospedeiro ESX mas não for gerida pelo vCenter, então o disco rígido da máquina virtual deve estar numa loja de dados a que o anfitrião do alvo principal pode aceder.
* Se a sua máquina virtual estiver num hospedeiro ESX e não utilizar o vCenter, então deve concluir a descoberta do anfitrião ESX do alvo principal antes de se reproteger. Isto aplica-se se também estiver a falhar nos servidores físicos.
* Pode falhar uma rede de área de armazenamento virtual (vSAN) ou um disco que se baseie no mapeamento de dispositivos brutos (RDM) se os discos já existirem e estiverem ligados à máquina virtual no local.

> [!IMPORTANT]
> É importante ativar o disco.enableUUID= TRUE para que, durante o relançamento, o serviço de recuperação do site Azure seja capaz de identificar o VMDK original na máquina virtual à qual as alterações pendentes serão escritas. Se este valor não for definido para ser TRUE, então o serviço tenta identificar o VMDK correspondente no local numa base de melhor esforço. Se o VMDK certo não for encontrado, cria um disco extra e os dados são escritos sobre isso.

## <a name="alternate-location-recovery-alr"></a>Recuperação alternativa de localização (ALR)
Se a máquina virtual no local não existir antes de reproteger a máquina virtual, o cenário chama-se uma recuperação alternativa de localização. O fluxo de trabalho de reproteção cria novamente a máquina virtual no local. Isto também causará um download completo de dados.

* Quando falha um local alternativo, a máquina virtual é recuperada para o mesmo hospedeiro ESX no qual o servidor alvo principal é implantado. A loja de dados que é usada para criar o disco será a mesma loja de dados que foi selecionada para reproteger a máquina virtual.
* Só pode falhar a reprodução num sistema de ficheiros de máquinavirtual (VMFS) ou vSAN. Se tiver um RDM, reproteger e falhar não funcionará.
* A reproteção envolve uma grande transferência inicial de dados que é seguida pelas alterações. Este processo existe porque a máquina virtual não existe nas instalações. Os dados completos têm de ser replicados de volta. Este reproteção também levará mais tempo do que uma recuperação original da localização.
* Não pode falhar nos discos baseados em RDM. Apenas novos discos de máquinas virtuais (VMDKs) podem ser criados numa loja de dados VMFS/vSAN.

> [!NOTE]
> Uma máquina física, quando falhada no Azure, só pode ser reprovada como uma máquina virtual VMware. Isto segue o mesmo fluxo de trabalho que a recuperação alternativa do local. Certifique-se de que descobre pelo menos um servidor de alvo principal e os anfitriões ESX/ESXi necessários aos quais precisa de voltar afalhar.

## <a name="next-steps"></a>Passos seguintes

Siga os passos para executar a [operação de reprovação](vmware-azure-failback.md).

