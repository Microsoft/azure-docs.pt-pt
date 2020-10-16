---
title: Atualizar a configuração de um cluster autónomo
description: Saiba como atualizar a configuração que executa um cluster de tecido de serviço autónomo.
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: 3cb1d40f5b32415588d3fd5a108967cfb4c0e534
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91842619"
---
# <a name="upgrade-the-configuration-of-a-standalone-cluster"></a>Atualizar a configuração de um cluster autónomo 

Para qualquer sistema moderno, a capacidade de upgrade é fundamental para o sucesso a longo prazo do seu produto. Um cluster Azure Service Fabric é um recurso que possui. Este artigo descreve como atualizar as definições de configuração do seu cluster de tecido de serviço autónomo.

## <a name="customize-cluster-settings-in-the-clusterconfigjson-file"></a>Personalize as definições do cluster no ClusterConfig.jsno ficheiro
Os aglomerados autónomos são configurados através do *ClusterConfig.jsem* arquivo. Para saber mais sobre as diferentes definições, consulte [as definições de Configuração para um cluster Windows autónomo](service-fabric-cluster-manifest.md).

Pode adicionar, atualizar ou remover as definições na `fabricSettings` secção sob a secção propriedades do [Cluster](./service-fabric-cluster-manifest.md#cluster-properties) em *ClusterConfig.jsem*. 

Por exemplo, o seguinte JSON adiciona uma nova definição *MaxDiskQuotaInMB* à secção *de Diagnóstico em* `fabricSettings` :

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

Depois de modificar as definições do seu ClusterConfig.jsno ficheiro, [teste a configuração do cluster](#test-the-cluster-configuration) e, em seguida, [atualize a configuração do cluster](#upgrade-the-cluster-configuration) para aplicar as definições no seu cluster. 

## <a name="test-the-cluster-configuration"></a>Testar a configuração do cluster
Antes de iniciar a atualização de configuração, pode testar a sua nova configuração de cluster JSON executando o seguinte script PowerShell no pacote autónomo:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File>
```

Ou use este script:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File> -FabricRuntimePackagePath <Path to the .cab file which you want to test the configuration against>
```

Algumas configurações não podem ser atualizadas, tais como pontos finais, nome de cluster, nó IP, etc. A nova configuração do cluster JSON é testada contra a antiga e lança erros na janela PowerShell se houver algum problema.

## <a name="upgrade-the-cluster-configuration"></a>Atualizar a configuração do cluster
Para atualizar a atualização da configuração do cluster, executar [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade). A atualização de configuração é processada domínio de atualização por domínio de upgrade.

```powershell
Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

## <a name="upgrade-cluster-certificate-configuration"></a>Configuração de certificado de cluster de upgrade
Um certificado de cluster é utilizado para a autenticação entre nós de cluster. O capotamento do certificado deve ser efetuado com cuidado extra porque a falha bloqueia a comunicação entre os nós do cluster.

Quatro opções são suportadas:  

* Atualização de certificado único: A trajetória de atualização é o Certificado A (Primário) -> Certificado B (Primário) -> Certificado C (Primário) ->....

* Atualização de certificado duplo: A trajetória de atualização é o Certificado A (Primário) -> (Primário) e B (Secundário) -> Certificado B (Primário) -> Certificado B (Primário) e C (Secundário) -> Certificado C (Primário) ->....

* Atualização do tipo de certificado: Configuração do certificado baseado em impressão digital <-> configuração do certificado baseado em CommonName. Por exemplo, Impressão digital de certificado A (Primária) e Impressão Digital B (Secundário) -> Certificado CommonName C.

* Atualização da impressão digital do emitente de certificado: A trajetória de atualização é Certificate CN=A,EmiterThumbprint=IT1 (Primária) -> Certificate CN=A,EmiterThumbprint=IT1,IT2 (Primária) -> Certificate CN=A,EmiterThumbprint=IT2 (Primário).


## <a name="next-steps"></a>Passos seguintes
* Saiba como personalizar algumas [configurações de cluster de tecido de serviço](service-fabric-cluster-fabric-settings.md).
* Aprenda a [escalar o seu cluster para dentro e para fora](service-fabric-cluster-scale-in-out.md).
* Saiba mais [sobre atualizações de aplicações.](service-fabric-application-upgrade.md)

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
