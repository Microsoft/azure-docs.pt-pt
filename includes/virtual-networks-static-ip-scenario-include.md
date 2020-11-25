---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 02/14/2020
ms.author: genli
ms.openlocfilehash: 280943bd965c4799ce294321129d1088be9c0caf
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015935"
---
## <a name="scenario"></a>Cenário

Para melhor ilustrar como configurar um endereço IP estático para um VM, este documento utiliza este cenário:

![Cenário de rede virtual: Subesí redes frontais e traseiras, com um endereço IP estático para a sub-rede frontal](./media/virtual-networks-static-ip-scenario-include/static-ip-scenario.png)

Neste cenário, cria-se um VM chamado *DNS01* na sub-rede *FrontEnd* e, em seguida, define-o para utilizar um endereço IP estático de *192.168.1.1.101*.
