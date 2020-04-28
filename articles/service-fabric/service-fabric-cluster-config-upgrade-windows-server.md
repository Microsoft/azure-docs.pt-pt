---
title: Atualizar a configuração de um cluster autónomo
description: Aprenda a atualizar a configuração que executa um cluster de Tecido de Serviço autónomo.
author: dkkapur
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 8e7e01dac29cb9ba91c83270dac4e46c73b2089e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75610132"
---
# <a name="upgrade-the-configuration-of-a-standalone-cluster"></a>Atualizar a configuração de um cluster autónomo 

Para qualquer sistema moderno, a capacidade de upgrade é fundamental para o sucesso a longo prazo do seu produto. Um cluster Azure Service Fabric é um recurso que possui. Este artigo descreve como atualizar as configurações de configuração do seu cluster de Tecido de Serviço autónomo.

## <a name="customize-cluster-settings-in-the-clusterconfigjson-file"></a>Personalize as definições de cluster no ficheiro ClusterConfig.json
Os clusters autónomos são configurados através do ficheiro *ClusterConfig.json.* Para saber mais sobre as diferentes definições, consulte as definições de [Configuração para um cluster Windows autónomo](service-fabric-cluster-manifest.md).

Pode adicionar, atualizar ou remover `fabricSettings` as definições na secção sob a secção de propriedades do [Cluster](./service-fabric-cluster-manifest.md#cluster-properties) em *ClusterConfig.json*. 

Por exemplo, o seguinte JSON adiciona uma nova definição *MaxDiskQuotaInMB* à secção *de Diagnósticos* em : `fabricSettings`

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

Depois de ter modificado as definições no ficheiro ClusterConfig.json, [teste a configuração do cluster](#test-the-cluster-configuration) e, em seguida, [atualize a configuração do cluster](#upgrade-the-cluster-configuration) para aplicar as definições ao seu cluster. 

## <a name="test-the-cluster-configuration"></a>Testar a configuração do cluster
Antes de iniciar a atualização de configuração, pode testar a sua nova configuração de cluster JSON executando o seguinte script PowerShell no pacote autónomo:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File>
```

Ou usar este guião:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File> -FabricRuntimePackagePath <Path to the .cab file which you want to test the configuration against>
```

Algumas configurações não podem ser atualizadas, tais como pontos finais, nome de cluster, IP do nó, etc. A nova configuração do cluster JSON é testada contra a antiga e lança erros na janela PowerShell se houver um problema.

## <a name="upgrade-the-cluster-configuration"></a>Atualizar a configuração do cluster
Para atualizar a atualização de configuração do cluster, executar [Start-ServiceFabricClusterConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade). A atualização de configuração é processada no domínio de upgrade por domínio de atualização.

```powershell
Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

## <a name="upgrade-cluster-certificate-configuration"></a>Configuração de certificado de cluster de upgrade
É utilizado um certificado de cluster para autenticação entre os nós do cluster. O capotamento do certificado deve ser realizado com especial cuidado porque a falha bloqueia a comunicação entre os nós do cluster.

São apoiadas quatro opções:  

* Atualização de certificado único: A trajetória de atualização é certificado A (Primário) -> Certificado B (Primário) -> Certificado C (Primário) ->....

* Atualização dupla do certificado: A trajetória de atualização é o Certificado A (Primário) -> Certificado A (Primário) e B (Secundário) -> Certificado B (Primário) -> Certificado B (Primário) e C (Secundário) -> Certificado C (Primário) ->....

* Atualização do tipo de certificado: Configuração de certificado baseado em impressão digital < configuração do certificado baseado em Nome Comum > CommonName. Por exemplo, Impressão digital do certificado A (Primária) e impressão digital B (secundária) -> Certificado Comum C.

* Atualização da impressão digital do emitente do certificado: O caminho de atualização é certificado CN=A,EmitenteThumbprint=IT1 (Primário) -> Certificado CN=A,EmitenteThumbprint=IT1,IT2 (Primário) -> Certificado CN=A,EmitenteThumbprint=IT2 (Primário).


## <a name="next-steps"></a>Passos seguintes
* Aprenda a personalizar algumas [definições](service-fabric-cluster-fabric-settings.md)de cluster de Tecido de Serviço .
* Aprenda a [escalar o seu cluster para dentro e para fora.](service-fabric-cluster-scale-up-down.md)
* Conheça as atualizações de [aplicações.](service-fabric-application-upgrade.md)

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
