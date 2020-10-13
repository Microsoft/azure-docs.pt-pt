---
title: Atualize a versão de um cluster autónomo
description: Atualize o código Azure Service Fabric que executa um cluster de tecido de serviço autónomo.
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: 5831fcffe718e4966c3444a29d35c5bbd55e5f57
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91842942"
---
# <a name="upgrade-the-service-fabric-version-that-runs-on-your-cluster"></a>Atualize a versão do Tecido de Serviço que funciona no seu cluster 

Para qualquer sistema moderno, a capacidade de upgrade é fundamental para o sucesso a longo prazo do seu produto. Um cluster Azure Service Fabric é um recurso que possui. Este artigo descreve como atualizar a versão do Service Fabric em execução no seu cluster autónomo.

> [!NOTE]
> Certifique-se de que o seu cluster executa sempre uma versão de Tecido de Serviço suportado. Quando a Microsoft anuncia o lançamento de uma nova versão do Service Fabric, a versão anterior fica marcada para o fim do suporte após um mínimo de 60 dias a contar da data do anúncio. Novos lançamentos são anunciados [no blog da equipa de Service Fabric.](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) O novo lançamento está disponível para escolher nessa altura.
>
>

Só pode atualizar o seu cluster para a nova versão se estiver a utilizar uma configuração de nó de tipo de produção, onde cada nó de Tecido de Serviço é atribuído numa máquina física ou virtual separada. Se tiver um cluster de desenvolvimento, onde mais do que um nó de Tecido de Serviço está numa única máquina física ou virtual, deve recriar o cluster com a nova versão.

Dois fluxos de trabalho distintos podem atualizar o seu cluster para a versão mais recente ou uma versão suportada do Service Fabric. Um fluxo de trabalho é para clusters que têm conectividade para baixar automaticamente a versão mais recente. O outro fluxo de trabalho é para clusters que não têm conectividade para descarregar a versão mais recente do Service Fabric.

## <a name="enable-auto-upgrade-of-the-service-fabric-version-of-your-cluster"></a>Ativar a atualização automática da versão do Tecido de Serviço do seu cluster
Para definir o seu cluster para descarregar atualizações do Service Fabric quando a Microsoft lançar uma nova versão, desafie a configuração do `fabricClusterAutoupgradeEnabled` cluster como *verdadeira*. Para selecionar manualmente uma versão suportada do Tecido de Serviço em que pretende que o seu cluster esteja ligado, desa ajuste a configuração do `fabricClusterAutoupgradeEnabled` cluster em *falso*.

