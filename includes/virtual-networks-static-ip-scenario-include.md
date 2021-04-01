---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 02/14/2020
ms.author: genli
ms.openlocfilehash: 280943bd965c4799ce294321129d1088be9c0caf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96015935"
---
## <a name="scenario"></a>Scenario

Para melhor ilustrar como configurar um endereço IP estático para um VM, este documento utiliza este cenário:

![Cenário de rede virtual: Subesí redes frontais e traseiras, com um endereço IP estático para a sub-rede frontal](./media/virtual-networks-static-ip-scenario-include/static-ip-scenario.png)

Neste cenário, cria-se um VM chamado *DNS01* na sub-rede *FrontEnd* e, em seguida, define-o para utilizar um endereço IP estático de *192.168.1.1.101*.
