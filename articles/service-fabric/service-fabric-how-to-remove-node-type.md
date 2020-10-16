---
title: Remova um nó no tecido de serviço Azure / Microsoft Docs
description: Aprenda a remover um tipo de nó de um cluster de tecido de serviço em execução em Azure.
author: inputoutputcode
manager: sridmad
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: chrpap
ms.openlocfilehash: ede999bee9ce1a4a9dd10652a2c52a840d5b24be
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88163582"
---
# <a name="how-to-remove-a-service-fabric-node-type"></a>Como remover um nó de tecido de serviço
Este artigo descreve como escalar um cluster de tecido de serviço Azure removendo um tipo de nó existente de um cluster. Um cluster de tecido de serviço é um conjunto de máquinas virtuais ou físicas ligadas à rede em que os seus microserviços são implantados e geridos. Uma máquina ou VM que faz parte de um aglomerado é chamada de nó. Os conjuntos de escala de máquinas virtuais são um recurso computacional Azure que utiliza para implementar e gerir uma coleção de máquinas virtuais como conjunto. Todos os tipos de nó definidos num cluster Azure [são configurado como um conjunto de escala separada](service-fabric-cluster-nodetypes.md). Cada tipo de nó pode então ser gerido separadamente. Depois de criar um cluster de Tecido de Serviço, pode escalar um cluster horizontalmente removendo um tipo de nó (conjunto de balança de máquina virtual) e todos os seus nós.  Pode escalar o cluster a qualquer momento, mesmo quando as cargas de trabalho estão a funcionar no cluster.  À medida que o cluster escala, as suas aplicações também escalam automaticamente.

> [!WARNING]
> Não é aconselhável utilizar esta abordagem para remover um nó de um aglomerado de produção. É um comando perigoso, uma vez que elimina o recurso de escala de máquina virtual por trás do tipo de nó. 

## <a name="durability-characteristics"></a>Características de durabilidade
A segurança é prioritária sobre a velocidade quando se utiliza remove-AzServiceFabricNodeType. O tipo de nó deve ser nível de durabilidade prateado ou [dourado,](./service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)porque:
- O Bronze não lhe dá quaisquer garantias sobre a poupança de informações do Estado.
- A durabilidade prateada e dourada prende qualquer alteração no conjunto de escala.
- O ouro também lhe dá controlo sobre as atualizações Azure por baixo do conjunto de escala.

O Tecido de Serviço "orquestra" alterações e atualizações subjacentes para que os dados não se percam. No entanto, quando remover um nó com durabilidade de Bronze, pode perder informações do estado. Se estiver a remover um nó primário e a sua aplicação for apátrida, o Bronze é aceitável. Quando se tem cargas de trabalho declaradas em produção, a configuração mínima deve ser Silver. Da mesma forma, para cenários de produção, o tipo de nó primário deve ser sempre prata ou ouro.

### <a name="more-about-bronze-durability"></a>Mais sobre a durabilidade do Bronze

Ao remover um nó que seja bronze, todos os nós do tipo nó descem imediatamente. O Tecido de Serviço não prende nenhuma escala de nós de bronze, assim todos os VMs descem imediatamente. Se tivesse algo de sossídico nesses nós, os dados perdem-se. Agora, mesmo que fosses apátrida, todos os nós do Tecido de Serviço participam no ringue, para que um bairro inteiro possa estar perdido, o que pode desestabilizar o próprio aglomerado.

## <a name="remove-a-node-type"></a>Remover um tipo de nó

1. Por favor, cuide destes pré-requisitos antes de iniciar o processo.

    - O aglomerado é saudável.
    - Continuará a haver capacidade suficiente após a remoção do tipo nó, por exemplo. número de nós para colocar a contagem de réplicas necessária.

2. Mova todos os serviços que tenham restrições de colocação para utilizar o tipo de nó fora do tipo de nó.

    - Modificar aplicação / Manifesto de serviço para deixar de fazer referência ao tipo de nó.
    - Desdobre a mudança.

    Em seguida, valide que:
    - Todos os serviços modificados acima já não estão a funcionar no nó pertencente ao tipo nó.
    - Todos os serviços são saudáveis.

3. Desmarcar o tipo de nó como não primário (saltar para tipos de nó não primários)

    - Localize o modelo do Gestor de Recursos Azure utilizado para a implementação.
    - Encontre a secção relacionada com o tipo de nó na secção Tecido de Serviço.
    - A alteração é propriedadeprimária para falsa. ** Não retire a secção relacionada com o tipo de nó nesta tarefa.
    - Implemente o modelo de Gestor de Recursos Azure modificado. ** Dependendo da configuração do cluster, este passo pode demorar algum tempo.
    
    Em seguida, valide que:
    - A Secção de Tecido de Serviço no Portal indica que o cluster está pronto.
    - O agrupamento é saudável.
    - Nenhum dos nós pertencentes ao tipo nó está marcado como Nó de Semente.

