---
title: Tutorial de limpeza de cluster autónomo do Service Fabric - Azure Service Fabric | Microsoft Docs
description: Neste tutorial, você aprenderá a limpar seu cluster autônomo
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
ms.date: 07/22/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: bebe3a2dc83b651e713ee80d7b11068b13096e04
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385159"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Tutorial: Limpar seu cluster autônomo

Os clusters autónomos do Service Fabric oferecem a opção de escolher o seu ambiente e criar um cluster como parte da abordagem "qualquer SO, qualquer cloud" que o Service Fabric está a realizar. Nesta série de tutoriais, você cria um cluster autônomo hospedado no AWS ou no Azure e instala um aplicativo nele.

Este tutorial é a quarta parte de uma série. Esta parte do tutorial mostra como limpar os recursos do AWS ou do Azure que você criou para hospedar seu Service Fabric cluster.

Na quarta parte da série, ficará a saber como:

> [!div class="checklist"]
> * Apagar um cluster do Service Fabric
> * Limpar seus recursos do AWS ou do Azure

## <a name="clean-up-service-fabric-cluster"></a>Apagar um cluster do Service Fabric

1. RDP na VM que você usou para instalar Service Fabric.
2. Abra o PowerShell.
3. Altere o diretório para a pasta extraída do segundo tutorial.
4. Execute o seguinte comando para remover o cluster do Service Fabric:

  ```powershell
  .\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
  ```

5. Insira `Y` quando solicitado, se for bem-sucedido, a saída será parecida com a seguinte, com seus próprios endereços IP substituídos em:

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

1. Entre em sua conta do AWS.
2. Vá para a consola EC2.
3. Selecione os três nós que criou na primeira parte do tutorial.
4. Clique no  > estadoda > instância de ações**encerrar**.

## <a name="clean-up-azure-resources"></a>Limpar recursos do Azure

1. Inicie sessão no Portal do Azure.
2. Vá para a seção **máquinas virtuais** .
3. Marque as caixas de seleção dos três nós que você criou na parte um do tutorial.
4. Clique em **excluir**.

## <a name="next-steps"></a>Passos Seguintes

Na quarta parte da série, aprendeu a apagar os recursos criados nos passos anteriores.

> [!div class="checklist"]
> * Apague os seus recursos

> [!div class="nextstepaction"]
> [Voltar ao início](service-fabric-tutorial-standalone-create-infrastructure.md)
