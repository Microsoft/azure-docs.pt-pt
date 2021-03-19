---
title: Limpe um aglomerado autónomo
description: Neste tutorial, aprenda a eliminar recursos AWS ou Azure para o seu cluster de Tecido de Serviço autónomo.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: 0d46e9068a311594f779411c3ccee2b408febb3f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91842891"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Tutorial: limpe o seu cluster autónomo

Os clusters autónomos de tecido de serviço oferecem-lhe a opção de escolher o seu próprio ambiente para hospedar o Service Fabric. Nesta série tutorial, você vai criar um cluster autónomo hospedado em AWS ou Azure e implementar uma aplicação para ele.

Este tutorial é a quarta parte de uma série. Esta parte do tutorial mostra-lhe como eliminar os recursos AWS ou Azure que criou para hospedar o seu cluster de Tecido de Serviço.

Neste artigo, aprenderá a:

> [!div class="checklist"]
> * Remover um cluster de tecido de serviço
> * Elimine os seus recursos AWS ou Azure

## <a name="remove-a-service-fabric-cluster"></a>Remover um cluster de tecido de serviço

1. RDP no VM que usou para instalar o Tecido de Serviço.
2. Abra o PowerShell.
3. Altere o diretório para a pasta extraída do segundo tutorial.
4. Execute o seguinte comando para remover o cluster do Service Fabric:

  ```powershell
  .\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
  ```

5. Entre `Y` quando solicitado. Se tiver sido bem sucedido, a sua saída será semelhante ao seguinte (com os seus próprios endereços IP):

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

## <a name="delete-aws-resources"></a>Eliminar recursos AWS

1. Inscreva-se na sua Conta AWS.
2. Vá para a consola EC2.
3. Selecione os três nós que criou na primeira parte do tutorial.
4. Selecione **Ações**  >  **Instance State**  >  **Terminate**.

## <a name="delete-azure-resources"></a>Eliminar recursos do Azure

1. Inicie sessão no portal do Azure.
2. Aceda à secção **máquinas virtuais.**
3. Selecione caixas de verificação para os três nós que criou na primeira parte do tutorial.
4. Selecione **Eliminar**.

## <a name="next-steps"></a>Próximos passos

Neste tutorial, aprendeu a apagar os recursos que criou em etapas anteriores.

> [!div class="checklist"]
> * Limpar os seus recursos

> [!div class="nextstepaction"]
> [Voltar ao início](service-fabric-tutorial-standalone-create-infrastructure.md)
