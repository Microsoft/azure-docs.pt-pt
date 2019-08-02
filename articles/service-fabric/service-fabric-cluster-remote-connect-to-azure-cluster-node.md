---
title: Conexão remota a um nó de Cluster Service Fabric do Azure | Microsoft Docs
description: Saiba como se conectar remotamente a uma instância do conjunto de dimensionamento (um nó de Cluster Service Fabric).
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: atsenthi
ms.openlocfilehash: 12508fd5297691f06bce46e056527672083c3a91
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599940"
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Conectar remotamente a uma instância do conjunto de dimensionamento de máquinas virtuais ou a um nó de cluster
Em um Cluster Service Fabric em execução no Azure, cada tipo de nó de cluster que você define [configura uma escala separada de máquina virtual](service-fabric-cluster-nodetypes.md).  Você pode conectar-se remotamente a instâncias específicas do conjunto de dimensionamento (nós de cluster).  Ao contrário das VMs de instância única, as instâncias do conjunto de dimensionamento não têm seus próprios endereços IP virtuais. Isso pode ser desafiador quando você está procurando um endereço IP e uma porta que você pode usar para se conectar remotamente a uma instância específica.

Para localizar um endereço IP e uma porta que você pode usar para se conectar remotamente a uma instância específica, conclua as etapas a seguir.

1. Obtenha as regras de NAT de entrada para protocolo RDP (RDP).

    Normalmente, cada tipo de nó definido em seu cluster tem seu próprio endereço IP virtual e um balanceador de carga dedicado. Por padrão, o balanceador de carga para um tipo de nó é nomeado com o seguinte formato: *Lb-{nome-do-cluster}-{tipo de nó}* ; por exemplo, *lb-mycluster-frontend*. 
    
    Na página do balanceador de carga no portal do Azure, selecione **configurações** > **regras de NAT de entrada**: 

    ![Regras NAT de entrada do balanceador de carga](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/lb-window.png)

    A captura de tela a seguir mostra as regras de NAT de entrada para um tipo de nó chamado FrontEnd: 

    ![Regras NAT de entrada do balanceador de carga](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/nat-rules.png)

    Para cada nó, o endereço IP aparece na coluna **destino** , a coluna de **destino** fornece a instância do conjunto de dimensionamento e a coluna **serviço** fornece o número da porta. Para a conexão remota, as portas são alocadas para cada nó em ordem crescente, começando com a porta 3389.

    Você também pode encontrar as regras NAT de entrada na `Microsoft.Network/loadBalancers` seção do modelo do Resource Manager para seu cluster.
    
2. Para confirmar a porta de entrada para o mapeamento de porta de destino de um nó, você pode clicar em sua regra e examinar o valor da **porta de destino** . A captura de tela a seguir mostra a regra NAT de entrada para o nó **frontend (instância 1)** na etapa anterior. Observe que, embora o número da porta (de entrada) seja 3390, a porta de destino é mapeada para a porta 3389, a porta para o serviço RDP no destino.  

    ![Mapeamento de porta de destino](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/port-mapping.png)

    Por padrão, para clusters do Windows, a porta de destino é a porta 3389, que é mapeada para o serviço RDP no nó de destino. Para clusters do Linux, a porta de destino é a porta 22, que é mapeada para o serviço de Secure Shell (SSH).

3. Conectar remotamente ao nó específico (instância do conjunto de dimensionamento). Você pode usar o nome de usuário e a senha que você definiu quando criou o cluster ou qualquer outra credencial configurada. 

    A captura de tela a seguir mostra o uso de Conexão de Área de Trabalho Remota para se conectar ao nó **frontend (instância 1)** em um cluster do Windows:
    
    ![Conexão de Área de Trabalho Remota](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/rdp-connect.png)

    Em nós do Linux, você pode se conectar com o SSH (o exemplo a seguir reutiliza o mesmo endereço IP e porta para fins de brevidade):

    ``` bash
    ssh SomeUser@40.117.156.199 -p 3390
    ```


Para as próximas etapas, leia os seguintes artigos:
* Consulte a [visão geral do recurso "implantar em qualquer lugar" e uma comparação com clusters gerenciados pelo Azure](service-fabric-deploy-anywhere.md).
* Saiba mais sobre a [segurança do cluster](service-fabric-cluster-security.md).
* [Atualizar os valores de intervalo de porta RDP](./scripts/service-fabric-powershell-change-rdp-port-range.md) em VMs de cluster após a implantação
* [Alterar o nome de usuário e a senha do administrador](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) para VMs do cluster

