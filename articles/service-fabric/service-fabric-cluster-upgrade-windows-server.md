---
title: Atualizar a versão de um cluster autônomo
description: Atualize o código de Service Fabric do Azure que executa um cluster de Service Fabric autônomo.
author: dkkapur
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: f8c146d1a82fd745e6641ac2aea91aa34539d6f0
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610034"
---
# <a name="upgrade-the-service-fabric-version-that-runs-on-your-cluster"></a>Atualizar a versão de Service Fabric que é executada no cluster 

Para qualquer sistema moderno, a capacidade de atualizar é a chave para o sucesso de longo prazo de seu produto. Um Cluster Service Fabric do Azure é um recurso que você possui. Este artigo descreve como atualizar a versão do Service Fabric em execução em seu cluster autônomo.

> [!NOTE]
> Certifique-se de que o cluster sempre execute uma versão de Service Fabric com suporte. Quando a Microsoft anuncia o lançamento de uma nova versão do Service Fabric, a versão anterior é marcada para o fim do suporte após um mínimo de 60 dias a partir da data do anúncio. Novas versões são anunciadas [no blog da equipe do Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/). A nova versão está disponível para escolha nesse ponto.
>
>

Você pode atualizar o cluster para a nova versão somente se estiver usando uma configuração de nó de estilo de produção, em que cada nó de Service Fabric é alocado em uma máquina virtual ou física separada. Se você tiver um cluster de desenvolvimento, no qual mais de um nó de Service Fabric está em uma única máquina virtual ou física, será necessário recriar o cluster com a nova versão.

Dois fluxos de trabalho distintos podem atualizar o cluster para a versão mais recente ou para uma versão de Service Fabric com suporte. Um fluxo de trabalho é para clusters que têm conectividade para baixar a versão mais recente automaticamente. O outro fluxo de trabalho é para clusters que não têm conectividade para baixar a versão mais recente do Service Fabric.

## <a name="enable-auto-upgrade-of-the-service-fabric-version-of-your-cluster"></a>Habilitar a atualização automática da versão Service Fabric do cluster
Para definir o cluster para baixar atualizações de Service Fabric quando a Microsoft lançar uma nova versão, defina a configuração de cluster `fabricClusterAutoupgradeEnabled` como *true*. Para selecionar manualmente uma versão com suporte do Service Fabric no qual você deseja que o cluster esteja, defina a configuração de cluster `fabricClusterAutoupgradeEnabled` como *false*.

