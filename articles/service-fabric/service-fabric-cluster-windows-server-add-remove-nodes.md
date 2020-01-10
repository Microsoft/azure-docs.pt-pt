---
title: Adicionar ou remover nós de um Cluster Service Fabric autônomo
description: Saiba como adicionar ou remover nós de um Cluster Service Fabric do Azure em uma máquina virtual ou física que executa o Windows Server, que pode ser local ou em qualquer nuvem.
author: dkkapur
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: dekapur
ms.openlocfilehash: aa9550d1ec6201f7cbaf552fac5f71c875428e21
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75458251"
---
# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Adicionar ou remover nós de um Cluster Service Fabric autônomo em execução no Windows Server
Depois de [criar seu cluster Service Fabric autônomo em computadores Windows Server](service-fabric-cluster-creation-for-windows-server.md), suas necessidades (comerciais) poderão ser alteradas e você precisará adicionar ou remover nós do cluster. Este artigo fornece etapas detalhadas para conseguir isso. Observe que não há suporte para a funcionalidade adicionar/remover nó em clusters de desenvolvimento locais.

## <a name="add-nodes-to-your-cluster"></a>Adicionar nós ao cluster

1. Prepare a VM/computador que você deseja adicionar ao cluster seguindo as etapas descritas em [planejar e preparar a implantação de cluster Service Fabric](service-fabric-cluster-creation-for-windows-server.md)
2. Identificar a qual domínio de falha e domínio de atualização você vai adicionar essa VM/computador
3. Área de trabalho remota (RDP) na VM/computador que você deseja adicionar ao cluster
4. Copiar ou [baixar o pacote autônomo para Service Fabric do Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690) para a VM/máquina e descompactar o pacote
5. Execute o PowerShell com privilégios elevados e navegue até o local do pacote descompactado
6. Execute o script *AddNode. ps1* com os parâmetros que descrevem o novo nó a ser adicionado. O exemplo a seguir adiciona um novo nó chamado VM5, com o tipo NodeType0 e o endereço IP 182.17.34.52, em UD1 e FD:/DC1/r0. O *ExistingClusterConnectionEndPoint* é um ponto de extremidade de conexão para um nó que já está no cluster existente, que pode ser o endereço IP de *qualquer* nó no cluster.

    ```
    .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -AcceptEULA
    ```
    Quando o script terminar a execução, você poderá verificar se o novo nó foi adicionado executando o cmdlet [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) .

7. Para garantir a consistência em diferentes nós no cluster, você deve iniciar uma atualização de configuração. Execute o [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) para obter o arquivo de configuração mais recente e adicione o nó recém-adicionado à seção "nós". Também é recomendável ter sempre a configuração de cluster mais recente disponível no caso de você precisar reimplantar um cluster com a mesma configuração.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
8. Execute [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) para iniciar a atualização.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    Você pode monitorar o progresso da atualização em Service Fabric Explorer. Como alternativa, você pode executar [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

### <a name="add-nodes-to-clusters-configured-with-windows-security-using-gmsa"></a>Adicionar nós a clusters configurados com segurança do Windows usando gMSA
Para clusters configurados com a conta de serviço gerenciado de grupo (gMSA) (https://technet.microsoft.com/library/hh831782.aspx), um novo nó pode ser adicionado usando uma atualização de configuração:
1. Execute [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) em qualquer um dos nós existentes para obter o arquivo de configuração mais recente e adicione detalhes sobre o novo nó que você deseja adicionar na seção "nós". Verifique se o novo nó faz parte da mesma conta gerenciada de grupo. Essa conta deve ser um administrador em todos os computadores.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
2. Execute [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) para iniciar a atualização.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
    ```
    Você pode monitorar o progresso da atualização em Service Fabric Explorer. Como alternativa, você pode executar [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

### <a name="add-node-types-to-your-cluster"></a>Adicionar tipos de nó ao cluster
Para adicionar um novo tipo de nó, modifique sua configuração para incluir o novo tipo de nó na seção "NodeTypes" em "Propriedades" e inicie uma atualização de configuração usando [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps). Quando a atualização for concluída, você poderá adicionar novos nós ao cluster com esse tipo de nó.

## <a name="remove-nodes-from-your-cluster"></a>Remover nós do cluster
Um nó pode ser removido de um cluster usando uma atualização de configuração, da seguinte maneira:

1. Execute [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) para obter o arquivo de configuração mais recente e *remova* o nó da seção "nós".
Adicione o parâmetro "NodesToBeRemoved" à seção "instalação" dentro da seção "FabricSettings". O "valor" deve ser uma lista separada por vírgulas de nomes de nó de nós que precisam ser removidos.

    ```
         "fabricSettings": [
            {
            "name": "Setup",
            "parameters": [
                {
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
                },
                {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
                },
                {
                "name": "NodesToBeRemoved",
                "value": "vm0, vm1"
                }
            ]
            }
        ]
    ```
2. Execute [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) para iniciar a atualização.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    Você pode monitorar o progresso da atualização em Service Fabric Explorer. Como alternativa, você pode executar [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

> [!NOTE]
> A remoção de nós pode iniciar várias atualizações. Alguns nós são marcados com `IsSeedNode=”true”` marca e podem ser identificados consultando o manifesto do cluster usando `Get-ServiceFabricClusterManifest`. A remoção desses nós pode levar mais tempo do que outros, já que os nós de semente precisarão ser movidos nesses cenários. O cluster deve manter um mínimo de três nós de tipo de nó primário.
> 
> 

### <a name="remove-node-types-from-your-cluster"></a>Remover tipos de nó do seu cluster
Antes de remover um tipo de nó, verifique novamente se há nós que fazem referência ao tipo de nó. Remova esses nós antes de remover o tipo de nó correspondente. Depois que todos os nós correspondentes forem removidos, você poderá remover o NodeType da configuração do cluster e iniciar uma atualização de configuração usando [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps).


### <a name="replace-primary-nodes-of-your-cluster"></a>Substituir nós primários do cluster
A substituição de nós primários deve ser executada em um nó após o outro, em vez de remover e depois adicionar em lotes.


## <a name="next-steps"></a>Passos seguintes
* [Definições de configuração para o cluster autônomo do Windows](service-fabric-cluster-manifest.md)
* [Proteger um cluster autônomo no Windows usando certificados X509](service-fabric-windows-cluster-x509-security.md)
* [Criar um Cluster Service Fabric autônomo com VMs do Azure executando o Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)

