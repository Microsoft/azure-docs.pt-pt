---
title: Atualizar o runtime do Service Fabric no Azure | Microsoft Docs
description: Neste tutorial, vai aprender a utilizar o PowerShell para atualizar o runtime de um cluster do Service Fabric alojado no Azure.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/28/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: a881b8b91c906de381eba55e01ead214edb6cfee
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54431117"
---
# <a name="tutorial-upgrade-the-runtime-of-a-service-fabric-cluster-in-azure"></a>Tutorial: Atualizar o runtime de um cluster do Service Fabric no Azure

Este tutorial é a parte três de uma série e mostra como atualizar o runtime do Service Fabric num cluster do Azure Service Fabric. Esta parte do tutorial foi escrita para clusters do Service Fabric em execução no Azure e não se aplica a clusters autónomos do Service Fabric.

> [!WARNING]
> Esta parte do tutorial requer o PowerShell. As ferramentas de CLI do Azure ainda não suportam a atualização do runtime do cluster. Em alternativa, um cluster pode ser atualizado no portal. Para obter mais informações, veja [Atualizar um cluster do Azure Service Fabric](service-fabric-cluster-upgrade.md).

Se o cluster já estiver a executar o runtime mais recente do Service Fabric, não é necessário executar este passo. No entanto, este artigo pode ser utilizado para instalar qualquer runtime suportado num cluster do Azure Service Fabric.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Ler a versão do cluster
> * Definir a versão do cluster

