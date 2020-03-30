---
title: Adicione ou remova nós para um cluster de tecido de serviço autónomo
description: Aprenda a adicionar ou remover nós a um cluster de tecido de serviço Azure numa máquina física ou virtual que executa o Windows Server, que pode estar no local ou em qualquer nuvem.
author: dkkapur
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: dekapur
ms.openlocfilehash: f9bee35ee8e82070b4cf601139b471562ba5e10b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934208"
---
# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Adicionar ou remover nós de um cluster do Service Fabric autónomo em execução no Windows Server
Depois de ter criado o seu cluster de tecido de [serviço autónomo nas máquinas do Windows Server,](service-fabric-cluster-creation-for-windows-server.md)as suas necessidades (empresariais) podem mudar e terá de adicionar ou remover nós do seu cluster. Este artigo fornece passos detalhados para o conseguir. Por favor, note que a funcionalidade de adicionar/remover o nó não é suportada em clusters de desenvolvimento local.

## <a name="add-nodes-to-your-cluster"></a>Adicione nós ao seu cluster

1. Prepare o VM/máquina que pretende adicionar ao seu cluster seguindo os passos delineados no Plano e prepare a [implantação](service-fabric-cluster-standalone-deployment-preparation.md)do cluster Service Fabric .

2. Identifique a que domínio de avaria e o domínio de atualização irá adicionar este VM/máquina.

   Se utilizar certificados para proteger o cluster, espera-se que os certificados sejam instalados nas lojas de certificados locais em preparação para que o nó se junte ao cluster. O analógico é aplicável quando se utilizam outras formas de segurança.

3. Ambiente de trabalho remoto (RDP) no VM/máquina que pretende adicionar ao cluster.

4. Copie ou [descarregue o pacote autónomo para o Serviço De Tecido para O Servidor do Windows](https://go.microsoft.com/fwlink/?LinkId=730690) para o VM/máquina e desaperte o pacote.

5. Executar powerShell com privilégios elevados e ir para a localização do pacote desapertado.

6. Executar o script *AddNode.ps1* com os parâmetros que descrevem o novo nó a adicionar. O exemplo seguinte adiciona um novo nó chamado VM5, com o tipo NodeType0 e o endereço IP 182.17.34.52, em UD1 e fd:/dc1/r0. `ExistingClusterConnectionEndPoint`é um ponto final de ligação para um nó já existente no cluster existente, que pode ser o endereço IP de *qualquer* nó no cluster. 

   Inseguro (prototipagem):

   ```
   .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -AcceptEULA
   ```

   Seguro (baseado em certificado):

   ```  
   $CertThumbprint= "***********************"
    
   .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -X509Credential -ServerCertThumbprint $CertThumbprint  -AcceptEULA

   ```

   Quando o script terminar a funcionar, pode verificar se o novo nó foi adicionado executando o [cmdlet Get-ServiceFabricNode.](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps)

7. Para garantir a consistência em diferentes nós do cluster, deve iniciar uma atualização de configuração. Executar [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) para obter o mais recente ficheiro de configuração e adicionar o nó recém-adicionado à secção "Nós". Recomenda-se também ter sempre a configuração mais recente do cluster disponível no caso de necessitar de reimplantar um cluster com a mesma configuração.

   ```
    {
        "nodeName": "vm5",
        "iPAddress": "182.17.34.52",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD1"
    }
   ```

8. Executar [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) para iniciar a atualização.

   ```
   Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
   ```

   Pode monitorizar o progresso da atualização no Service Fabric Explorer. Em alternativa, pode executar [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps).

### <a name="add-nodes-to-clusters-configured-with-windows-security-using-gmsa"></a>Adicione nós a clusters configurados com a Segurança do Windows usando gMSA
Para clusters configurados com Conta de Serviço Geridahttps://technet.microsoft.com/library/hh831782.aspx)pelo Grupo (gMSA), um novo nó pode ser adicionado usando uma atualização de configuração:
1. Executar [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) em qualquer um dos nós existentes para obter o mais recente ficheiro de configuração e adicionar detalhes sobre o novo nó que pretende adicionar na secção "Nós". Certifique-se de que o novo nó faz parte da mesma conta gerida pelo grupo. Esta conta deve ser um Administrador em todas as máquinas.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
2. Executar [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) para iniciar a atualização.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
    ```
    Pode monitorizar o progresso da atualização no Service Fabric Explorer. Em alternativa, pode executar [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

### <a name="add-node-types-to-your-cluster"></a>Adicione tipos de nó ao seu cluster
Para adicionar um novo tipo de nó, modifique a sua configuração para incluir o novo tipo de nó na secção "NodeTypes" em "Propriedades" e inicie uma atualização de configuração utilizando [start-serviceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps). Uma vez concluída a atualização, pode adicionar novos nós ao seu cluster com este tipo de nó.

## <a name="remove-nodes-from-your-cluster"></a>Remova os nós do seu aglomerado
Um nó pode ser removido de um cluster utilizando uma atualização de configuração, da seguinte forma:

1. Executar [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) para obter o ficheiro de configuração mais recente e *remover* o nó da secção "Nós".
Adicione o parâmetro "NodesToBeRemoved" à secção "Configurar" dentro da secção "Definições de Tecido". O "valor" deve ser uma lista separada de vírdinos separados de nomes de nódosos que precisam de ser removidos.

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
2. Executar [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) para iniciar a atualização.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    Pode monitorizar o progresso da atualização no Service Fabric Explorer. Em alternativa, pode executar [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps).

> [!NOTE]
> A remoção dos nódosos pode iniciar várias atualizações. Alguns nós são `IsSeedNode=”true”` marcados com etiqueta e podem ser identificados consultando o manifesto do cluster usando `Get-ServiceFabricClusterManifest`. A remoção desses nódosos pode demorar mais tempo do que outros, uma vez que os nódosos de sementes terão de ser movidos nesses cenários. O aglomerado deve manter um mínimo de 3 nós primários do tipo.
> 
> 

### <a name="remove-node-types-from-your-cluster"></a>Remova os tipos de nó do seu cluster
Antes de remover um tipo de nó, verifique duas vezes se existem nós referentes ao tipo de nó. Retire estes nódosos antes de remover o tipo de nó correspondente. Uma vez removidos todos os nós correspondentes, pode remover o NodeType da configuração do cluster e iniciar uma atualização de configuração utilizando o [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps).


### <a name="replace-primary-nodes-of-your-cluster"></a>Substitua os nós primários do seu cluster
A substituição dos nós primários deve ser executada um nó após o outro, em vez de remover e, em seguida, adicionar em lotes.


## <a name="next-steps"></a>Passos seguintes
* [Definições de configuração para cluster Windows autónomo](service-fabric-cluster-manifest.md)
* [Proteja um cluster autónomo no Windows utilizando certificados X509](service-fabric-windows-cluster-x509-security.md)
* [Crie um cluster de tecido de serviço autónomo com VMs Azure executando Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)

