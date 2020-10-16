---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 091e4dc0759ce1b227349d5d1b6b08b9eda66ee0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84237702"
---
* A conversão necessita de um reinício da VM, por isso, agende a migração das VMs durante uma janela de manutenção pré-existente. 

* A conversão não é reversível. 

* Esteja ciente de que qualquer utilizadores com a função [de Contribuinte de Máquina Virtual](../articles/role-based-access-control/built-in-roles.md#virtual-machine-contributor) não será capaz de alterar o tamanho VM (como poderiam pré-conversão). Isto porque os VMs com discos geridos exigem que o utilizador tenha a permissão microsoft.Compute/disks/write nos discos DE.

* Certifique-se de que testa a conversão. Migre uma máquina virtual de teste antes de efetuar a migração na produção.

* Durante a conversão, deve desalocar a VM. A VM recebe um novo endereço IP quando é iniciada após a conversão. Se necessário, pode [atribuir um endereço IP estático](../articles/virtual-network/public-ip-addresses.md) à VM.

* Reveja a versão mínima do agente Azure VM necessário para apoiar o processo de conversão. Para obter informações sobre como verificar e atualizar a versão do seu agente, consulte [o suporte mínimo da versão para agentes VM em Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)