## <a name="upgrade-clusters-that-have-connectivity-to-download-the-latest-code-and-configuration"></a>Atualizar clusters que têm conectividade para descarregar o código e configuração mais recentes
Utilize estes passos para atualizar o seu cluster para uma versão suportada se os seus nós de cluster tiverem conectividade com a Internet para o [Microsoft Download Center.](https://download.microsoft.com)

Para clusters que tenham conectividade com o [Microsoft Download Center,](https://download.microsoft.com)a Microsoft verifica periodicamente a disponibilidade de novas versões de Service Fabric.

Quando uma nova versão do Service Fabric está disponível, o pacote é descarregado localmente para o cluster e previsto para upgrade. Além disso, para informar o cliente desta nova versão, o sistema mostra um aviso explícito de saúde do cluster que é semelhante ao seguinte:

"A versão atual do cluster [versão #] suporta [data]."

Depois do cluster estar a executar a versão mais recente, o aviso desaparece.

Quando vir o aviso de saúde do cluster, atualize o cluster:

1. Ligue-se ao cluster de qualquer máquina que tenha acesso ao administrador a todas as máquinas listadas como nós no cluster. A máquina em que este guião é executado não tem de fazer parte do cluster.

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

2. Obtenha a lista de versões de Service Fabric para as que pode fazer upgrade.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    Deve obter uma saída semelhante a esta:

    ![Obtenha versões de Tecido de Serviço][getfabversions]
3. Inicie uma atualização de cluster para uma versão disponível utilizando o comando [Start-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/start-servicefabricclusterupgrade) Windows PowerShell.

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
   Para monitorizar o progresso da atualização, pode utilizar o Explorador de Tecidos de Serviço ou executar o seguinte comando PowerShell:

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    Se as políticas de saúde do cluster não forem cumpridas, a atualização é revertida. Para especificar as políticas de saúde personalizadas para o comando Start-ServiceFabricClusterUpgrade, consulte a documentação para [Start-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/start-servicefabricclusterupgrade).

    Depois de corrigir os problemas que resultaram na reversão, inicie novamente a atualização seguindo os mesmos passos descritos anteriormente.

## <a name="upgrade-clusters-that-have-no-connectivity-to-download-the-latest-code-and-configuration"></a>Upgrade clusters que não têm *conectividade* para descarregar o código e configuração mais recentes
Utilize estes passos para atualizar o seu cluster para uma versão suportada se os seus nós de cluster não tiverem conectividade com a Internet para o [Microsoft Download Center.](https://download.microsoft.com)

> [!NOTE]
> Se estiver a executar um cluster que não está ligado à internet, tem de monitorizar o blog da equipa do [Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) para aprender sobre novos lançamentos. O sistema não mostra um aviso de saúde do cluster para alertá-lo de novos lançamentos.  
>
>

### <a name="auto-provisioning-vs-manual-provisioning"></a>Provisão automática vs. provisionamento manual
Para permitir o descarregamento automático e o registo para a versão código mais recente, crie o Serviço de Atualização do Tecido de Serviço. Para obter instruções, consulte *Tools\ServiceFabricUpdateService.zip\Readme_InstructionsAndHowTos.txt* no [pacote autónomo](service-fabric-cluster-standalone-package-contents.md).

Para o processo manual, siga estas instruções.

Modifique a configuração do cluster para definir a seguinte propriedade para *falsa* antes de iniciar uma atualização de configuração:

```json
"fabricClusterAutoupgradeEnabled": false,
```

Para mais detalhes de utilização, consulte o comando [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) PowerShell. Certifique-se de atualizar 'clusterConfigurationVersion' no seu JSON antes de iniciar a atualização de configuração.

```powershell
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

### <a name="cluster-upgrade-workflow"></a>Fluxo de trabalho de upgrade de cluster

1. Executar [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade) a partir de um dos nós no cluster e observar a *TargetCodeVersion*.

2. Execute o seguinte a partir de uma máquina ligada à Internet para listar todas as versões compatíveis com a atualização com a versão atual e descarregue o pacote correspondente a partir dos links de descarregamento associados:

    ```powershell
    ###### Get list of all upgrade compatible packages  
    Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion <TargetCodeVersion as noted in Step 1> 
    ```

3. Ligue-se ao cluster de qualquer máquina que tenha acesso ao administrador a todas as máquinas listadas como nós no cluster. A máquina em que este guião é executado não tem de fazer parte do cluster.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

    ###### Here is a filled-out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"
    ```
4. Copie o pacote descarregado na loja de imagens de cluster.

5. Registe o pacote copiado.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file>

    ###### Here is a filled-out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab
    ```
6. Inicie um upgrade de cluster para uma versão disponível.

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
    Pode monitorizar o progresso da atualização no Service Fabric Explorer ou pode executar o seguinte comando PowerShell:

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    Se as políticas de saúde do cluster não forem cumpridas, a atualização é revertida. Para especificar as políticas de saúde personalizadas para o comando Start-ServiceFabricClusterUpgrade, consulte a documentação para [Start-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/start-servicefabricclusterupgrade).

    Depois de corrigir os problemas que resultaram na reversão, inicie novamente a atualização seguindo os mesmos passos descritos anteriormente.

## <a name="next-steps"></a>Passos seguintes
* [Atualizar a configuração de um cluster autónomo](service-fabric-cluster-config-upgrade-windows-server.md)
* Personalize algumas [configurações de cluster de tecido de serviço](service-fabric-cluster-fabric-settings.md).
* [Dimensione o seu aglomerado para dentro e para fora](service-fabric-cluster-scale-in-out.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
