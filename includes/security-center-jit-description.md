---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 02/24/2020
ms.topic: include
ms.openlocfilehash: 5efa8b845952c8ab5b66b781fb534c3754239b4e
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84677440"
---
## <a name="attack-scenario"></a>Cenário de ataque

Ataques de força bruta geralmente visam portas de gestão como forma de ter acesso a um VM. Se for bem sucedido, um intruso pode assumir o controlo sobre o VM e estabelecer um ponto de apoio no seu ambiente.

Uma maneira de reduzir a exposição a um ataque de força bruta é limitar o tempo que um porto está aberto. Os portos de gestão não precisam de estar sempre abertos. Eles só precisam de estar abertos enquanto estiver ligado ao VM, por exemplo, para executar tarefas de gestão ou manutenção. Quando o tempo é permitido, o Security Center utiliza regras [do Grupo de Segurança](../articles/virtual-network/security-overview.md#security-rules) da Rede (NSG) e do Azure Firewall, que restringem o acesso às portas de gestão para que não possam ser alvo de atacantes.

## <a name="how-does-jit-access-work"></a>Como funciona o acesso ao JIT?

Quando o tempo é apenas a tempo, o Centro de Segurança bloqueia o tráfego de entrada para os seus VMs Azure, criando uma regra NSG. Selecione as portas do VM para as quais o tráfego de entrada será bloqueado. Estas portas são controladas pela solução just-in-time.

Quando um utilizador solicita o acesso a um VM, o Security Center verifica se o utilizador tem permissões [de Controlo de Acesso Baseado em Fun (RBAC)](../articles/role-based-access-control/role-assignments-portal.md) para esse VM. Se o pedido for aprovado, o Security Center configura automaticamente os Grupos de Segurança da Rede (NSGs) e a Azure Firewall para permitir o tráfego de entrada nas portas selecionadas e endereços IP de origem solicitados, durante o tempo especificado. Após o tempo ter expirado, o Centro de Segurança restaura os NSGs aos estados anteriores. No entanto, as ligações já estabelecidas não estão a ser interrompidas.

 > [!NOTE]
 > Se um pedido de acesso JIT for aprovado para um VM por trás de um Azure Firewall, então o Centro de Segurança muda automaticamente as regras de política de NSG e firewall. Durante o tempo especificado, as regras permitem o tráfego de entrada nas portas selecionadas e endereços ip de origem solicitados ou intervalos. Depois de terminado o tempo, o Centro de Segurança restaura as regras de firewall e NSG para os estados anteriores.


## <a name="roles-that-can-read-jit-policies"></a>Papéis que podem ler as políticas do JIT

**As** funções de Leitor e **SecurityReader** podem ler políticas.

## <a name="permissions-needed-to-configure-and-use-jit"></a>Permissões necessárias para configurar e utilizar o JIT

Se quiser criar funções personalizadas que possam funcionar com o JIT, precisará dos seguintes detalhes:

| Para permitir que um utilizador: | Permissões a definir|
| --- | --- |
| Configure ou edite uma política de JIT para um VM | *Atribua estas ações ao papel:*  <ul><li>No âmbito de uma subscrição ou grupo de recursos associado ao VM:<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> No âmbito de uma subscrição ou grupo de recursos de VM: <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|Solicite acesso ao JIT a um VM | *Atribuir estas ações ao utilizador:*  <ul><li>No âmbito de uma subscrição ou grupo de recursos associado ao VM:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>No âmbito de uma subscrição ou grupo de recursos associado ao VM:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  No âmbito de um grupo de subscrição ou de recursos ou VM:<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  No âmbito de um grupo de subscrição ou de recursos ou VM:<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|
|Leia as políticas do JIT| *Atribuir estas ações ao utilizador:*  <ul><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/read`</li><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action`</li><li>`Microsoft.Security/policies/read`</li><li>`Microsoft.Compute/virtualMachines/read`</li><li>`Microsoft.Network/*/read`</li>|