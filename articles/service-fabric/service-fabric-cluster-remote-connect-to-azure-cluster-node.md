---
title: Ligação remota a um nó de cluster de tecido de serviço Azure
description: Aprenda a ligar-se remotamente a uma instância definida em escala (um nó de cluster de tecido de serviço).
ms.topic: conceptual
ms.date: 03/23/2018
ms.openlocfilehash: 98d573af4fc2026134e75d4caf24a09e57e52c87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91268099"
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Ligação remota a uma placa de conjunto de escala de máquina virtual ou um nó de cluster
Num cluster de tecido de serviço em execução em Azure, cada tipo de nó de cluster que define [configura uma escala separada de máquina virtual](service-fabric-cluster-nodetypes.md).  Pode ligar-se remotamente a instâncias específicas definidas (nosdes de cluster).  Ao contrário dos VMs de instância única, as instâncias definidas em escala não têm os seus próprios endereços IP virtuais. Isto pode ser um desafio quando procura um endereço IP e uma porta que pode usar para ligar remotamente a uma instância específica.

Para encontrar um endereço IP e uma porta que possa utilizar para ligar remotamente a uma instância específica, complete os seguintes passos.

1. Obtenha as regras NAT de entrada para o Protocolo de Ambiente de Trabalho Remoto (RDP).

    Normalmente, cada tipo de nó definido no seu cluster tem o seu próprio endereço IP virtual e um equilibrador de carga dedicado. Por predefinição, o balançador de carga para um tipo de nó é nomeado com o seguinte formato: *LB-{cluster-name}-{node-type}*; por exemplo, *LB-mycluster-FrontEnd*. 
    
    Na página para o seu equilibrador de **Settings**carga no portal Azure, selecione  >  **Definições Inbound NAT regras**: 

    ![Screenshot de uma página de balanço de carga no portal Azure. No menu à esquerda, em DEFINIÇÕES, são selecionadas as regras INBOUND NAT.](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/lb-window.png)

    A imagem que se segue mostra as regras NAT de entrada para um tipo de nó chamado FrontEnd: 

    ![Screenshot mostrando as regras NAT de entrada para um equilibrador de carga. O Nome, Versão IP, Destino, Destino e Serviço estão listados para cada regra.](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/nat-rules.png)

    Para cada nó, o endereço IP aparece na coluna **DESTINO,** a coluna **TARGET** dá a instância definida na escala e a coluna **SERVICE** fornece o número de porta. Para ligação remota, as portas são atribuídas a cada nó por ordem ascendente a partir da porta 3389.

    Também pode encontrar as regras NAT de entrada na `Microsoft.Network/loadBalancers` secção do modelo de Gestor de Recursos para o seu cluster.
    
2. Para confirmar a porta de entrada para o mapeamento da porta alvo para um nó, pode clicar na sua regra e olhar para o valor da **porta Target.** A imagem seguinte mostra a regra NAT de entrada para o nó **FrontEnd (Exemplo 1)** no degrau anterior. Note que, embora o número de porta (entrada) seja 3390, a porta-alvo está mapeada para o porto 3389, a porta para o serviço RDP no alvo.  

    ![Mapeamento da porta-alvo](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/port-mapping.png)

    Por predefinição, para os clusters Windows, a porta-alvo é a porta 3389, que mapeia para o serviço RDP no nó-alvo. Para os clusters Linux, a porta-alvo é a porta 22, que mapeia para o serviço Secure Shell (SSH).

3. Ligue-se remotamente ao nó específico (instância definida em escala). Pode utilizar o nome de utilizador e a palavra-passe que definiu quando criou o cluster ou quaisquer outras credenciais que tenha configurado. 

    A imagem que se segue mostra a utilização da Ligação de Ambiente de Trabalho Remoto para ligar ao nó **FrontEnd (Exemplo 1)** num cluster Windows:
    
    ![Ligação de Ambiente de Trabalho Remoto](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/rdp-connect.png)

    Nos nos nóns Linux, pode ligar-se com SSH (o exemplo a seguir reutiliza o mesmo endereço IP e porta para a brevidade):

    ``` bash
    ssh SomeUser@40.117.156.199 -p 3390
    ```


Para os próximos passos, leia os seguintes artigos:
* Consulte a [visão geral da funcionalidade "Implementar em qualquer lugar" e uma comparação com os clusters geridos pelo Azure.](service-fabric-deploy-anywhere.md)
* Conheça a [segurança do cluster.](service-fabric-cluster-security.md)
* [Atualizar os valores da gama de portas RDP](./scripts/service-fabric-powershell-change-rdp-port-range.md) nos VMs do cluster após a implantação
* [Alterar o nome de utilizador e a palavra-passe](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) do administrador para os VMs do cluster

