---
title: Failback durante a recuperação de desastre com o Azure Site Recovery | Microsoft Docs
description: Este artigo fornece uma visão geral de vários tipos de failback e advertências a serem considerados ao fazer failback no local durante a recuperação de desastres com o serviço de Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: raynew
ms.openlocfilehash: c0eaf28f9aeb4050fd35a6036a53e3e91d00f3eb
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68847489"
---
# <a name="failback-of-vmware-vms-after-disaster-recovery-to-azure"></a>Failback de VMs VMware após a recuperação de desastre para o Azure

Após o failover para o Azure como parte do processo de recuperação de desastre, você poderá fazer failback para o site local. Há dois tipos diferentes de failback que são possíveis com Azure Site Recovery: 

- Fazer failback para o local original 
- Failback para um local alternativo

Se você tiver feito failover de uma máquina virtual VMware, poderá fazer failback para a mesma máquina virtual local de origem se ela ainda existir. Nesse cenário, somente as alterações são replicadas de volta. Esse cenário é conhecido como **recuperação de local original**. Se a máquina virtual local não existir, o cenário será uma **recuperação de local alternativo**.

> [!NOTE]
> Você só pode realizar failback para o vCenter e o servidor de configuração originais. Você não pode implantar um novo servidor de configuração e fazer failback usando-o. Além disso, você não pode adicionar um novo vCenter ao servidor de configuração existente e fazer failback no novo vCenter.

## <a name="original-location-recovery-olr"></a>OLR (recuperação de local original)
Se você optar por fazer failback para a máquina virtual original, as seguintes condições precisarão ser atendidas:

* Se a máquina virtual for gerenciada por um servidor vCenter, o host ESX do destino mestre deverá ter acesso ao repositório de armazenamento da máquina virtual.
* Se a máquina virtual estiver em um host ESX, mas não for gerenciada pelo vCenter, o disco rígido da máquina virtual deverá estar em um repositório de armazenamento que o host do destino mestre possa acessar.
* Se sua máquina virtual estiver em um host ESX e não usar o vCenter, você deverá concluir a descoberta do host ESX do destino mestre antes de proteger novamente. Isso se aplicará se você estiver realizando o failback de servidores físicos também.
* Você pode fazer failback para uma rede de área de armazenamento virtual (vSAN) ou um disco com base no mapeamento de dispositivo bruto (RDM) se os discos já existirem e estiverem conectados à máquina virtual local.

> [!IMPORTANT]
> É importante habilitar Disk. enableUUID = TRUE para que, durante o failback, o serviço de Azure Site Recovery seja capaz de identificar o VMDK original na máquina virtual para a qual as alterações pendentes serão gravadas. Se esse valor não for definido como TRUE, o serviço tentará identificar o VMDK local correspondente em uma base de melhor esforço. Se o VMDK correto não for encontrado, ele cria um disco extra e os dados são gravados nele.

## <a name="alternate-location-recovery-alr"></a>Recuperação de local alternativo (ALR)
Se a máquina virtual local não existir antes de proteger novamente a máquina virtual, o cenário será chamado de recuperação de local alternativo. O fluxo de trabalho de nova proteção cria a máquina virtual local novamente. Isso também causará um download de dados completo.

* Quando você faz failback para um local alternativo, a máquina virtual é recuperada para o mesmo host ESX no qual o servidor de destino mestre é implantado. O repositório de armazenamento que é usado para criar o disco será o mesmo repositório de armazenamento que foi selecionado ao proteger novamente a máquina virtual.
* Você pode executar o failback somente para um armazenamento de um sistema de arquivos de máquina virtual (VMFS) ou vSAN. Se você tiver um RDM, a nova proteção e o failback não funcionarão.
* A nova proteção envolve uma grande transferência de dados inicial seguida pelas alterações. Esse processo existe porque a máquina virtual não existe no local. Os dados completos têm que ser replicados de volta. Essa nova proteção também levará mais tempo do que uma recuperação de local original.
* Não é possível fazer failback para discos baseados em RDM. Somente novos discos de máquina virtual (VMDKs) podem ser criados em um repositório de armazenamento VMFS/vSAN.

> [!NOTE]
> Um computador físico, quando fez failover no Azure, pode ter falhado novamente como uma máquina virtual VMware. Isso segue o mesmo fluxo de trabalho que a recuperação de local alternativo. Verifique se você descobriu pelo menos um servidor de destino mestre e os hosts ESX/ESXi necessários para os quais você precisa fazer failback.

## <a name="next-steps"></a>Passos Seguintes

Siga as etapas para executar a [operação de failback](vmware-azure-failback.md).