## <a name="upgrade-clusters-that-have-connectivity-to-download-the-latest-code-and-configuration"></a>Atualizar os clusters que têm conectividade para baixar o código e a configuração mais recentes
Use estas etapas para atualizar o cluster para uma versão com suporte se os nós de cluster tiverem conectividade com a Internet para o [centro de download da Microsoft](https://download.microsoft.com).

Para clusters que têm conectividade com o [centro de download da Microsoft](https://download.microsoft.com), a Microsoft verifica periodicamente a disponibilidade de novas versões de Service Fabric.

Quando uma nova versão de Service Fabric estiver disponível, o pacote será baixado localmente para o cluster e provisionado para atualização. Além disso, para informar o cliente sobre essa nova versão, o sistema mostra um aviso de integridade de cluster explícito semelhante ao seguinte:

"O suporte para a versão atual do cluster [Version #] termina em [data]."

Depois que o cluster estiver executando a versão mais recente, o aviso desaparecerá.

Quando você vir o aviso de integridade do cluster, atualize o cluster:

1. Conecte-se ao cluster de qualquer computador que tenha acesso de administrador a todos os computadores listados como nós no cluster. O computador em que esse script é executado não precisa fazer parte do cluster.

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

2. Obtenha a lista de versões de Service Fabric para as quais você pode atualizar.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    Você deve obter uma saída semelhante a esta:

    ![Obter Service Fabric versões][getfabversions]
3. Inicie uma atualização de cluster para uma versão disponível usando o comando [Start-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade) do Windows PowerShell.

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
   Para monitorar o progresso da atualização, você pode usar Service Fabric Explorer ou executar o seguinte comando do PowerShell:

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    Se as políticas de integridade do cluster não forem atendidas, a atualização será revertida. Para especificar políticas de integridade personalizadas para o comando Start-ServiceFabricClusterUpgrade, consulte a documentação de [Start-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade).

    Depois de corrigir os problemas que resultaram na reversão, inicie a atualização novamente seguindo as mesmas etapas descritas anteriormente.

## <a name="upgrade-clusters-that-have-no-connectivity-to-download-the-latest-code-and-configuration"></a>Atualizar os clusters que *não têm conectividade* para baixar o código e a configuração mais recentes
Use estas etapas para atualizar o cluster para uma versão com suporte se os nós de cluster não tiverem conectividade com a Internet para o [centro de download da Microsoft](https://download.microsoft.com).

> [!NOTE]
> Se você estiver executando um cluster que não está conectado à Internet, será necessário monitorar o [blog da equipe do Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/) para saber mais sobre as novas versões. O sistema não mostra um aviso de integridade do cluster para alertá-lo sobre novas versões.  
>
>

### <a name="auto-provisioning-vs-manual-provisioning"></a>Provisionamento automático vs. provisionamento manual
Para habilitar o download e o registro automáticos para a versão de código mais recente, configure o serviço de atualização de Service Fabric. Para obter instruções, consulte *tools\servicefabricupdateservice.zip\ Readme_InstructionsAndHowTos. txt* no [pacote autônomo](service-fabric-cluster-standalone-package-contents.md).

Para o processo manual, siga estas instruções.

Modifique a configuração do cluster para definir a seguinte propriedade como *false* antes de iniciar uma atualização de configuração:

```json
"fabricClusterAutoupgradeEnabled": false,
```

Para obter detalhes de uso, consulte o comando [Start-ServiceFabricClusterConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) do PowerShell. Certifique-se de atualizar ' clusterConfigurationVersion ' em seu JSON antes de iniciar a atualização da configuração.

```powershell
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

### <a name="cluster-upgrade-workflow"></a>Fluxo de trabalho de atualização de cluster

1. Execute [Get-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterupgrade) de um dos nós no cluster e observe o *TargetCodeVersion*.

2. Execute o seguinte de um computador conectado à Internet para listar todas as versões compatíveis com a atualização com a versão atual e baixar o pacote correspondente dos links de download associados:

    ```powershell
    ###### Get list of all upgrade compatible packages  
    Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion <TargetCodeVersion as noted in Step 1> 
    ```

3. Conecte-se ao cluster de qualquer computador que tenha acesso de administrador a todos os computadores listados como nós no cluster. O computador em que esse script é executado não precisa fazer parte do cluster.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

    ###### Here is a filled-out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"
    ```
4. Copie o pacote baixado no repositório de imagens do cluster.

5. Registre o pacote copiado.

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
    Você pode monitorar o progresso da atualização em Service Fabric Explorer ou pode executar o seguinte comando do PowerShell:

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    Se as políticas de integridade do cluster não forem atendidas, a atualização será revertida. Para especificar políticas de integridade personalizadas para o comando Start-ServiceFabricClusterUpgrade, consulte a documentação de [Start-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade).

    Depois de corrigir os problemas que resultaram na reversão, inicie a atualização novamente seguindo as mesmas etapas descritas anteriormente.

## <a name="next-steps"></a>Passos seguintes
* [Atualizar a configuração de um cluster autônomo](service-fabric-cluster-config-upgrade-windows-server.md)
* Personalizar algumas [configurações de cluster Service Fabric](service-fabric-cluster-fabric-settings.md).
* [Dimensione o cluster para dentro e para fora](service-fabric-cluster-scale-up-down.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
