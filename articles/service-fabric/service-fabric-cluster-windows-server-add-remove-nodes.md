---
title: Adicionar ou remover nós para um cluster autónomo do Service Fabric | Documentos da Microsoft
description: Saiba como adicionar ou remover nós num cluster do Azure Service Fabric num computador físico ou máquina virtual com o Windows Server, que pode ser local ou em qualquer cloud.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: bc6b8fc0-d2af-42f8-a164-58538be38d02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: dekapur
ms.openlocfilehash: e9b87c19977fe35132d80729810c3a0547c486fe
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57446117"
---
# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Adicionar ou remover nós do cluster autónomo do Service Fabric em execução no Windows Server
Depois de ter [criado o seu cluster autónomo do Service Fabric em máquinas do Windows Server](service-fabric-cluster-creation-for-windows-server.md), suas necessidades (empresariais) pode ser alterada e terá de adicionar ou remover nós ao cluster. Este artigo fornece passos detalhados para atingir esse objetivo. Tenha em atenção que adicionar/remover nó funcionalidade não é suportada em clusters de desenvolvimento local.

## <a name="add-nodes-to-your-cluster"></a>Adicionar nós ao cluster

1. Preparar a VM/máquina que pretende adicionar ao seu cluster, seguindo os passos descritos em [planear e preparar a implementação de cluster do Service Fabric](service-fabric-cluster-creation-for-windows-server.md)
2. Identificar o domínio de falha e pretender adicionar esta VM/máquina para o domínio de atualização
3. Ambiente de trabalho remoto (RDP) para a VM/máquina em que pretende adicionar ao cluster
4. Cópia ou [transferir o pacote autónomo para o Service Fabric para Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690) para a VM/máquina e deszipe o pacote
5. Executar o Powershell com privilégios elevados e navegue para a localização do pacote descompactado
6. Executar o *AddNode.ps1* skript os parâmetros que descrevem o novo nó para adicionar. O exemplo abaixo adiciona um novo nó chamado VM5, com o tipo NodeType0 e endereços IP 182.17.34.52, em UD1 e fd: / dc1/r0. O *ExistingClusterConnectionEndPoint* é um ponto final de ligação para um nó do cluster existente, já que pode ser o endereço IP do *qualquer* nó do cluster.

    ```
    .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -AcceptEULA
    ```
    Assim que o script termina a execução, pode verificar se o novo nó foi adicionado ao executar o [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) cmdlet.

7. Para assegurar a consistência entre diferentes nós do cluster, tem de iniciar uma atualização de configuração. Execute [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) para obter o ficheiro de configuração mais recente e adicione o nó recém-adicionado à seção de "Nós". Também é recomendado ter sempre a configuração de cluster mais recente disponível no caso que terá de voltar a implementar um cluster com a mesma configuração.

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
    Pode monitorizar o progresso da atualização no Service Fabric Explorer. Em alternativa, pode executar [Get ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

### <a name="add-nodes-to-clusters-configured-with-windows-security-using-gmsa"></a>Adicionar nós para clusters configurados com a segurança do Windows utilizam a gMSA
Para clusters configurados com grupo Account(gMSA) de serviço gerida (https://technet.microsoft.com/library/hh831782.aspx), um novo nó pode ser adicionado usando uma atualização de configuração:
1. Execute [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) em qualquer um de nós existentes para obter o ficheiro de configuração mais recente e adicionar os detalhes sobre o novo nó que pretende adicionar na secção "Nós". Certifique-se de que o novo nó faz parte da mesma conta geridas de grupo. Esta conta deve ser um administrador em todos os computadores.

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
    Pode monitorizar o progresso da atualização no Service Fabric Explorer. Em alternativa, pode executar [Get ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

### <a name="add-node-types-to-your-cluster"></a>Adicionar tipos de nó ao cluster
Para adicionar um novo tipo de nó, modificar a configuração para incluir o novo tipo de nó "NodeTypes" nos secção em "Propriedades" e iniciar a uma configuração de atualizar com o [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps). Depois de concluída a atualização, pode adicionar novos nós ao cluster com este tipo de nó.

## <a name="remove-nodes-from-your-cluster"></a>Remover nós do cluster
Um nó pode ser removido de um cluster com uma atualização de configuração, da seguinte forma:

1. Execute [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) para obter o ficheiro de configuração mais recente e *remover* o nó da seção de "Nós".
Adicione o parâmetro de "NodesToBeRemoved" para "Setup" secção dentro da seção de "FabricSettings". O "valor" deve ser uma lista separada por vírgulas de nomes de nó de nós que tenham de ser removidas.

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
    Pode monitorizar o progresso da atualização no Service Fabric Explorer. Em alternativa, pode executar [Get ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

> [!NOTE]
> Remoção de nós pode iniciar várias atualizações. Alguns nós são marcados com `IsSeedNode=”true”` Etiquetar e podem ser identificados através da consulta do cluster através do manifesto `Get-ServiceFabricClusterManifest`. Remoção desses nós pode demorar mais tempo do que outras pessoas, visto que os nós de semente tem de ser movidos em torno em tais cenários. O cluster tem de manter um mínimo de 3 nós de tipo de nó primário.
> 
> 

### <a name="remove-node-types-from-your-cluster"></a>Remover tipos de nós de cluster
Antes de remover um tipo de nó, o problema verifique novamente se existirem quaisquer nós a referenciar o tipo de nó. Remova estes nós antes de remover o tipo de nó correspondente. Depois de todos os nós correspondentes são removidos, pode remover o NodeType da configuração do cluster e começar a uma configuração de atualizar com o [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps).


### <a name="replace-primary-nodes-of-your-cluster"></a>Substitua nós principais do cluster
A substituição de nós principal deve ser efetuado um nó após a outra, em vez de remover e, em seguida, adicionar em lotes.


## <a name="next-steps"></a>Passos Seguintes
* [Definições de configuração para cluster autónomo do Windows](service-fabric-cluster-manifest.md)
* [Proteger um cluster autónomo no Windows com X509 certificados](service-fabric-windows-cluster-x509-security.md)
* [Criar um cluster autónomo do Service Fabric com VMs do Azure a executar o Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)

