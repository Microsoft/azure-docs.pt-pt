---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: e128f3c67a41322d9c25a8d6941e937729760bf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67184205"
---
Neste passo, cria-se uma regra de firewall para abrir a porta da sonda para o ponto final equilibrado (59999, conforme especificado anteriormente) e outra regra para abrir a porta de escuta do grupo de disponibilidade. Uma vez que criou o ponto final equilibrado em carga nos VMs que contêm réplicas de grupo de disponibilidade, precisa abrir a porta da sonda e a porta de escuta nos respetivos VMs.

1. Nos VMs que acolhem réplicas, inicie **o Windows Firewall com Segurança Avançada**.

2. Clique na direita regras de **entrada**e, em seguida, clique em **Nova Regra**.

3. Na página **'Tipo de regra',** selecione **Porta**, e, em seguida, clique **em Seguinte**.

4. Na página **Protocolo e Portas,** selecione **TCP,** tipo **59999** na caixa **de portos locais específicos** e, em seguida, clique em **Seguinte**.

5. Na página **Ação,** mantenha **permitir a ligação** selecionada e, em seguida, clique em **Seguinte**.

6. Na página **'Perfil',** aceite as definições predefinidas e, em seguida, clique em **Next**.

7. Na página **Nome,** na caixa de texto **Nome,** especifique um nome de regra, como a porta de **sonda Always On Listener,** e, em seguida, clique em **Terminar**.

8. Repita os passos anteriores para a porta de escuta do grupo de disponibilidade (conforme especificado anteriormente no parâmetro $EndpointPort do script), e, em seguida, especifique um nome de regra apropriado, como **a Porto de Escuta Sempre On Listener**.

