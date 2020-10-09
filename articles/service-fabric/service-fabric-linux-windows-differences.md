---
title: Diferenças de tecido de serviço Azure entre Linux e Windows
description: Diferenças entre o Azure Service Fabric no Linux e o Azure Service Fabric no Windows.
ms.topic: conceptual
ms.date: 2/23/2018
ms.custom: sfrev
ms.openlocfilehash: c064291491110b17c172a561afbd1e65d6c08d06
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75748523"
---
# <a name="differences-between-service-fabric-on-linux-and-windows"></a>Diferenças entre o Service Fabric no Linux e no Windows

Existem algumas funcionalidades que são suportadas no Windows, mas no Linux ainda não. Eventualmente, os conjuntos de funcionalidades estarão em paridade e, com cada versão, esta diferença de funcionalidades diminuirá. Existem as seguintes diferenças entre as últimas versões disponíveis.

* Enviado (Reverse Proxy) está em pré-visualização no Linux
* O instalador autónomo para Linux ainda não está disponível no Linux
* Redirecionamento da consola (não suportado em clusters de produção do Windows ou Linux)
* O Serviço de Análise de Falhas (FAS) no Linux
* Serviço DNS para serviços do Service Fabric (o serviço DNS é suportado para contentores no Linux)
* Equivalentes de comandos da CLI de determinados comandos do Powershell (lista abaixo, a maioria da qual é aplicável apenas a clusters autónomos)
* [Diferenças na implementação do registo que podem afetar a escalabilidade](service-fabric-concepts-scalability.md#choosing-a-platform)

## <a name="powershell-cmdlets-that-do-not-work-against-a-linux-service-fabric-cluster"></a>Cmdlets do PowerShell que não funcionam num cluster do Service Fabric do Linux

* Invoke-ServiceFabricChaosTestScenario
* Invoke-ServiceFabricFailoverTestScenario
* Invoke-ServiceFabricPartitionDataLoss
* Invoke-ServiceFabricPartitionQuorumLoss
* Restart-ServiceFabricPartition
* Start-ServiceFabricNode
* Stop-ServiceFabricNode
* Get-ServiceFabricImageStoreContent
* Get-ServiceFabricChaosReport
* Get-ServiceFabricNodeTransitionProgress
* Get-ServiceFabricPartitionDataLossProgress
* Get-ServiceFabricPartitionQuorumLossProgress
* Get-ServiceFabricPartitionRestartProgress
* Get-ServiceFabricTestCommandStatusList
* Remove-ServiceFabricTestState
* Start-ServiceFabricChaos
* Start-ServiceFabricNodeTransition
* Start-ServiceFabricPartitionDataLoss
* Start-ServiceFabricPartitionQuorumLoss
* Start-ServiceFabricPartitionRestart
* Stop-ServiceFabricChaos
* Stop-ServiceFabricTestCommand
* Get-ServiceFabricNodeConfiguration
* Get-ServiceFabricClusterConfiguration
* Get-ServiceFabricClusterConfigurationUpgradeStatus
* Get-ServiceFabricPackageDebugParameters
* New-ServiceFabricPackageDebugParameter
* New-ServiceFabricPackageSharingPolicy
* Add-ServiceFabricNode
* Copy-ServiceFabricClusterPackage
* Get-ServiceFabricRuntimeSupportedVersion
* Get-ServiceFabricRuntimeUpgradeVersion
* New-ServiceFabricCluster
* New-ServiceFabricNodeConfiguration
* Remove-ServiceFabricCluster
* Remove-ServiceFabricClusterPackage
* Remove-ServiceFabricNodeConfiguration
* Test-ServiceFabricClusterManifest
* Test-ServiceFabricConfiguration
* Update-ServiceFabricNodeConfiguration
* Approve-ServiceFabricRepairTask
* Complete-ServiceFabricRepairTask
* Get-ServiceFabricRepairTask
* Invoke-ServiceFabricDecryptText
* Invoke-ServiceFabricEncryptSecret
* Invoke-ServiceFabricEncryptText
* Invoke-ServiceFabricInfrastructureCommand
* Invoke-ServiceFabricInfrastructureQuery
* Remove-ServiceFabricRepairTask
* Start-ServiceFabricRepairTask
* Stop-ServiceFabricRepairTask
* Update-ServiceFabricRepairTaskHealthPolicy

## <a name="next-steps"></a>Passos seguintes

* [Preparar o ambiente de desenvolvimento no Linux](service-fabric-get-started-linux.md)
* [Prepare your development environment on OSX (Preparar o ambiente de desenvolvimento no OSX)](service-fabric-get-started-mac.md)
* [Criar e implementar a sua primeira aplicação Java do Service Fabric no Linux com o Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Crie e implemente a sua primeira aplicação De Tecido de Serviço Java no Linux utilizando o Plugin de Tecido de Serviço para Eclipse](service-fabric-get-started-eclipse.md)
* [Create your first CSharp application on Linux (Criar a sua primeira aplicação CSharp no Linux)](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Utilizar a CLI do Service Fabric para gerir as aplicações](service-fabric-application-lifecycle-sfctl.md)
