---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 9f7e2760ef8bf06a2e680dce90c323672ca9d491
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "66416053"
---
* A conversão necessita de um reinício da VM, por isso, agende a migração das VMs durante uma janela de manutenção pré-existente. 

* A conversão não é reversível. 

* Esteja ciente de que quaisquer utilizadores com a função de Colaborador de [Máquina Virtual](../articles/role-based-access-control/built-in-roles.md#virtual-machine-contributor) não serão capazes de alterar o tamanho VM (como poderiam pré-conversão). Isto porque os VMs com discos geridos exigem que o utilizador tenha a permissão Microsoft.Compute/disks/write nos discos OS.

* Certifique-se de que testa a conversão. Migre uma máquina virtual de teste antes de efetuar a migração na produção.

* Durante a conversão, deve desalocar a VM. A VM recebe um novo endereço IP quando é iniciada após a conversão. Se necessário, pode [atribuir um endereço IP estático](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md) à VM.

* Reveja a versão mínima do agente Azure VM necessária para apoiar o processo de conversão. Para obter informações sobre como verificar e atualizar a versão do seu agente, consulte o suporte de [versão Mínima para agentes VM em Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)