4. Desative cada nó no tipo de nó.

    Ligue ao cluster utilizando o PowerShell e, em seguida, corra o passo seguinte.
    
    ```powershell
    $nodeType = "" # specify the name of node type
    $nodes = Get-ServiceFabricNode
    
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
      }
    }
    ```

    - Para a durabilidade do bronze, espere que todos os nós entrem em estado de desativação
    - Para a durabilidade da prata e do ouro, alguns nós vão para deficientes e o resto estará em estado incapacitante. Verifique os detalhes do separador dos nós em estado incapacitante, se todos estiverem presos em garantir o quórum para as divisórias de serviço de infraestrutura, então é seguro continuar.

5. Pare os dados para o tipo de nó.

    Ligue ao cluster utilizando o PowerShell e, em seguida, corra o passo seguinte.
    
    ```powershell
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
      }
    }
    ```
    
    Aguarde até que todos os nós para o tipo de nó estejam marcados para baixo.

6. Nódes deallocate no conjunto de escala de máquina virtual original
    
    Faça login na subscrição Azure onde o conjunto de escala foi implantado e remova o Conjunto de Escala de Máquina Virtual. 

    ```powershell
    $scaleSetName="myscaleset"
    $scaleSetResourceType="Microsoft.Compute/virtualMachineScaleSets"
    
    Remove-AzResource -ResourceName $scaleSetName -ResourceType $scaleSetResourceType -ResourceGroupName $resourceGroupName -Force
    ```

    
7. Remova os dados para o tipo de nó.

    Ligue ao cluster utilizando o PowerShell e, em seguida, corra o passo seguinte.
    
    ```powershell
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
      }
    }
    ```

    Aguarde até que todos os nós sejam removidos do aglomerado. Os nós não devem ser apresentados em SFX.

8. Retire o tipo de nó da secção de tecido de serviço.

    - Localize o modelo do Gestor de Recursos Azure utilizado para a implementação.
    - Encontre a secção relacionada com o tipo de nó na secção Tecido de Serviço.
    - Retire a secção correspondente ao tipo de nó.
    - Apenas para aglomerados prateados e de durabilidade mais elevados, atualize o recurso de cluster no modelo e configuure as políticas de saúde para ignorar a saúde da aplicação do tecido:/Saúde da aplicação do sistema adicionando `applicationDeltaHealthPolicies` sob o recurso de cluster como indicado `properties` abaixo. A política abaixo deve ignorar os erros existentes, mas não permitir novos erros de saúde. 
 
 
     ```json
    "upgradeDescription":  
    { 
      "forceRestart": false, 
      "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
      "healthCheckWaitDuration": "00:05:00", 
      "healthCheckStableDuration": "00:05:00", 
      "healthCheckRetryTimeout": "00:45:00", 
      "upgradeTimeout": "12:00:00", 
      "upgradeDomainTimeout": "02:00:00", 
      "healthPolicy": { 
        "maxPercentUnhealthyNodes": 100, 
        "maxPercentUnhealthyApplications": 100 
      }, 
      "deltaHealthPolicy":  
      { 
        "maxPercentDeltaUnhealthyNodes": 0, 
        "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
        "maxPercentDeltaUnhealthyApplications": 0, 
        "applicationDeltaHealthPolicies":  
        { 
            "fabric:/System":  
            { 
                "defaultServiceTypeDeltaHealthPolicy":  
                { 
                        "maxPercentDeltaUnhealthyServices": 0 
                } 
            } 
        } 
      } 
    },
    ```

    - Implemente o modelo de Gestor de Recursos Azure modificado. ** Este passo vai demorar um pouco, normalmente até duas horas. Esta atualização irá alterar as definições para o InfrastructureService, pelo que é necessário um reinício do nó. Neste caso `forceRestart` é ignorado. 
    O parâmetro `upgradeReplicaSetCheckTimeout` especifica o tempo máximo que o Service Fabric espera que uma partição esteja num estado seguro, se não já em estado seguro. Uma vez que as verificações de segurança passam para todas as divisórias num nó, o Tecido de Serviço procede com a atualização desse nó.
    O valor do parâmetro `upgradeTimeout` pode ser reduzido para 6 horas, mas para segurança máxima devem ser utilizados 12 horas.

    Em seguida, valide que:
    - O Recurso de Tecido de Serviço no portal mostra-se pronto.

9. Remova todas as referências aos recursos relativos ao tipo de nó do modelo ARM.

    - Localize o modelo do Gestor de Recursos Azure utilizado para a implementação.
    - Remova o conjunto de balanças de máquina virtual e outros recursos relacionados com o tipo de nó do modelo.
    - Implemente as alterações.

    Em seguida:
    - Aguarde que a implantação esteja concluída.
    
10. Remova os recursos relativos ao tipo de nó que já não está a ser utilizado. Exemplo Balanceador de Carga e IP Público. 

    - Para remover estes recursos, pode utilizar o mesmo comando PowerShell utilizado no passo 6 especificando o tipo de recurso específico e a versão API. 

> [!Note]
> Este passo é opcional se o mesmo Balanceador de Carga e IP for reutilizado entre os tipos de nós.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [as características de durabilidade do](./service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)cluster.
- Saiba mais sobre [os tipos de nós e os conjuntos de balanças de máquinas virtuais.](service-fabric-cluster-nodetypes.md)
- Saiba mais sobre [o dimensionamento do cluster de tecido de serviço.](service-fabric-cluster-scaling.md)
