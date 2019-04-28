---
title: Ligar remotamente a um nó de cluster do Azure Service Fabric | Documentos da Microsoft
description: Saiba como ligar remotamente a uma instância de conjunto de dimensionamento (um nó de cluster do Service Fabric).
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: aljo
ms.openlocfilehash: 4cc2d6355a0147c33048f1c2c27a3648b9223db4
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62110928"
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Ligar remotamente a uma instância de conjunto de dimensionamento de máquina virtual ou um nó de cluster
Num recurso de infraestrutura do serviço de cluster em execução no Azure, cada tipo de nó de cluster que definir [conjuntos de dimensionamento de máquinas virtuais separado](service-fabric-cluster-nodetypes.md).  Pode ligar remotamente às instâncias de conjunto de dimensionamento específico (nós de cluster).  Ao contrário das VMs de instância única, instâncias do conjunto de dimensionamento não tem seus próprios endereços IP virtuais. Isso pode ser um desafio quando está procurando um endereço IP e porta que pode utilizar para ligar remotamente a uma instância específica.

Para localizar um endereço IP e porta que pode utilizar para ligar remotamente a uma instância específica, conclua os passos seguintes.

1. Obter as regras NAT de entrada para o protocolo RDP (Remote Desktop).

    Normalmente, cada tipo de nó definido no seu cluster tem seu próprio endereço IP virtual e um balanceador de carga dedicado. Por predefinição, o Balanceador de carga para um tipo de nó com o nome com o seguinte formato: *LB-{nome do cluster}-{tipo de nó}*; por exemplo, *mycluster-LB-FrontEnd*. 
    
    Na página do seu Balanceador de carga no portal do Azure, selecione **configurações** > **regras NAT de entrada**: 

    ![Regras de NAT de entrada do Balanceador de carga](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/lb-window.png)

    Captura de ecrã seguinte mostra as regras NAT de entrada para um tipo de nó com o nome FrontEnd: 

    ![Regras de NAT de entrada do Balanceador de carga](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/nat-rules.png)

    Para cada nó, o endereço IP é apresentado na **destino** coluna, o **destino** coluna dá a instância de conjunto de dimensionamento e o **serviço** coluna fornece o número de porta. Para a ligação remota, as portas são alocadas a cada nó em crescente de início de pedidos com a porta 3389.

    Também pode encontrar as regras de NAT de entrada no `Microsoft.Network/loadBalancers` secção do modelo do Resource Manager para o seu cluster.
    
2. Para confirmar a porta de entrada para mapeamento de portas de destino para um nó, pode clicar a regra e examinar os **porta de destino** valor. Captura de ecrã seguinte mostra a regra NAT de entrada para o **front-end (1 instância)** nó no passo anterior. Tenha em atenção que, embora o número de porta (entrada) é 3390, a porta de destino está mapeada para a porta 3389, a porta para o serviço RDP no destino.  

    ![Mapeamento de portas de destino](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/port-mapping.png)

    Por predefinição, para os clusters do Windows, a porta de destino é a porta 3389, que mapeia para o serviço RDP no nó de destino. Para clusters do Linux, a porta de destino é a porta 22, que mapeia para o serviço de Secure Shell (SSH).

3. Ligar remotamente ao nó específico (instância de conjunto de dimensionamento). Pode utilizar o nome de utilizador e palavra-passe que configurou quando criou o cluster ou quaisquer outras credenciais que configurou. 

    A seguinte captura de ecrã mostra através da ligação de ambiente de trabalho remoto para ligar à **front-end (1 instância)** nó num cluster do Windows:
    
    ![Conexão de área de trabalho remoto](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/rdp-connect.png)

    Em nós do Linux, pode ligar com SSH (o exemplo seguinte reutiliza o mesmo endereço IP e a porta para fins de brevidade):

    ``` bash
    ssh SomeUser@40.117.156.199 -p 3390
    ```


Para obter passos seguintes, leia os artigos seguintes:
* Consulte a [descrição geral do recurso "Implementar em qualquer lugar" e uma comparação com os clusters geridos pelo Azure](service-fabric-deploy-anywhere.md).
* Saiba mais sobre [segurança de cluster](service-fabric-cluster-security.md).
* [Atualizar os valores de intervalo de portas RDP](./scripts/service-fabric-powershell-change-rdp-port-range.md) no cluster de VMs após a implementação
* [Alterar o nome de utilizador administrador e a palavra-passe](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) para VMs do cluster

