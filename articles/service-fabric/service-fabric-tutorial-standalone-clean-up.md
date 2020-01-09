---
title: Limpar um cluster autônomo
description: Neste tutorial, você aprenderá a limpar os recursos do AWS ou do Azure no Cluster Service Fabric autônomo.
author: dkkapur
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: bfb23ca5f5eb9540491fbd05efdfd6997db15e6b
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75639025"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Tutorial: limpe o seu cluster autónomo

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
4. Clique em **ações** > **estado da instância** > **encerrar**.

## <a name="clean-up-azure-resources"></a>Limpar recursos do Azure

1. Inicie sessão no Portal do Azure.
2. Vá para a seção **máquinas virtuais** .
3. Marque as caixas de seleção dos três nós que você criou na parte um do tutorial.
4. Clique em **excluir**.

## <a name="next-steps"></a>Passos seguintes

Na quarta parte da série, aprendeu a apagar os recursos criados nos passos anteriores.

> [!div class="checklist"]
> * Apague os seus recursos

> [!div class="nextstepaction"]
> [Voltar ao início](service-fabric-tutorial-standalone-create-infrastructure.md)
