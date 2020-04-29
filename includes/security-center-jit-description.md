---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 02/24/2020
ms.topic: include
ms.openlocfilehash: c77849b2285283a34e6adf84dc3845a4076407af
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77597950"
---
## <a name="attack-scenario"></a>Cenário de ataque

A força bruta ataca comumente os portos de gestão como forma de ter acesso a um VM. Se for bem sucedido, um intruso pode assumir o controlo sobre o VM e estabelecer um ponto de apoio no seu ambiente.

Uma maneira de reduzir a exposição a um ataque de força bruta é limitar o tempo que um porto está aberto. Os portos de gestão não precisam de estar sempre abertos. Só precisam de estar abertos enquanto estiver ligado ao VM, por exemplo, para realizar tarefas de gestão ou manutenção. Quando o tempo é ativado, o Security Center utiliza as regras do grupo de segurança de [rede](../articles/virtual-network/security-overview.md#security-rules) (NSG) e do Azure Firewall, que restringem o acesso às portas de gestão para que não possam ser alvo de atacantes.

![Cenário just-in-time](../articles/security-center/media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>Como funciona o acesso ao JIT?

Quando o tempo está ativado, o Security Center bloqueia o tráfego de entrada para os seus VMs Azure, criando uma regra NSG. Selecione as portas do VM para as quais o tráfego de entrada será bloqueado. Estes portos são controlados pela solução just-in-time.

Quando um utilizador solicita acesso a um VM, o Security Center verifica se o utilizador tem permissões de Controlo de Acesso Baseado em [Funções (RBAC)](../articles/role-based-access-control/role-assignments-portal.md) para esse VM. Se o pedido for aprovado, o Security Center configura automaticamente os Grupos de Segurança da Rede (NSGs) e o Firewall Azure para permitir o tráfego de entrada nas portas selecionadas e endereços IP de origem solicitados ou intervalos, pelo período de tempo especificado. Após o tempo expirado, o Centro de Segurança restaura os NSGs aos seus estados anteriores. No entanto, as ligações já estabelecidas não estão a ser interrompidas.

 > [!NOTE]
 > Se for aprovado um pedido de acesso JIT para um VM atrás de uma Firewall Azure, o Security Center altera automaticamente as regras de política de NSG e firewall. Durante o período de tempo especificado, as regras permitem o tráfego de entrada para as portas selecionadas e endereços IP de origem solicitados ou gamas. Após o fim do tempo, o Security Center restaura as regras da firewall e da NSG para os seus estados anteriores.


## <a name="permissions-needed-to-configure-and-use-jit"></a>Permissões necessárias para configurar e usar JIT

| Para permitir que um utilizador: | Permissões para definir|
| --- | --- |
| Configure ou edite uma política de JIT para um VM | *Atribuir estas ações ao papel:*  <ul><li>No âmbito de um grupo de subscrição ou de recursos associado ao VM:<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> No âmbito de uma subscrição ou grupo de recursos de VM: <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|Solicitar acesso jIT a um VM | *Atribuir estas ações ao utilizador:*  <ul><li>No âmbito de um grupo de subscrição ou de recursos associado ao VM:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>No âmbito de um grupo de subscrição ou de recursos associado ao VM:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  No âmbito de um grupo de subscrição ou de recursos ou VM:<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  No âmbito de um grupo de subscrição ou de recursos ou VM:<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|