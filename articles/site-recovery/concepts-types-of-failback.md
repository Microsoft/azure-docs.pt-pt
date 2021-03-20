---
title: Falha durante recuperação de desastres com | de recuperação do local de Azure Microsoft Docs
description: Este artigo fornece uma visão geral de vários tipos de falhas e ressalvas a considerar enquanto não volta aos locais durante a recuperação de desastres com o serviço de recuperação do local de Azure.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: raynew
ms.openlocfilehash: c0eaf28f9aeb4050fd35a6036a53e3e91d00f3eb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "84691089"
---
# <a name="failback-of-vmware-vms-after-disaster-recovery-to-azure"></a>Reativação pós-falha das VMs VMware após a recuperação após desastre do Azure

Depois de ter falhado com a Azure como parte do seu processo de recuperação de desastres, pode voltar ao seu local. Existem dois tipos diferentes de failback que são possíveis com a recuperação do local de Azure: 

- Falhe de volta ao local original 
- Falhar de volta a um local alternativo

Se tiver falhado por cima de uma máquina virtual VMware, pode falhar de volta à mesma máquina virtual de origem no local, se ainda existir. Neste cenário, apenas as alterações são replicadas de volta. Este cenário é conhecido como **recuperação original da localização.** Se a máquina virtual no local não existir, o cenário é uma **recuperação alternativa da localização**.

> [!NOTE]
> Só pode falhar no servidor original do vCenter e da Configuração. Não é possível implementar um novo servidor de Configuração e falhar na sua utilização. Além disso, não é possível adicionar um novo vCenter ao servidor de configuração existente e falhar no novo vCenter.

## <a name="original-location-recovery-olr"></a>Recuperação original da localização (OLR)
Se optar por voltar a falhar na máquina virtual original, devem ser satisfeitas as seguintes condições:

* Se a máquina virtual for gerida por um servidor vCenter, então o anfitrião ESX do alvo principal deve ter acesso à loja de dados da máquina virtual.
* Se a máquina virtual estiver num hospedeiro ESX mas não for gerida pelo vCenter, então o disco rígido da máquina virtual deve estar numa loja de dados a que o anfitrião do alvo principal pode aceder.
* Se a sua máquina virtual estiver num hospedeiro ESX e não utilizar o vCenter, então deve completar a descoberta do anfitrião ESX do alvo principal antes de reprotegir. Isto aplica-se se também estiveres a falhar nos servidores físicos.
* Pode falhar de volta a uma rede de área de armazenamento virtual (vSAN) ou a um disco baseado no mapeamento de dispositivos brutos (RDM) se os discos já existirem e estiverem ligados à máquina virtual no local.

> [!IMPORTANT]
> É importante ativar o disco.enableUID= TRUE para que, durante o failback, o serviço de recuperação do local de Azure seja capaz de identificar o VMDK original na máquina virtual para a qual serão escritas as alterações pendentes. Se este valor não for definido como TRUE, então o serviço tenta identificar o VMDK correspondente no local com uma melhor base de esforço. Se o VMDK certo não for encontrado, cria um disco extra e os dados são escritos sobre isso.

## <a name="alternate-location-recovery-alr"></a>Recuperação alternativa da localização (ALR)
Se a máquina virtual no local não existir antes de reprotegir a máquina virtual, o cenário é chamado de recuperação alternativa de localização. O fluxo de trabalho reprotetor cria novamente a máquina virtual no local. Isto também irá causar um download completo de dados.

* Quando falhas numa localização alternativa, a máquina virtual é recuperada para o mesmo anfitrião ESX no qual o servidor alvo principal é implantado. A loja de dados que é usada para criar o disco será a mesma datastore que foi selecionada ao reprotecer a máquina virtual.
* Só pode falhar num sistema de ficheiros de máquina virtual (VMFS) ou na loja de dados vSAN. Se tiver um RDM, reprotegir e falhar não funcionará.
* Reprotectet envolve uma grande transferência inicial de dados que é seguida pelas mudanças. Este processo existe porque a máquina virtual não existe no local. Os dados completos têm de ser replicados. Este reprotetor também levará mais tempo do que uma recuperação original da localização.
* Não pode falhar nos discos baseados em RDM. Apenas novos discos de máquinas virtuais (VMDKs) podem ser criados numa loja de dados VMFS/vSAN.

> [!NOTE]
> Uma máquina física, quando falha no Azure, só pode ser falhada como uma máquina virtual VMware. Isto segue o mesmo fluxo de trabalho que a recuperação alternativa da localização. Certifique-se de que descobre pelo menos um servidor-alvo principal e os anfitriões ESX/ESXi necessários aos quais precisa de falhar.

## <a name="next-steps"></a>Passos seguintes

Siga os passos para executar a [operação de failback](vmware-azure-failback.md).

