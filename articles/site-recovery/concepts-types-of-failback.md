---
title: Reativação pós-falha durante a recuperação após desastre com o Azure Site Recovery | Documentos da Microsoft
description: Este artigo fornece uma visão geral dos vários tipos de reativação pós-falha e avisos que devem ser considerados ao realizar a ativação para o local durante a recuperação após desastre com o serviço Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: raynew
ms.openlocfilehash: 6f178ef1a0aec7f742bce4c2570962b995876026
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316274"
---
# <a name="failback-after-disaster-recovery-of-vmware-vms"></a>Reativação pós-falha após a recuperação após desastre de VMs de VMware

Depois de ter ativação pós-falha para o Azure como parte do seu processo de recuperação após desastre, pode efetuar a ativação para seu site no local. Existem dois tipos diferentes de reativação pós-falha que são possíveis com o Azure Site Recovery: 

- Reativação pós-falha para a localização original 
- Reativação pós-falha para uma localização alternativa

Se efetuar a ativação pós-falha de uma máquina virtual VMware, pode fazer a reativação a mesma máquina de virtual no local de origem se ainda existir. Neste cenário, apenas as alterações são replicadas novamente. Este cenário é conhecido como **recuperação de localização original**. Se a máquina de virtual no local não existir, o cenário é um **alternativo a recuperação de localização**.

> [!NOTE]
> Apenas pode efetuar a ativação pós-falha para o original vCenter e o servidor de configuração. Não é possível implementar um novo servidor de configuração e falham utilizá-lo de volta. Além disso, não é possível adicionar um novo vCenter para o servidor de configuração existente e a reativação pós-falha para o vCenter novo.

## <a name="original-location-recovery-olr"></a>Recuperação de localização original (OLR)
Se escolher para a reativação pós-falha da máquina virtual original, as seguintes condições devem ser atendidas:

* Se a máquina virtual for gerenciada por um vCenter server, o anfitrião do ESX de destino principal deve ter acesso ao arquivo de dados da máquina virtual.
* Se a máquina virtual está num anfitrião ESX, mas não é gerenciada pelo vCenter, em seguida, o disco rígido da máquina virtual deve estar num arquivo de dados que o anfitrião de destino principal pode aceder.
* Se a sua máquina virtual estiver num anfitrião ESX e não utilizar o vCenter, em seguida, deve concluir a deteção do anfitrião ESX de destino principal antes de voltar a proteger. Isso se aplica se está a reativar pós-falha de servidores físicos, também.
* Pode falhar novamente para uma rede de área de armazenamento virtual (vSAN) ou um disco com base no dispositivo sem formato (RDM) de mapeamento, se os discos já existem e estão ligados à máquina virtual no local.

> [!IMPORTANT]
> É importante ativar disk.enableUUID= TRUE, para que durante a reativação pós-falha, o serviço Azure Site Recovery é capaz de identificar o VMDK original na máquina virtual para que as alterações pendentes serão escritas. Se este valor não está definido ser verdadeiro, em seguida, o serviço tenta identificar o VMDK no local correspondente na base de melhor esforço. Se o VMDK direito não for encontrado, ele cria um disco adicional e gravados para que os dados.

## <a name="alternate-location-recovery-alr"></a>Recuperação de localização alternativa (ALR)
Se a máquina de virtual no local não existe antes de proteger novamente a máquina virtual, o cenário é chamado de uma recuperação de localização alternativa. O fluxo de trabalho de reproteção cria novamente a máquina virtual no local. Isso também fará com que uma transferência de dados completa.

* Quando a reativação pós-falha para uma localização alternativa, a máquina virtual é recuperada para o mesmo anfitrião ESX no qual o servidor de destino mestre está implementado. O arquivo de dados que é utilizado para criar o disco será o mesmo arquivo de dados que foi selecionado quando proteger novamente a máquina virtual.
* Pode efetuar a ativação back apenas para um sistema de ficheiros de máquina virtual (VMFS) ou o arquivo de dados de vSAN. Se tiver um RDM, reproteção e reativação pós-falha não funcionará.
* Voltar a proteger envolve uma transferência de grandes de dados inicial que é seguida as alterações. Este processo existe porque a máquina virtual não existe no local. Tem de ser replicados novamente os dados completos. Esta nova proteção também irá demorar mais tempo do que uma recuperação de localização original.
* Não é possível reativação pós-falha para discos com base em RDM. Apenas novos de máquina virtual de discos (VMDKs) podem ser criados num arquivo de dados VMFS/vSAN.

> [!NOTE]
> Uma máquina física, quando a ativação pós-falha para o Azure, pode ser não conseguiu fazer uma cópia apenas como uma máquina virtual VMware. Isso segue o mesmo fluxo de trabalho como a recuperação de localização alternativa. Certifique-se de que descubra, pelo menos, um servidor de destino principal e os anfitriões ESX/ESXi necessários para que precisa para a reativação pós-falha.

## <a name="next-steps"></a>Passos Seguintes

Siga os passos para executar o [operação de reativação pós-falha](vmware-azure-failback.md).

