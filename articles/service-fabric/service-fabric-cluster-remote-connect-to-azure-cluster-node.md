---
title: Ligação remota a um nó de cluster de tecido de serviço Azure
description: Aprenda a ligar-se remotamente a uma instância de conjunto de escala (um nó de cluster de tecido de serviço).
ms.topic: conceptual
ms.date: 03/23/2018
ms.openlocfilehash: c7ca4f0d5dce1b19837a44d5c9749f3e1293c6b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458326"
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Ligação remota a uma instância de conjunto de escala de máquina virtual ou um nó de cluster
Num cluster de tecido de serviço em funcionamento em Azure, cada tipo de nó de cluster que define [configura uma escala separada](service-fabric-cluster-nodetypes.md)da máquina virtual .  Pode ligar-se remotamente a instâncias específicas de conjuntos de escala (nós de cluster).  Ao contrário dos VMs de instância única, as instâncias de conjunto de escala não têm os seus próprios endereços IP virtuais. Isto pode ser um desafio quando procura um endereço IP e uma porta que pode usar para ligar remotamente a uma instância específica.

Para encontrar um endereço IP e uma porta que possa utilizar para ligar remotamente a uma instância específica, complete os seguintes passos.

1. Obtenha as regras nat de entrada para o Protocolo de Ambiente de Trabalho Remoto (RDP).

    Tipicamente, cada tipo de nó definido no seu cluster tem o seu próprio endereço IP virtual e um equilibrador de carga dedicado. Por predefinição, o equilibrista de carga para um tipo de nó é nomeado com o seguinte formato: *LB-{cluster-name}-{node-type}*; por exemplo, *LB-mycluster-FrontEnd*. 
    
    Na página do seu equilibrista de carga no portal Azure, selecione **Definições** > **Regras NAT de entrada:** 

    ![Regras na NAT do equilibrista de carga](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/lb-window.png)

    A seguinte imagem mostra as regras nat de entrada para um tipo de nó chamado FrontEnd: 

    ![Regras na NAT do equilibrista de carga](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/nat-rules.png)

    Para cada nó, o endereço IP aparece na coluna **DESTINATION,** a coluna **TARGET** dá a instância de conjunto de escala, e a coluna **SERVICE** fornece o número da porta. Para ligação remota, as portas são alocados a cada nó em ordem ascendente a partir da porta 3389.

    Também pode encontrar as regras na `Microsoft.Network/loadBalancers` nat inbound na secção do modelo de Gestor de Recursos para o seu cluster.
    
2. Para confirmar a porta de entrada para o mapeamento da porta-alvo para um nó, pode clicar na sua regra e olhar para o valor da **porta Target.** A imagem seguinte mostra a regra NAT de entrada para o nó **FrontEnd (Instância 1)** no passo anterior. Note-se que, embora o número de porta (de entrada) seja 3390, a porta-alvo é mapeada para a porta 3389, a porta para o serviço RDP no alvo.  

    ![Mapeamento de porta-alvo](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/port-mapping.png)

    Por predefinição, para os clusters Windows, a porta-alvo é a porta-alvo 3389, que mapeia o serviço RDP no nó alvo. Para os clusters Linux, a porta-alvo é a porta-alvo 22, que mapeia o serviço Secure Shell (SSH).

3. Ligue-se remotamente ao nó específico (instância de conjunto de escala). Pode utilizar o nome de utilizador e a palavra-passe que definiu quando criou o cluster ou quaisquer outras credenciais configuradas. 

    A imagem que se segue mostra a utilização da Ligação remota de ambiente de trabalho para ligar ao nó **FrontEnd (Instância 1)** num cluster Windows:
    
    ![Ligação de Ambiente de Trabalho Remoto](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/rdp-connect.png)

    Nos nós Linux, pode ligar-se ao SSH (o exemplo seguinte reutiliza o mesmo endereço IP e porta para brevidade):

    ``` bash
    ssh SomeUser@40.117.156.199 -p 3390
    ```


Para os próximos passos, leia os seguintes artigos:
* Veja a [visão geral da funcionalidade "Implementar em qualquer lugar" e uma comparação com clusters geridos pelo Azure](service-fabric-deploy-anywhere.md).
* Saiba mais sobre [a segurança do cluster.](service-fabric-cluster-security.md)
* [Atualizar os valores da gama de porta rdp](./scripts/service-fabric-powershell-change-rdp-port-range.md) em VMs de cluster após a implantação
* Alterar o nome de utilizador e a [palavra-passe](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) do administrador para VMs de cluster

