---
title: Limpeza de um aglomerado autónomo
description: Neste tutorial aprende-se a limpar os recursos AWS ou Azure no seu cluster de Tecido de Serviço autónomo.
author: dkkapur
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: bfb23ca5f5eb9540491fbd05efdfd6997db15e6b
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/25/2020
ms.locfileid: "75639025"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Tutorial: limpe o seu cluster autónomo

Os clusters autónomos do Service Fabric oferecem a opção de escolher o seu ambiente e criar um cluster como parte da abordagem "qualquer SO, qualquer cloud" que o Service Fabric está a realizar. Nesta série tutorial, você cria um cluster autónomo hospedado em AWS ou Azure e instala uma aplicação nele.

Este tutorial é a quarta parte de uma série. Esta parte do tutorial mostra-lhe como limpar os recursos AWS ou Azure que criou para hospedar o seu cluster de Tecido de Serviço.

Na quarta parte da série, ficará a saber como:

> [!div class="checklist"]
> * Apagar um cluster do Service Fabric
> * Limpe os seus recursos AWS ou Azure

## <a name="clean-up-service-fabric-cluster"></a>Apagar um cluster do Service Fabric

1. RDP no VM que usou para instalar o Tecido de Serviço.
2. Abra o PowerShell.
3. Altere o diretório para a pasta extraída do segundo tutorial.
4. Execute o seguinte comando para remover o cluster do Service Fabric:

  ```powershell
  .\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
  ```

5. Insira `Y` quando solicitado, se tiver sucesso a sua saída será semelhante à seguinte, com os seus próprios endereços IP substituídos em:

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

1. Inscreva-se na sua Conta AWS.
2. Vá para a consola EC2.
3. Selecione os três nós que criou na primeira parte do tutorial.
4. Clique em **Ações**  >  **Instance State**  >  **Terminate**.

## <a name="clean-up-azure-resources"></a>Limpe os recursos do Azure

1. Inicie sessão no portal do Azure.
2. Aceda à secção **máquinas virtuais.**
3. Selecione caixas de verificação para os três nós que criou na primeira parte do tutorial.
4. Clique em **Apagar**.

## <a name="next-steps"></a>Passos seguintes

Na quarta parte da série, aprendeu a apagar os recursos criados nos passos anteriores.

> [!div class="checklist"]
> * Limpar os seus recursos

> [!div class="nextstepaction"]
> [Voltar ao início](service-fabric-tutorial-standalone-create-infrastructure.md)
