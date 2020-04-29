---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 11/09/2018
ms.author: genli
ms.openlocfilehash: 3df4108907a4e1e65a444faf1049163966b7accf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67184181"
---
## <a name="scenario"></a>Cenário
Um VM com um único NIC é criado e conectado a uma rede virtual. O VM requer três endereços IP *privados* diferentes e dois endereços IP *públicos.* Os endereços IP são atribuídos às seguintes configurações IP:

* **IPConfig-1:** Atribui um endereço IP *privado estático* e um endereço IP público *estático.*
* **IPConfig-2:** Atribui um endereço IP *privado estático* e um endereço IP público *estático.*
* **IPConfig-3:** Atribui um endereço IP *privado estático* e nenhum endereço IP público.
  
    ![Vários endereços IP](./media/virtual-network-multiple-ip-addresses-scenario/multiple-ipconfigs.png)

As configurações IP estão associadas ao NIC quando o NIC é criado e o NIC é ligado ao VM quando o VM é criado. Os tipos de endereços IP utilizados para o cenário são para ilustração. Pode atribuir os tipos de endereço ip e atribuição que necessite.

> [!NOTE]
> Embora os passos deste artigo atribuam todas as configurações ip a um único NIC, também pode atribuir várias configurações ip a qualquer NIC num VM multi-NIC. Para aprender a criar um VM com vários NICs, leia o [Create a VM com vários artigos nics.](../articles/virtual-machines/windows/multiple-nics.md)