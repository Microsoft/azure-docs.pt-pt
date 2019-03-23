---
title: O tutorial instalou uma aplicação no seu cluster autónomo do Service Fabric - Azure Service Fabric | Microsoft Docs
description: Neste tutorial, vai aprender a instalar uma aplicação num cluster autónomo do Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
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
ms.openlocfilehash: 392fcdd9b9df3eb37c8e105c846a6bddfddeb4d1
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370436"
---
# <a name="tutorial-deploy-an-application-on-your-service-fabric-standalone-cluster"></a>Tutorial: Implementar uma aplicação no seu cluster autónomo do Service Fabric

Os clusters autónomos do Service Fabric oferecem a opção de escolher o seu ambiente e criar um cluster como parte da abordagem "qualquer SO, qualquer cloud" que o Service Fabric está a realizar. Nesta série de tutoriais, vai criar um cluster autónomo alojado no AWS e implementar uma aplicação no mesmo.

Este tutorial é a terceira parte de uma série.  O cluster autónomo do Service Fabric oferece-lhe a opção de escolher o seu ambiente e criar um cluster como parte da nossa abordagem "qualquer SO, qualquer cloud" com o Service Fabric. Este tutorial mostra como criar a infraestrutura do AWS necessário para alojar este cluster autónomo.

Na terceira parte da série, ficará a saber como:

> [!div class="checklist"]
> * Transferir a aplicação de exemplo
> * Implementar no cluster

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* [Instale o Visual Studio 2017](https://www.visualstudio.com/) e as cargas de trabalho de **desenvolvimento no Azure** e **desenvolvimento na Web e em ASP.NET**.
* [Instale o SDK do Service Fabric](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>Transferir o exemplo de aplicação de votação

Numa janela do comando, execute o seguinte comando para clonar o repositório da aplicação de exemplo para o seu computador local.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

### <a name="deploy-the-app-to-the-service-fabric-cluster"></a>Implementar a aplicação no cluster do Service Fabric

Agora que a aplicação foi transferida, pode implementá-la num cluster diretamente a partir do Visual Studio.

1. Abrir o Visual Studio

2. Selecione **Ficheiro** > **Abrir**

3. Navegue para a pasta para onde clonou o repositório de git para e selecione Voting.sln

4. Clique com o botão direito do rato no projeto de aplicação `Voting` no Explorador de Soluções e escolha **Publicar**

5. Selecione o menu pendente do **Ponto Final de Ligação** e introduza o nome DNS público de um dos nós no cluster.  Por exemplo, `ec2-34-215-183-77.us-west-2.compute.amazonaws.com:19000`

6. Abra o browser preferido e escreva o endereço do cluster (ponto final da ligação, esta aplicação implementa na porta 8080, por exemplo, ec2-34-215-183-77.us-west-2.compute.amazonaws.com:8080).

    ![Resposta da API a partir do Cluster](./media/service-fabric-tutorial-standalone-cluster/deployed-app.png)

## <a name="next-steps"></a>Passos Seguintes

Na terceira parte da série, aprendeu a implementar uma aplicação para o cluster:

> [!div class="checklist"]
> * Transferir a aplicação de exemplo
> * Implementar no cluster

Avance para a quarta parte da série para apagar o cluster.

> [!div class="nextstepaction"]
> [Apague os seus recursos](service-fabric-tutorial-standalone-clean-up.md)