---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 361d0ce5091d80198d47e4ad164f7cba8e21a55d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58099515"
---
Uma máquina virtual *personalizada* significa simplesmente uma máquina virtual criada com uma **Aplicação em destaque** a partir do **Marketplace** porque trabalha bastante por si. No entanto, pode continuar a realizar escolhas de configuração que incluem os seguintes itens:

* Ligar a máquina virtual a uma rede virtual.
* Instalar o Agente da Máquina Virtual do Azure e as Extensões da Máquina Virtual do Azure, como por exemplo para antimalware.
* Adicionar a máquina virtual a serviços cloud existentes.
* Adicionar a máquina virtual a uma conta de Armazenamento existente.
* Adicionar a máquina virtual a um conjunto de disponibilidade.

<!--
> [!IMPORTANT]
> If you want your virtual machine to use a virtual network so you can connect to it directly by host name or set up cross-premises connections, make sure that you specify the virtual network when you create the virtual machine. A virtual machine can be configured to join a virtual network only when you create the virtual machine. For details on virtual networks, see [Azure Virtual Network overview](../articles/virtual-network/virtual-networks-overview.md).
>
>
 -->

> [!IMPORTANT]
> Se pretender que a máquina virtual utilize uma rede virtual, certifique-se de que especifica a rede virtual ao criar a máquina virtual.
> 
> * Duas vantagens de utilizar uma rede virtual são a ligação direta à máquina virtual e a configuração de ligações entre locais.
> 
> * Uma máquina virtual pode ser configurada para associar uma rede virtual apenas quando cria a máquina virtual. Para obter detalhes sobre redes virtuais, consulte [Azure Virtual Network overview (Descrição geral da Rede Virtual do Azure)](../articles/virtual-network/virtual-networks-overview.md).

## <a name="to-create-the-virtual-machine"></a>Para criar a máquina virtual
