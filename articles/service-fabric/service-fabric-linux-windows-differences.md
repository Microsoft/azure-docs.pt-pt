---
title: Diferenças do Azure Service Fabric entre o Linux e o Windows | Microsoft Docs
description: Diferenças entre o Azure Service Fabric no Linux e o Azure Service Fabric no Windows.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: a7ff2d7e02b557676d2ba569f7baa216d07221b9
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70192547"
---
# <a name="differences-between-service-fabric-on-linux-and-windows"></a>Diferenças entre o Service Fabric no Linux e no Windows

Existem algumas funcionalidades que são suportadas no Windows, mas no Linux ainda não. Eventualmente, os conjuntos de funcionalidades estarão em paridade e, com cada versão, esta diferença de funcionalidades diminuirá. Existem as seguintes diferenças entre as versões mais recentes disponíveis.

* Envoy (proxy reverso) está em versão prévia no Linux
* O instalador autónomo para Linux ainda não está disponível no Linux
* Redirecionamento da consola (não suportado em clusters de produção do Windows ou Linux)
* O Serviço de Análise de Falhas (FAS) no Linux
* Serviço DNS para serviços do Service Fabric (o serviço DNS é suportado para contentores no Linux)
* Equivalentes de comandos da CLI de determinados comandos do Powershell (lista abaixo, a maioria da qual é aplicável apenas a clusters autónomos)
* [Diferenças na implementação de log que podem afetar a escalabilidade](service-fabric-concepts-scalability.md#choosing-a-platform)

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
* [Criar e implementar a sua primeira aplicação Java do Service Fabric no Linux com o Plug-in do Service Fabric para Eclipse](service-fabric-get-started-eclipse.md)
* [Create your first CSharp application on Linux (Criar a sua primeira aplicação CSharp no Linux)](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Utilizar a CLI do Service Fabric para gerir as aplicações](service-fabric-application-lifecycle-sfctl.md)
