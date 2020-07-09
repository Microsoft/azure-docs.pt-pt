---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: e128f3c67a41322d9c25a8d6941e937729760bf4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "67184205"
---
Neste passo, cria-se uma regra de firewall para abrir a porta da sonda para o ponto final equilibrado de carga (59999, conforme especificado anteriormente) e outra regra para abrir a porta de ouvintes do grupo de disponibilidade. Como criou o ponto final equilibrado de carga nos VMs que contêm réplicas de grupo de disponibilidade, precisa de abrir a porta da sonda e a porta do ouvinte nos respetivos VMs.

1. Em VMs que acolhem réplicas, inicie **o Windows Firewall com Segurança Avançada.**

2. Clique nas **Regras de Entrada à**Direita e, em seguida, clique em Nova **Regra**.

3. Na página **'Tipo regra',** selecione **Porta**e, em seguida, clique em **Seguinte**.

4. Na página **Protocolo e Portas,** selecione **TCP**, tipo **59999** na caixa **de portas locais específica** e, em seguida, clique em **Seguinte**.

5. Na página **Ação,** mantenha **a ligação** selecionada e, em seguida, clique em **Seguinte**.

6. Na página **'Perfil',** aceite as definições predefinidos e, em seguida, clique em **Seguinte**.

7. Na página **Nome,** na caixa de texto **'Nome',** especifique um nome de regra, como **Porta sonda Always On Listener**, e, em seguida, clique em **Terminar**.

8. Repita os passos anteriores para a porta de ouvintes do grupo de disponibilidade (conforme especificado no parâmetro $EndpointPort do script) e, em seguida, especifique um nome de regra apropriado, como **Always On Listener Port**.

