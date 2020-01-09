---
title: Atualizar a configuração de um cluster autônomo
description: Saiba como atualizar a configuração que executa um Cluster Service Fabric autônomo.
author: dkkapur
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 8e7e01dac29cb9ba91c83270dac4e46c73b2089e
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610132"
---
# <a name="upgrade-the-configuration-of-a-standalone-cluster"></a>Atualizar a configuração de um cluster autônomo 

Para qualquer sistema moderno, a capacidade de atualizar é a chave para o sucesso de longo prazo de seu produto. Um Cluster Service Fabric do Azure é um recurso que você possui. Este artigo descreve como atualizar as definições de configuração do Cluster Service Fabric autônomo.

## <a name="customize-cluster-settings-in-the-clusterconfigjson-file"></a>Personalizar as configurações de cluster no arquivo ClusterConfig. JSON
Os clusters autônomos são configurados por meio do arquivo *ClusterConfig. JSON* . Para saber mais sobre as diferentes configurações, confira [definições de configuração para um cluster autônomo do Windows](service-fabric-cluster-manifest.md).

Você pode adicionar, atualizar ou remover as configurações na seção `fabricSettings` na seção [Propriedades do cluster](./service-fabric-cluster-manifest.md#cluster-properties) em *ClusterConfig. JSON*. 

Por exemplo, o JSON a seguir adiciona uma nova configuração *MaxDiskQuotaInMB* à seção *diagnóstico* em `fabricSettings`:

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

Depois de modificar as configurações no arquivo ClusterConfig. JSON, [teste a configuração do cluster](#test-the-cluster-configuration) e, em seguida, [atualize a configuração do cluster](#upgrade-the-cluster-configuration) para aplicar as configurações ao cluster. 

## <a name="test-the-cluster-configuration"></a>Testar a configuração do cluster
Antes de iniciar a atualização de configuração, você pode testar seu novo JSON de configuração de cluster executando o seguinte script do PowerShell no pacote autônomo:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File>
```

Ou use este script:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File> -FabricRuntimePackagePath <Path to the .cab file which you want to test the configuration against>
```

Algumas configurações não podem ser atualizadas, como pontos de extremidade, nome do cluster, IP do nó, etc. O novo JSON de configuração de cluster é testado em relação ao antigo e gera erros na janela do PowerShell se houver um problema.

## <a name="upgrade-the-cluster-configuration"></a>Atualizar a configuração do cluster
Para atualizar a atualização de configuração de cluster, execute [Start-ServiceFabricClusterConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade). A atualização de configuração é processada domínio de atualização pelo domínio de atualização.

```powershell
Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

## <a name="upgrade-cluster-certificate-configuration"></a>Atualizar configuração de certificado do cluster
Um certificado de cluster é usado para autenticação entre nós de cluster. A substituição do certificado deve ser executada com cuidado extra, pois a falha bloqueia a comunicação entre os nós do cluster.

Há suporte para quatro opções:  

* Atualização de certificado único: o caminho de atualização é certificado A (primário)-> certificado B (primário)-> certificado C (primário)->....

* Atualização de certificado duplo: o caminho de atualização é certificado A (primário)-> certificado A (primário) e B (secundário)-> certificado B (primário)-> certificado B (primário) e C (secundário)-> certificado C (primário)->....

* Atualização de tipo de certificado: configuração de certificado baseada em impressão digital <-> configuração de certificado com base em comum. Por exemplo, impressão digital do certificado A (primária) e impressão digital B (secundária)-> certificado CommonName C.

* Atualização de impressão digital do emissor do certificado: o caminho de atualização é certificado CN = A, IssuerThumbprint = IT1 (primário)-> certificado CN = A, IssuerThumbprint = IT1, IT2 (primário)-> certificado CN = A, IssuerThumbprint = IT2 (primário).


## <a name="next-steps"></a>Passos seguintes
* Saiba como personalizar algumas [configurações de cluster Service Fabric](service-fabric-cluster-fabric-settings.md).
* Saiba como [dimensionar o cluster para dentro e para fora](service-fabric-cluster-scale-up-down.md).
* Saiba mais sobre [atualizações de aplicativos](service-fabric-application-upgrade.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
