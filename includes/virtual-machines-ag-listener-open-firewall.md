---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: e128f3c67a41322d9c25a8d6941e937729760bf4
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66165423"
---
Neste passo, vai criar uma regra de firewall para abrir a porta de sonda para o endpoint com balanceamento de carga (59999, conforme especificado anteriormente) e outra regra para abrir a porta de serviço de escuta do grupo de disponibilidade. Uma vez que criou o ponto final com balanceamento de carga nas VMs que contêm as réplicas do grupo de disponibilidade, terá de abrir a porta de sonda e a porta do serviço de escuta nas respetivas VMs.

1. Em VMs que alojam as réplicas, inicie **Firewall do Windows com segurança avançada**.

2. Com o botão direito **regras de entrada**e, em seguida, clique em **nova regra**.

3. Sobre o **tipo de regra** página, selecione **porta**e, em seguida, clique em **seguinte**.

4. Sobre o **protocolo e portas** página, selecione **TCP**, tipo **59999** no **portas locais específicas** caixa e, em seguida, clique em  **Próxima**.

5. Sobre o **ação** página, manter **permitir a ligação** selecionada e, em seguida, clique em **seguinte**.

6. Sobre o **perfil** página, aceite as predefinições e, em seguida, clique em **próxima**.

7. No **nome** página, além do **nome** texto caixa, especifique um nome de regra, como **sempre no serviço de escuta de sonda de porta**e, em seguida, clique em **concluir**.

8. Repita os passos anteriores para a porta de serviço de escuta do grupo de disponibilidade (conforme especificado anteriormente no parâmetro $EndpointPort do script) e, em seguida, especifique um nome de regra adequada, tal como **sempre no serviço de escuta de porta**.

