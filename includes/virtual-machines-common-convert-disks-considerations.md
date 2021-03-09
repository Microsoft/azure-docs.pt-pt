---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 72050ff4887642ba16d52948c1c1253ca01443c0
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102473878"
---
* A conversão reiniciará o VM, por isso agende a migração dos seus VMs durante uma janela de manutenção pré-existente. 

* A conversão não é reversível. 

* Esteja ciente de que qualquer utilizadores com a função [de Contribuinte de Máquina Virtual](../articles/role-based-access-control/built-in-roles.md#virtual-machine-contributor) não será capaz de alterar o tamanho VM (como poderiam pré-conversão). Isto porque os VMs com discos geridos exigem que o utilizador tenha a permissão microsoft.Compute/disks/write nos discos DE.

* Certifique-se de que testa a conversão. Migre uma máquina virtual de teste antes de efetuar a migração na produção.

* Durante a conversão, deve desalocar a VM. A VM recebe um novo endereço IP quando é iniciada após a conversão. Se necessário, pode [atribuir um endereço IP estático](../articles/virtual-network/public-ip-addresses.md) à VM.

* Reveja a versão mínima do agente Azure VM necessário para apoiar o processo de conversão. Para obter informações sobre como verificar e atualizar a versão do seu agente, consulte [o suporte mínimo da versão para agentes VM em Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)
