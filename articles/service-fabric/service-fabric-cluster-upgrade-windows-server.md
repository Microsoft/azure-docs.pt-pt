---
title: Atualizar a versão de um cluster autónomo de Azure Service Fabric | Documentos da Microsoft
description: Atualize o código de Azure Service Fabric que executa um cluster autónomo do Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 29d034be5999d0bc3f0a244cfa7a5658a4ecce32
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661467"
---
# <a name="upgrade-the-service-fabric-version-that-runs-on-your-cluster"></a>Atualizar a versão de Service Fabric que é executado no seu cluster 

Para qualquer sistema moderno, a capacidade de atualizar é fundamental para o sucesso de longo prazo de seu produto. Um cluster do Azure Service Fabric é um recurso que é proprietário. Este artigo descreve como atualizar a versão do Service Fabric em execução no seu cluster autónomo.

> [!NOTE]
> Certifique-se de que o cluster é sempre executada uma versão suportada do Service Fabric. Quando a Microsoft anuncia o lançamento de uma nova versão do Service Fabric, a versão anterior está marcada para o fim do suporte após um mínimo de 60 dias a contar da data do anúncio. Novas versões sejam anunciados [no blog da equipe de recursos de infraestrutura do serviço](https://blogs.msdn.microsoft.com/azureservicefabric/). A nova versão está disponível para escolher nesse ponto.
>
>

Pode atualizar o seu cluster para a nova versão apenas se estiver a utilizar uma configuração de nó de estilo de produção, em que cada nó do Service Fabric é alocado numa máquina virtual ou físico separado. Se tiver um cluster de desenvolvimento, onde mais de um nó do Service Fabric está numa única máquina física ou virtual, tem de voltar a criar o cluster com a nova versão.

Dois fluxos de trabalho distintos podem atualizar o seu cluster para a versão mais recente ou uma versão suportada do Service Fabric. Um fluxo de trabalho destina-se a clusters que tem conectividade para transferir a versão mais recente automaticamente. O outro fluxo de trabalho destina-se a clusters que não tem ligação para transferir a versão mais recente do Service Fabric.

## <a name="enable-auto-upgrade-of-the-service-fabric-version-of-your-cluster"></a>Ativar atualização automática da versão do cluster do Service Fabric
Para configurar o seu cluster para transferir as atualizações do Service Fabric, quando a Microsoft lança uma nova versão, definir o `fabricClusterAutoupgradeEnabled` configuração do cluster para *true*. Para selecionar manualmente uma versão suportada do Service Fabric que pretende que o seu cluster estar no, defina o `fabricClusterAutoupgradeEnabled` configuração do cluster para *false*.

## <a name="upgrade-clusters-that-have-connectivity-to-download-the-latest-code-and-configuration"></a>Atualizar clusters que tem conectividade para transferir o código e a configuração mais recente
Utilize estes passos para atualizar o cluster para uma versão suportada, se os nós do cluster tem conectividade de internet para o [Microsoft Download Center](https://download.microsoft.com).

Para os clusters que tem conectividade para o [Microsoft Download Center](https://download.microsoft.com), Microsoft verifica periodicamente a disponibilidade de novas versões de Service Fabric.

Quando uma nova versão do Service Fabric está disponível, o pacote é transferido localmente para o cluster e provisionado para atualização. Além disso, para informar o cliente dessa nova versão, o sistema mostra um aviso de estado de funcionamento do cluster explícito que é semelhante ao seguinte:

"A versão do cluster atual [versão n. º] o suporte termina [date]."

Depois do cluster está a executar a versão mais recente, o aviso desaparece.

Quando vir o aviso de estado de funcionamento do cluster, atualize o cluster:

1. Ligar ao cluster a partir de qualquer máquina que tenha acesso de administrador para todas as máquinas que estão listadas como nós do cluster. A máquina que este script é executado não tem a fazer parte do cluster.

    ```powershell
    ###### connect to the secure cluster using certs
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3"
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```

2. Obter a lista de versões de Service Fabric, que pode atualizar para o.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    Deverá obter um resultado semelhante ao seguinte:

    ![Obtenha versões de Service Fabric][getfabversions]
3. Iniciar uma atualização de cluster para uma versão disponível com o [Start-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade) comando do Windows PowerShell.

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
   Para monitorizar o progresso da atualização, pode utilizar o Service Fabric Explorer ou execute o seguinte comando do PowerShell:

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    Se não forem cumpridas as políticas de estado de funcionamento do cluster, a atualização é revertida. Para especificar políticas de estado de funcionamento personalizados para o comando de início ServiceFabricClusterUpgrade, consulte a documentação para [Start-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade).

    Depois de corrigir os problemas que resultaram na reversão, inicie novamente a atualização ao seguir os mesmos passos conforme descrito anteriormente.

## <a name="upgrade-clusters-that-have-no-connectivity-to-download-the-latest-code-and-configuration"></a>Atualizar clusters que tenham *sem conectividade* para transferir o código e a configuração mais recente
Utilize estes passos para atualizar o cluster para uma versão suportada, se os nós do cluster não tem ligação à internet para o [Microsoft Download Center](https://download.microsoft.com).

> [!NOTE]
> Se estiver executando um cluster que não está ligado à internet, tem de monitorizar o [blogue da equipa do Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/) para saber mais sobre novas versões. O sistema não mostra um aviso de estado de funcionamento do cluster para o alertar de novas versões.  
>
>

### <a name="auto-provisioning-vs-manual-provisioning"></a>Provisionamento versus manual de aprovisionamento automático
Para ativar a transferência automática e de registo para a versão mais recente do código, configure o serviço de atualização do Service Fabric. Para obter instruções, consulte *Tools\ServiceFabricUpdateService.zip\Readme_InstructionsAndHowTos.txt* no [pacote autónomo](service-fabric-cluster-standalone-package-contents.md).

Para o processo manual, siga estas instruções.

Modificar a configuração do cluster para definir a seguinte propriedade como *false* antes de iniciar uma atualização de configuração:

```json
"fabricClusterAutoupgradeEnabled": false,
```

Para obter detalhes de utilização, consulte a [Start-ServiceFabricClusterConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) comando do PowerShell. Certifique-se atualizar clusterConfigurationVersion no JSON antes de iniciar a atualização de configuração.

```powershell
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

### <a name="cluster-upgrade-workflow"></a>Fluxo de trabalho de atualização de cluster

1. Execute [Get-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterupgrade) a partir de um de nós no cluster e tenha em atenção a *TargetCodeVersion*.

2. Execute o seguinte a partir de uma máquina ligada à internet para listar todas as versões de atualização compatível com a versão atual e transferir o pacote correspondente a partir de ligações de transferência associados:

    ```powershell
    ###### Get list of all upgrade compatible packages  
    Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion <TargetCodeVersion as noted in Step 1> 
    ```

3. Ligar ao cluster a partir de qualquer máquina que tenha acesso de administrador para todas as máquinas que estão listadas como nós do cluster. A máquina que este script é executado não tem a fazer parte do cluster.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

    ###### Here is a filled-out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"
    ```
4. Copie o pacote transferido para o arquivo de imagem do cluster.

5. Registe o pacote copiado.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file>

    ###### Here is a filled-out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab
    ```
6. Inicie uma atualização de cluster para uma versão disponível.

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
    Pode monitorizar o progresso da atualização no Service Fabric Explorer, ou pode executar o seguinte comando do PowerShell:

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    Se não forem cumpridas as políticas de estado de funcionamento do cluster, a atualização é revertida. Para especificar políticas de estado de funcionamento personalizados para o comando de início ServiceFabricClusterUpgrade, consulte a documentação para [Start-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade).

    Depois de corrigir os problemas que resultaram na reversão, inicie novamente a atualização ao seguir os mesmos passos conforme descrito anteriormente.

## <a name="next-steps"></a>Passos Seguintes
* [Atualizar a configuração de um cluster autónomo](service-fabric-cluster-config-upgrade-windows-server.md)
* Personalizar algumas [definições de cluster do Service Fabric](service-fabric-cluster-fabric-settings.md).
* [Dimensionar o seu cluster e reduzir](service-fabric-cluster-scale-up-down.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
