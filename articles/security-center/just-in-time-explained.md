---
title: Compreender o acesso a máquinas virtuais just-in-time no Azure Security Center
description: Este documento explica como o acesso ao VM just-in-time no Azure Security Center ajuda-o a controlar o acesso às suas máquinas virtuais Azure
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: 9a52596aa0dd5fa7b9a7226d2ae57259dab08d37
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93285738"
---
# <a name="understanding-just-in-time-jit-vm-access"></a>Compreensão do acesso just-in-time (JIT) à VM

Esta página explica os princípios por trás da funcionalidade de acesso vm do Azure Security Center (JIT) e a lógica por trás da recomendação.

Para aprender a aplicar JIT nos seus VMs utilizando o portal Azure (Centro de Segurança ou Máquinas Virtuais Azure) ou programaticamente, consulte [Como proteger as suas portas de gestão com o JIT](security-center-just-in-time.md).


## <a name="the-risk-of-open-management-ports-on-a-virtual-machine"></a>O risco de portas de gestão abertas numa máquina virtual

Os atores de ameaça caçam ativamente máquinas acessíveis com portas de gestão abertas, como RDP ou SSH. Todas as suas máquinas virtuais são alvos potenciais para um ataque. Quando um VM é comprometido com sucesso, é usado como ponto de entrada para atacar mais recursos dentro do seu ambiente.



## <a name="why-jit-vm-access-is-the-solution"></a>Por que o acesso JIT VM é a solução 

Como em todas as técnicas de prevenção da cibersegurança, o seu objetivo deve ser reduzir a superfície de ataque. Neste caso, isso significa ter menos portos abertos, especialmente portos de gestão.

Os seus utilizadores legítimos também usam estas portas, por isso não é prático mantê-los fechados.

Para resolver este dilema, o Azure Security Center oferece JIT. Com o JIT, pode bloquear o tráfego de entrada para os seus VMs, reduzindo a exposição a ataques, proporcionando fácil acesso à ligação aos VM quando necessário.



## <a name="how-jit-operates-with-network-security-groups-and-azure-firewall"></a>Como o JIT opera com grupos de segurança de rede e firewall Azure

Quando ativa o acesso em VM a tempo, pode selecionar as portas do VM para as quais o tráfego de entrada será bloqueado. O Security Center garante que existem regras de "negar todo o tráfego de entrada" para as suas portas selecionadas no [grupo de segurança](../virtual-network/network-security-groups-overview.md#security-rules) da rede (NSG) e nas [regras do Azure Firewall](../firewall/rule-processing.md). Estas regras restringem o acesso aos portos de gestão dos seus VMs Azure e defendem-nos de ataques. 

Se já existem outras regras para os portos selecionados, então as regras existentes têm prioridade sobre as novas regras de "negar todo o tráfego de entrada". Se não existirem regras existentes nas portas selecionadas, as novas regras têm prioridade máxima no NSG e no Azure Firewall.

Quando um utilizador solicita o acesso a um VM, o Security Center verifica se o utilizador tem permissões [de controlo de acesso baseado em funções (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) para esse VM. Se o pedido for aprovado, o Security Center configura os NSGs e a Azure Firewall para permitir o tráfego de entrada nas portas selecionadas a partir do endereço IP relevante (ou alcance), durante o tempo especificado. Após o tempo ter expirado, o Centro de Segurança restaura os NSGs aos estados anteriores. As ligações já estabelecidas não são interrompidas.

> [!NOTE]
> O JIT não suporta VMs protegidos por Firewalls Azure controlados pelo [Azure Firewall Manager](../firewall-manager/overview.md).




## <a name="how-security-center-identifies-which-vms-should-have-jit-applied"></a>Como o Centro de Segurança identifica quais os VMs que devem ter JIT aplicado

O diagrama abaixo mostra a lógica que o Centro de Segurança aplica ao decidir como categorizar os seus VMs suportados: 

[![Fluxo lógico da máquina virtual (JIT) just-in-time (VM)](media/just-in-time-explained/jit-logic-flow.png)](media/just-in-time-explained/jit-logic-flow.png#lightbox)

Quando o Security Center encontra uma máquina que pode beneficiar do JIT, adiciona essa máquina ao separador de **recursos insalubres** da recomendação. 

![Recomendação de acesso à máquina virtual (JIT) just-in-time (VM)](./media/just-in-time-explained/unhealthy-resources.png)


## <a name="faq---questions-about-just-in-time-virtual-machine-access"></a>FAQ - Perguntas sobre o acesso de máquina virtual a tempo

### <a name="what-permissions-are-needed-to-configure-and-use-jit"></a>Que permissões são necessárias para configurar e utilizar o JIT?

O JIT requer [que o Azure Defender seja](defender-for-servers-introduction.md) ativado na subscrição. 

**As** funções reader e **SecurityReader** podem visualizar o estado e os parâmetros do JIT.

Se quiser criar funções personalizadas que possam funcionar com o JIT, precisará dos detalhes da tabela abaixo.

> [!TIP]
> Para criar um papel menos privilegiado para os utilizadores que precisam solicitar o acesso do JIT a um VM, e não realizar outras operações JIT, use o [script Set-JitLeastPrivilegedRole](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20scripts/JIT%20Custom%20Role) a partir das páginas comunitárias do Centro de Segurança GitHub.

| Para permitir que um utilizador: | Permissões a definir|
| --- | --- |
|Configure ou edite uma política de JIT para um VM | *Atribua estas ações ao papel:*  <ul><li>No âmbito de uma subscrição ou grupo de recursos associado ao VM:<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> No âmbito de uma subscrição ou grupo de recursos de VM: <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|Solicite acesso ao JIT a um VM | *Atribuir estas ações ao utilizador:*  <ul><li>No âmbito de uma subscrição ou grupo de recursos associado ao VM:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>No âmbito de uma subscrição ou grupo de recursos associado ao VM:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  No âmbito de um grupo de subscrição ou de recursos ou VM:<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  No âmbito de um grupo de subscrição ou de recursos ou VM:<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|
|Leia as políticas do JIT| *Atribuir estas ações ao utilizador:*  <ul><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/read`</li><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action`</li><li>`Microsoft.Security/policies/read`</li><li>`Microsoft.Security/pricings/read`</li><li>`Microsoft.Compute/virtualMachines/read`</li><li>`Microsoft.Network/*/read`</li>|
|||





## <a name="next-steps"></a>Passos seguintes

Esta página explicou _por que razão_ deve ser utilizado o acesso à máquina virtual (VM) just-in-time (JIT). 

Avance para o artigo de como saber sobre como permitir o JIT e solicitar o acesso aos seus VMs habilitados a JIT:

> [!div class="nextstepaction"]
> [Como garantir os seus portos de gestão com o JIT](security-center-just-in-time.md)