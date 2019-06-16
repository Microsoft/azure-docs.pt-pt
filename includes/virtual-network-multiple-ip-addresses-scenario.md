---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 11/09/2018
ms.author: genli
ms.openlocfilehash: 3df4108907a4e1e65a444faf1049163966b7accf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66170771"
---
## <a name="scenario"></a>Cenário
Uma VM com um único NIC é criada e ligada a uma rede virtual. A VM requer três diferentes *privada* IP endereços e duas *público* endereços IP. Os endereços IP são atribuídos para as seguintes configurações de IP:

* **IPConfig-1:** Atribui um *estático* endereço IP privado e um *estático* endereço IP público.
* **IPConfig-2:** Atribui um *estático* endereço IP privado e um *estático* endereço IP público.
* **IPConfig-3:** Atribui um *estático* endereço IP privado e nenhum endereço IP público.
  
    ![Vários endereços IP](./media/virtual-network-multiple-ip-addresses-scenario/multiple-ipconfigs.png)

As configurações de IP são associadas à NIC quando é criada a NIC e a NIC está ligada à VM quando a VM é criada. Os tipos de endereços IP utilizados para o cenário são para fins de ilustração. Pode atribuir qualquer tipos de endereço e a atribuição de IP que necessita.

> [!NOTE]
> Embora os passos neste artigo atribui todas as configurações de IP para um único NIC, também pode atribuir várias configurações de IP para qualquer NIC numa VM com várias NICs. Para saber como criar uma VM com várias NICs, leia os [criar uma VM com várias NICs](../articles/virtual-machines/windows/multiple-nics.md) artigo.