---
title: Adicione ou remova os nódinhos a um cluster de tecido de serviço autónomo
description: Aprenda a adicionar ou remover nós a um cluster de Tecido de Serviço Azure numa máquina física ou virtual que executa o Windows Server, que pode estar no local ou em qualquer nuvem.
ms.topic: conceptual
ms.date: 11/02/2017
ms.openlocfilehash: 26945b4785a0591d997139f2427b0ae6b59fa742
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98790601"
---
# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Adicionar ou remover nós de um cluster do Service Fabric autónomo em execução no Windows Server
Depois de ter criado o seu cluster de Tecido de [Serviço autónomo em máquinas do Windows Server,](service-fabric-cluster-creation-for-windows-server.md)as suas necessidades (empresariais) podem mudar e terá de adicionar ou remover nós ao seu cluster, conforme descrito neste artigo.

> [!NOTE]
> A funcionalidade de adição e remoção de nó não é suportada em clusters de desenvolvimento local.

## <a name="add-nodes-to-your-cluster"></a>Adicione os nódes ao seu cluster

1. Prepare o VM/máquina que pretende adicionar ao seu cluster seguindo os passos descritos no [Plano e prepare a sua implantação de cluster de tecido de serviço](service-fabric-cluster-standalone-deployment-preparation.md).

2. Identifique o domínio de avaria e o domínio de atualização a que irá adicionar este VM/máquina.

   Se utilizar certificados para assegurar o cluster, espera-se que os certificados sejam instalados nas lojas de certificados locais em preparação para que o nó se junte ao cluster. O analógico é aplicável quando se utiliza outras formas de segurança.

3. Ambiente de trabalho remoto (PDR) no VM/máquina que pretende adicionar ao cluster.

4. Copie ou [descarregue o pacote autónomo para o Service Fabric for Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690) para o VM/máquina e desaperte o pacote.

5. Executar PowerShell com privilégios elevados e vá para o local do pacote desapertado.

6. Executar o *AddNode.ps1* script com os parâmetros que descrevem o novo nó a adicionar. O exemplo a seguir adiciona um novo nó chamado VM5, com o tipo NodeType0 e o endereço IP 182.17.34.52, em UD1 e fd:/dc1/r0. `ExistingClusterConnectionEndPoint` é um ponto final de ligação para um nó já no cluster existente, que pode ser o endereço IP de *qualquer* nó no cluster. 

   Inseguro (prototipagem):

   ```
   .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -AcceptEULA
   ```

   Seguro (baseado em certificados):

   ```  
   $CertThumbprint= "***********************"
    
   .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -X509Credential -ServerCertThumbprint $CertThumbprint  -AcceptEULA

   ```

   Quando o script terminar de funcionar, pode verificar se o novo nó foi adicionado executando o [cmdlet Get-ServiceFabricNode.](/powershell/module/servicefabric/get-servicefabricnode)

7. Para garantir a consistência entre diferentes nós no cluster, tem de iniciar uma atualização de configuração. Executar [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration) para obter o mais recente ficheiro de configuração e adicionar o nó recém-adicionado à secção "Nós". Recomenda-se também que tenha sempre a mais recente configuração de cluster disponível no caso de precisar de recolocar um cluster com a mesma configuração.

   ```
    {
        "nodeName": "vm5",
        "iPAddress": "182.17.34.52",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD1"
    }
   ```

8. Executar [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) para iniciar a atualização.

   ```
   Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
   ```

   Pode monitorizar o progresso da atualização no Service Fabric Explorer. Em alternativa, pode executar [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade).

### <a name="add-nodes-to-clusters-configured-with-windows-security-using-gmsa"></a>Adicione os gDes aos clusters configurados com a Segurança do Windows utilizando gMSA
Para os clusters configurados com a Conta de Serviço Gerido do Grupo (gMSA), https://technet.microsoft.com/library/hh831782.aspx) um novo nó pode ser adicionado usando uma atualização de configuração:
1. Executar [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration) em qualquer um dos nós existentes para obter o ficheiro de configuração mais recente e adicionar detalhes sobre o novo nó que pretende adicionar na secção "Nós". Certifique-se de que o novo nó faz parte da conta gerida pelo mesmo grupo. Esta conta deve ser um Administrador em todas as máquinas.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
2. Executar [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) para iniciar a atualização.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
    ```
    Pode monitorizar o progresso da atualização no Service Fabric Explorer. Em alternativa, pode executar [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade)

### <a name="add-node-types-to-your-cluster"></a>Adicione tipos de nó ao seu cluster
Para adicionar um novo tipo de nó, modifique a sua configuração para incluir o novo tipo de nó na secção "NodeTypes" em "Propriedades" e inicie uma atualização de configuração utilizando [start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade). Uma vez concluída a atualização, pode adicionar novos nós ao seu cluster com este tipo de nó.

## <a name="remove-nodes-from-your-cluster"></a>Remova os nódes do seu cluster
Um nó pode ser removido de um cluster utilizando uma atualização de configuração, da seguinte forma:

1. Executar [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration) para obter o ficheiro de configuração mais recente e *remover* o nó da secção "Nós".
Adicione o parâmetro "NodesToBeRemoved" à secção "Configuração" dentro da secção "FabricSettings". O "valor" deve ser uma lista separada por vírgula de nomes de nós que precisam de ser removidos.

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
2. Executar [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) para iniciar a atualização.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    Pode monitorizar o progresso da atualização no Service Fabric Explorer. Em alternativa, pode executar [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade).

> [!NOTE]
> A remoção dos nós pode iniciar várias atualizações. Alguns nós são marcados com `IsSeedNode=”true”` etiqueta e podem ser identificados consultando o manifesto do cluster usando `Get-ServiceFabricClusterManifest` . A remoção de tais nós pode demorar mais tempo do que outros, uma vez que os nós de sementes terão de ser movidos nesses cenários. O cluster deve manter um mínimo de 3 nós do tipo de nó primário.
> 
> 

### <a name="remove-node-types-from-your-cluster"></a>Remova os tipos de nó do seu cluster
Antes de remover um tipo de nó, verifique se existem nóles que referenciam o tipo de nó. Retire estes nós antes de remover o tipo de nó correspondente. Uma vez removidos todos os nós correspondentes, pode remover o NodeType da configuração do cluster e iniciar uma atualização de configuração utilizando [start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade).


### <a name="replace-primary-nodes-of-your-cluster"></a>Substitua os nóns primários do seu cluster
A substituição dos nós primários deve ser executada um nó após o outro, em vez de remover e, em seguida, adicionar em lotes.


## <a name="next-steps"></a>Passos seguintes
* [Definições de configuração para cluster de Windows autónomo](service-fabric-cluster-manifest.md)
* [Garantir um cluster autónomo no Windows utilizando certificados X509](service-fabric-windows-cluster-x509-security.md)
* [Crie um cluster de tecido de serviço autónomo com VMs Azure executando Windows](./service-fabric-cluster-creation-via-arm.md)
