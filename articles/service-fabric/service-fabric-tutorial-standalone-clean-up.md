---
title: Tutorial de limpeza de cluster autónomo do Service Fabric - Azure Service Fabric | Microsoft Docs
description: Neste tutorial irá aprender a limpar o seu cluster autónomo
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/11/2018
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 9127ad1fe148f85779913454adf6578a9a8a1055
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274088"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Tutorial: Limpar o seu cluster autónomo

Os clusters autónomos do Service Fabric oferecem a opção de escolher o seu ambiente e criar um cluster como parte da abordagem "qualquer SO, qualquer cloud" que o Service Fabric está a realizar. Esta série de tutoriais, criar um cluster autónomo hospedado no AWS ou do Azure e instalar uma aplicação para o mesmo.

Este tutorial é a quarta parte de uma série. Esta parte do tutorial mostra-lhe como limpar o AWS ou recursos do Azure que criou para alojar o seu cluster do Service Fabric.

Na quarta parte da série, ficará a saber como:

> [!div class="checklist"]
> * Apagar um cluster do Service Fabric
> * Limpar o AWS ou recursos do Azure

## <a name="clean-up-service-fabric-cluster"></a>Apagar um cluster do Service Fabric

1. RDP para a VM que utilizou para instalar o Service Fabric.
2. Abra o PowerShell.
3. Altere o diretório para a pasta extraída do segundo tutorial.
4. Execute o seguinte comando para remover o cluster do Service Fabric:

  ```powershell
  .\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
  ```

5. Introduza `Y` quando lhe for pedido, se ele foi concluída com êxito a sua saída ficará semelhante ao seguinte, com seus próprios endereços IP substituídos no:

  ```powershell
  Best Practices Analyzer completed successfully.
  Removing configuration from machine 172.31.21.141
  Removing configuration from machine 172.31.27.1
  Removing configuration from machine 172.31.20.163
  Configuration removed from machine 172.31.21.141
  Configuration removed from machine 172.31.27.1
  Configuration removed from machine 172.31.20.163
  The cluster is successfully removed.
  ```

## <a name="clean-up-aws-resources"></a>Apague os recursos do AWS

1. Inicie sessão na sua conta AWS.
2. Vá para a consola EC2.
3. Selecione os três nós que criou na primeira parte do tutorial.
4. Clique em **ações** > **estado da instância** > **terminar**.

## <a name="clean-up-azure-resources"></a>Limpar os recursos do Azure

1. Inicie sessão no Portal do Azure.
2. Vá para o **máquinas virtuais** secção.
3. Selecione as caixas de verificação para os três nós que criou na primeira parte do tutorial.
4. Clique em **eliminar**.

## <a name="next-steps"></a>Passos Seguintes

Na quarta parte da série, aprendeu a apagar os recursos criados nos passos anteriores.

> [!div class="checklist"]
> * Apague os seus recursos

> [!div class="nextstepaction"]
> [Voltar ao início](service-fabric-tutorial-standalone-create-infrastructure.md)
