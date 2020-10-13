---
title: Ligue-se a um VM do Windows utilizando o Azure Bastion
description: Neste artigo, aprenda a ligar-se a uma Máquina Virtual Azure que executa o Windows utilizando o Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/12/2020
ms.author: cherylmc
ms.openlocfilehash: 8b9653daf945b6a189bc528cd00de832ae97c03b
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978147"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Ligue-se a uma máquina virtual Windows usando Azure Bastion

Utilizando o Azure Bastion, pode ligar-se de forma segura e perfeita às suas máquinas virtuais sobre a SSL diretamente no portal Azure. Quando utiliza o Azure Bastion, os seus VMs não requerem um cliente, agente ou software adicional. Este artigo mostra-lhe como ligar-se aos seus VMs windows. Para obter informações sobre a ligação a um VM Linux, consulte [Connect to a VM usando Azure Bastion - Linux](bastion-connect-vm-ssh.md).

O Azure Bastion proporciona uma conectividade segura a todos os VMs na rede virtual em que é a provisionado. A utilização do Azure Bastion protege as suas máquinas virtuais de expor portas RDP/SSH ao mundo exterior, ao mesmo tempo que fornece acesso seguro utilizando RDP/SSH. Para obter mais informações, consulte [Descrição Geral](bastion-overview.md).

## <a name="before-you-begin"></a>Antes de começar

### <a name="install-the-bastion-host"></a>Instale o anfitrião bastonário

Certifique-se de que criou um anfitrião Azure Bastion para a rede virtual em que o VM está localizado. Uma vez que o serviço Bastion é aloque e implementado na sua rede virtual, pode usá-lo para ligar a qualquer VM da rede virtual. Para montar um anfitrião do Azure Bastion, consulte [criar um anfitrião do Azure Bastion.](bastion-create-host-portal.md)

### <a name="required-roles"></a>Funções necessárias

Para estabelecer uma ligação, são necessárias as seguintes funções:

* Papel do leitor na máquina virtual
* Função do leitor no NIC com IP privado da máquina virtual
* Papel do leitor no recurso Azure Bastion

### <a name="ports"></a>Portas

Para ligar ao Windows VM, tem de ter as seguintes portas abertas no seu VM Do Windows:

* Portas de entrada: RDP (3389)

## <a name="connect"></a><a name="rdp"></a>Ligar

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]
 
## <a name="next-steps"></a>Passos seguintes

Leia as [FAQ do Bastião.](bastion-faq.md)