Nesta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * Criar um [cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) ou um [cluster do Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) protegidos no Azure utilizando um modelo
> * [Reduzir ou aumentar um cluster horizontalmente](service-fabric-tutorial-scale-cluster.md)
> * Atualizar o runtime de um cluster
> * [Eliminar um cluster](service-fabric-tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Instale o [módulo do Azure PowerShell na versão 4.1 ou superior](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) ou a [CLI do Azure ](/cli/azure/install-azure-cli).
* Crie um [cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) ou um [cluster do Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) protegidos no Azure
* Se implementar um cluster do Windows, configure um ambiente de desenvolvimento do Windows. Instale o [Visual Studio 2017](https://www.visualstudio.com) e as cargas de trabalho **desenvolvimento no Azure**, **desenvolvimento em ASP.NET e na Web** e **desenvolvimento em várias plataformas .NET Core**.  Em seguida, configure um [ambiente de desenvolvimento .NET](service-fabric-get-started.md).
* Se implementar um cluster do Linux, configure um ambiente de desenvolvimento Java no [Linux](service-fabric-get-started-linux.md) ou no [MacOS](service-fabric-get-started-mac.md).  Instale a [CLI do Service Fabric](service-fabric-cli.md).

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão na sua conta do Azure, selecione a sua subscrição antes de executar os comandos do Azure.

```powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="get-the-runtime-version"></a>Obter a versão de runtime

Depois de ter ligado ao Azure e selecionado a subscrição que contém o cluster do Service Fabric, pode obter a versão de runtime do cluster.

```powershell
Get-AzureRmServiceFabricCluster -ResourceGroupName SFCLUSTERTUTORIALGROUP -Name aztestcluster `
    | Select-Object ClusterCodeVersion
```

Em alternativa, pode obter apenas uma lista de todos os clusters na sua subscrição com o seguinte:

```powershell
Get-AzureRmServiceFabricCluster | Select-Object Name, ClusterCodeVersion
```

Tome nota do valor de **ClusterCodeVersion**. Este valor vai ser utilizado na secção seguinte.

## <a name="upgrade-the-runtime"></a>Atualizar o runtime

Utilize o valor de **ClusterCodeVersion** da secção anterior com o cmdlet `Get-ServiceFabricRuntimeUpgradeVersion` para descobrir que versões estão disponíveis para efetuar a atualização. Este cmdlet só pode ser executado a partir de um computador ligado à Internet. Por exemplo, se quiser ver para que versões de runtime pode atualizar a partir da versão `5.7.198.9494`, utilize o seguinte comando:

```powershell
Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion "5.7.198.9494"
```

Com uma lista de versões, pode dizer ao cluster do Azure Service Fabric para atualizar para um runtime mais recente. Por exemplo, se versão `6.0.219.9494` estiver disponível para atualizar para a mesma, utilize o seguinte comando para atualizar o cluster.

```powershell
Set-AzureRmServiceFabricUpgradeType -ResourceGroupName SFCLUSTERTUTORIALGROUP `
                                    -Name aztestcluster `
                                    -UpgradeMode Manual `
                                    -Version "6.0.219.9494"
```

> [!IMPORTANT]
> A atualização do runtime do cluster pode demorar muito tempo a concluir. O PowerShell está bloqueado enquanto a atualização está em execução. Pode utilizar outra sessão do PowerShell para verificar o estado da atualização.

O estado da atualização pode ser monitorizado com o PowerShell ou a CLI do Azure Service Fabric (sfctl).

Em primeiro lugar, ligue ao cluster com o certificado SSL criado na primeira parte do tutorial. Utilize o cmdlet `Connect-ServiceFabricCluster` ou `sfctl cluster upgrade-status`.

```powershell
$endpoint = "<mycluster>.southcentralus.cloudapp.azure.com:19000"
$thumbprint = "63EB5BA4BC2A3BADC42CA6F93D6F45E5AD98A1E4"

Connect-ServiceFabricCluster -ConnectionEndpoint $endpoint `
                             -KeepAliveIntervalInSec 10 `
                             -X509Credential -ServerCertThumbprint $thumbprint `
                             -FindType FindByThumbprint -FindValue $thumbprint `
                             -StoreLocation CurrentUser -StoreName My
```

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Em seguida, utilize `Get-ServiceFabricClusterUpgrade` ou `sfctl cluster upgrade-status` para apresentar o estado. É apresentado algo semelhante ao resultado seguinte.

```powershell
Get-ServiceFabricClusterUpgrade

TargetCodeVersion                          : 6.0.219.9494
TargetConfigVersion                        : 3
StartTimestampUtc                          : 11/28/2017 3:09:48 AM
UpgradeState                               : RollingForwardPending
UpgradeDuration                            : 00:09:00
CurrentUpgradeDomainDuration               : 00:09:00
NextUpgradeDomain                          : 1
UpgradeDomainsStatus                       : { "0" = "Completed";
                                             "1" = "Pending";
                                             "2" = "Pending";
                                             "3" = "Pending";
                                             "4" = "Pending" }
UpgradeKind                                : Rolling
RollingUpgradeMode                         : Monitored
FailureAction                              : Rollback
ForceRestart                               : False
UpgradeReplicaSetCheckTimeout              : 37201.09:59:01
HealthCheckWaitDuration                    : 00:05:00
HealthCheckStableDuration                  : 00:05:00
HealthCheckRetryTimeout                    : 00:45:00
UpgradeDomainTimeout                       : 02:00:00
UpgradeTimeout                             : 12:00:00
ConsiderWarningAsError                     : False
MaxPercentUnhealthyApplications            : 0
MaxPercentUnhealthyNodes                   : 100
ApplicationTypeHealthPolicyMap             : {}
EnableDeltaHealthEvaluation                : True
MaxPercentDeltaUnhealthyNodes              : 0
MaxPercentUpgradeDomainDeltaUnhealthyNodes : 0
ApplicationHealthPolicyMap                 : {}
```

```azurecli
sfctl cluster upgrade-status

{
  "codeVersion": "6.0.219.9494",
  "configVersion": "3",

... item cut to save space ...

  },
  "upgradeDomains": [
    {
      "name": "0",
      "state": "Completed"
    },
    {
      "name": "1",
      "state": "Pending"
    },
    {
      "name": "2",
      "state": "Pending"
    },
    {
      "name": "3",
      "state": "Pending"
    },
    {
      "name": "4",
      "state": "Pending"
    }
  ],
  "upgradeDurationInMilliseconds": "PT1H2M4.63889S",
  "upgradeState": "RollingForwardPending"
}
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Obter a versão do runtime do cluster
> * Atualizar o runtime do cluster
> * Monitorizar a atualização

