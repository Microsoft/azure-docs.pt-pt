---
title: Remova um nó em Tecido de Serviço Azure [ Microsoft Docs
description: Aprenda a remover um tipo de nó de um cluster de tecido de serviço em funcionamento em Azure.
author: inputoutputcode
manager: sridmad
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: chrpap
ms.openlocfilehash: 330b455a61c45ccdb59e5aef8162fd1b04859a00
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78969411"
---
# <a name="how-to-remove-a-service-fabric-node-type"></a>Como remover um tipo de nó de tecido de serviço
Este artigo descreve como escalar um cluster de tecido de serviço Azure removendo um tipo de nó existente de um cluster. Um cluster de tecido de serviço é um conjunto de máquinas virtuais ou físicas ligadas à rede, nas quais os seus microserviços são implantados e geridos. Uma máquina ou VM que faz parte de um aglomerado é chamado de nó. Os conjuntos de escala de máquinas virtuais são um recurso de computação Azure que utiliza para implantar e gerir uma coleção de máquinas virtuais como conjunto. Cada tipo de nó definido num cluster Azure é [configurado como um conjunto](service-fabric-cluster-nodetypes.md)de escala separado . Cada tipo de nó pode então ser gerido separadamente. Depois de criar um cluster de Tecido de Serviço, pode escalar um cluster horizontalmente removendo um tipo de nó (conjunto de escala de máquina virtual) e todos os seus nós.  Pode escalar o cluster a qualquer momento, mesmo quando as cargas de trabalho estão a decorrer no cluster.  À medida que o cluster escala, as suas aplicações também escalam automaticamente.

> [!WARNING]
> Não é recomendável utilizar esta abordagem para remover um tipo de nó de um cluster de produção frequentemente utilizado. É um comando perigoso pois elimina o recurso de conjunto de escala de máquina virtual por trás do tipo de nó. 

## <a name="durability-characteristics"></a>Características de durabilidade
A segurança é prioritária em relação à velocidade quando se utiliza Remove-AzServiceFabricNodeType. O tipo de nó deve ser de prata ou [ouro,](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)porque:
- O bronze não lhe dá garantias sobre a poupança de informações do Estado.
- Durabilidade prateada e dourada prendem qualquer alteração ao conjunto de escala.
- O ouro também lhe dá controlo sobre as atualizações do Azure por baixo do conjunto de escala.

Service Fabric "orquestra" alterações e atualizações subjacentes para que os dados não se percam. No entanto, ao remover um nó tipo com durabilidade de Bronze, pode perder informações do Estado. Se estiver a remover um nó primário e a sua aplicação for apátrida, o Bronze é aceitável. Quando executa cargas de trabalho audais na produção, a configuração mínima deve ser silver. Da mesma forma, para cenários de produção o tipo principal de nó deve ser sempre Prata ou Ouro.

### <a name="more-about-bronze-durability"></a>Mais sobre a durabilidade do Bronze

Ao remover um nó tipo que é Bronze, todos os nós do nó descem imediatamente. O Serviço Fabric não prende nenhuma atualização de conjunto de nóesbronze, pelo que todos os VMs descem imediatamente. Se tivesse algo audabil izado nesses nós, os dados estão perdidos. Mesmo que fosse apátrida, todos os nós do Tecido de Serviço participam no ringue, para que um bairro inteiro possa estar perdido, o que pode desestabilizar o aglomerado em si.

## <a name="remove-a-node-type"></a>Remover um tipo de nó

1. Por favor, cuide destes pré-requisitos antes de iniciar o processo.

    - O aglomerado é saudável.
    - Ainda haverá capacidade suficiente após a remoção do tipo do nó, por exemplo. número de nós para colocar a contagem de réplicas necessária.

2. Mova todos os serviços que têm restrições de colocação para utilizar o tipo de nó fora do tipo de nó.

    - Modificar a Aplicação /Manifesto de Serviço para deixar de fazer referência ao tipo de nó.
    - Implemente a mudança.

    Em seguida, valide que:
    - Todos os serviços acima modificados já não estão a funcionar no nó pertencente ao nó.
    - Todos os serviços são saudáveis.

3. Desmarcar o tipo de nó como não primário (saltar para tipos de nó não primários)

    - Localize o modelo do Gestor de Recursos Azure utilizado para a implantação.
    - Encontre a secção relacionada com o tipo de nó na secção Tecido de Serviço.
    - Mudar é propriedade primária para falso. ** Não retire a secção relacionada com o tipo de nó nesta tarefa.
    - Implemente o modelo modificado do Gestor de Recursos Azure. ** Dependendo da configuração do cluster, este passo pode demorar algum tempo.
    
    Em seguida, valide que:
    - Secção de tecido de serviço no Portal indica que cluster está pronto.
    - O aglomerado é saudável.
    - Nenhum dos nós pertencentes ao nó está marcado como Nó de Sementes.

4. Desative os dados para o tipo de nó.

    Ligue-se ao cluster utilizando o PowerShell e, em seguida, executar o seguinte passo.
    
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

    - Para a durabilidade do bronze, espere que todos os nós vão para o estado de deficientes
    - Para a durabilidade de prata e ouro, alguns nós entrarão para deficientes e o resto estará em estado de desativação. Verifique o separador de detalhes dos nós em estado de desativação, se todos eles estão presos em garantir quórum para divisórias de serviço de infraestrutura, então é seguro continuar.

5. Pare os dados para o tipo de nó.

    Ligue-se ao cluster utilizando o PowerShell e, em seguida, executar o seguinte passo.
    
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
    
    Espere até que todos os nós para o tipo de nó estejam marcados para baixo.
    
6. Remova os dados para o tipo de nó.

    Ligue-se ao cluster utilizando o PowerShell e, em seguida, executar o seguinte passo.
    
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

    Espere até que todos os nós sejam removidos do aglomerado. Os nódosos não devem ser exibidos no SFX.

7. Retire o tipo do nó da secção de tecido de serviço.

    - Localize o modelo do Gestor de Recursos Azure utilizado para a implantação.
    - Encontre a secção relacionada com o tipo de nó na secção Tecido de Serviço.
    - Retire a secção correspondente ao tipo de nó.
    - Apenas para aglomerados de prata e maior durabilidade, atualize o recurso de cluster no `applicationDeltaHealthPolicies` modelo `properties` e configure as políticas de saúde para ignorar a saúde da aplicação do tecido:/Sistema, adicionando sob o recurso cluster como indicado abaixo. A política abaixo deve ignorar os erros existentes, mas não permitir novos erros de saúde. 
 
 
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

    - Implemente o modelo modificado do Gestor de Recursos Azure. ** Este passo vai demorar um pouco, geralmente até duas horas. Esta atualização irá alterar as definições para o Serviço de Infraestruturas, pelo que é necessário um reinício do nó. Neste caso `forceRestart` é ignorado. 
    O parâmetro `upgradeReplicaSetCheckTimeout` especifica o tempo máximo que o Tecido de Serviço espera que uma partição esteja em estado seguro, se não já em estado seguro. Uma vez que as verificações de segurança passam para todas as divisórias num nó, o Tecido de Serviço procede com a atualização do nó.
    O valor do `upgradeTimeout` parâmetro pode ser reduzido para 6 horas, mas para a segurança máxima devem ser utilizadas 12 horas.

    Em seguida, valide que:
    - Serviço De recurso de tecido no portal mostra pronto.

8. Remova todas as referências aos recursos relativos ao tipo de nó.

    - Localize o modelo do Gestor de Recursos Azure utilizado para a implantação.
    - Remova o conjunto de escala de máquina virtual e outros recursos relacionados com o tipo de nó do modelo.
    - Implementar as alterações.

    Em seguida:
    - Aguarde a colocação completa.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [as características](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)de durabilidade do cluster.
- Saiba mais sobre [tipos de nó e conjuntos](service-fabric-cluster-nodetypes.md)de escala de máquina virtual.
- Saiba mais sobre [a escala de cluster de tecido de serviço.](service-fabric-cluster-scaling.md)
