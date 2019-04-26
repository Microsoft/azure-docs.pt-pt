---
title: Atualizar a configuração de um cluster autónomo de Azure Service Fabric | Documentos da Microsoft
description: Saiba como atualizar a configuração que executa um cluster autónomo do Service Fabric.
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
ms.openlocfilehash: f99c1ebb64bf881bcd42f15e13bb81b96ccfa064
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60387133"
---
# <a name="upgrade-the-configuration-of-a-standalone-cluster"></a>Atualizar a configuração de um cluster autónomo 

Para qualquer sistema moderno, a capacidade de atualizar é fundamental para o sucesso de longo prazo de seu produto. Um cluster do Azure Service Fabric é um recurso que é proprietário. Este artigo descreve como atualizar as definições de configuração do seu cluster autónomo do Service Fabric.

## <a name="customize-cluster-settings-in-the-clusterconfigjson-file"></a>Personalizar definições de cluster no ficheiro ClusterConfig.json
Clusters autónomos que são configuradas através da *ClusterConfig.json* ficheiro. Para saber mais sobre as definições diferentes, veja [definições de configuração para um cluster autónomo de Windows](service-fabric-cluster-manifest.md).

Pode adicionar, atualizar ou remover definições no `fabricSettings` secção sob o [propriedades do Cluster](./service-fabric-cluster-manifest.md#cluster-properties) secção *ClusterConfig.json*. 

Por exemplo, o JSON seguinte adiciona uma nova definição *MaxDiskQuotaInMB* para o *diagnóstico* secção em `fabricSettings`:

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

Depois de modificar as definições no seu ficheiro de ClusterConfig.json [testar a configuração de cluster](#test-the-cluster-configuration) e, em seguida [atualizar a configuração de cluster](#upgrade-the-cluster-configuration) para aplicar as definições no seu cluster. 

## <a name="test-the-cluster-configuration"></a>Testar a configuração de cluster
Antes de iniciar a atualização de configuração, pode testar a nova configuração de cluster JSON ao executar o seguinte script do PowerShell no pacote autónomo:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File>
```

Em alternativa, utilize este script:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File> -FabricRuntimePackagePath <Path to the .cab file which you want to test the configuration against>
```

Algumas configurações não podem ser atualizado, como pontos de extremidade, nome do cluster, IP de nó, etc. A nova configuração de cluster JSON é testada em relação ao antigo e gera erros na janela do PowerShell, se houver um problema.

## <a name="upgrade-the-cluster-configuration"></a>Atualizar a configuração de cluster
Para atualizar a atualização de configuração do cluster, execute [Start-ServiceFabricClusterConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade). A atualização de configuração é o domínio de atualização processado por domínio de atualização.

```powershell
Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

## <a name="upgrade-cluster-certificate-configuration"></a>Atualizar configuração de certificado de cluster
Um certificado de cluster é utilizado para autenticação entre nós de cluster. O rollover de certificado deve ser realizado com cuidado extra porque falha bloqueia a comunicação entre nós de cluster.

São suportadas quatro opções:  

* Atualização do certificado único: O caminho de atualização é o certificado de um site (primário) -> B de certificado (primário) -> C de certificado (primário) ->...

* Double atualização do certificado: O caminho de atualização é um site (primário) de certificado do certificado (primário) -> e B (secundário) -> B de certificado (primário) -> (primário) do certificado B e C (secundário) -> C de certificado (primário) ->...

* Atualização do tipo de certificado: Configuração de certificado com base em CommonName de configuração <> – baseado na thumbprint do certificado. Por exemplo, o Thumbprint do certificado (primário) e Thumbprint B (secundário) -> C. CommonName do certificado

* Atualização de thumbprint do emissor de certificado: O caminho de atualização é o CN do certificado = A, IssuerThumbprint = IT1 (principal) -> CN do certificado = A, IssuerThumbprint = IT1, IT2 (principal) -> certificado CN = A, IssuerThumbprint = IT2 (principal).


## <a name="next-steps"></a>Passos Seguintes
* Saiba como personalizar algumas [definições de cluster do Service Fabric](service-fabric-cluster-fabric-settings.md).
* Saiba como [dimensionar o seu cluster e reduzir](service-fabric-cluster-scale-up-down.md).
* Saiba mais sobre [as atualizações de aplicações](service-fabric-application-upgrade.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
