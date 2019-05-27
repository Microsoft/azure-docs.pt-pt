---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: bf26af7fa4b1b31514fb82c5e28a85154b2e274a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66158472"
---
* A conversão necessita de um reinício da VM, por isso, agende a migração das VMs durante uma janela de manutenção pré-existente. 

* A conversão não é reversível. 

* Tenha em atenção que qualquer os utilizadores com o [contribuinte de Máquina Virtual](../articles/role-based-access-control/built-in-roles.md#virtual-machine-contributor) função não será possível alterar o tamanho da VM (como pré-conversão de). Isto acontece porque as VMs com discos geridos pedir ao utilizador que tem a permissão de Microsoft.Compute/disks/write nos discos de SO.

* Certifique-se de que testa a conversão. Migre uma máquina virtual de teste antes de efetuar a migração na produção.

* Durante a conversão, deve desalocar a VM. A VM recebe um novo endereço IP quando é iniciada após a conversão. Se necessário, pode [atribuir um endereço IP estático](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md) à VM.

* Os VHDs originais e a conta de armazenamento utilizada pela VM antes da conversão não são eliminados. Continuam a incorrer em custos. Para evitar a cobrança destes artefactos, elimine os blobs de VHD originais depois de verificar que a conversão está concluída.

* Reveja a versão mínima do agente de VM do Azure necessário para suportar o processo de conversão. Para obter informações sobre como verificar e atualizar a versão do agente, consulte [suporte para a versão mínima para os agentes VM no Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)