---
title: O tutorial instalou uma aplicação no seu cluster autónomo do Service Fabric - Azure Service Fabric | Microsoft Docs
description: Neste tutorial, saiba como instalar uma aplicação no seu cluster autónomo do Service Fabric.
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
ms.openlocfilehash: 5bc326bbc16ef93d484425f26b6f8226150c77c6
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66302429"
---
# <a name="tutorial-deploy-an-application-on-your-service-fabric-standalone-cluster"></a>Tutorial: Implementar uma aplicação no seu cluster autónomo do Service Fabric

Os clusters autónomos do Service Fabric oferecem a opção de escolher o seu ambiente e criar um cluster como parte da abordagem "qualquer SO, qualquer cloud" que o Service Fabric está a realizar. Esta série de tutoriais, criar um cluster autónomo alojado no AWS e implementar uma aplicação para o mesmo.

Este tutorial é a terceira parte de uma série.  Clusters autónomos do Service Fabric oferecem a opção para escolher o seu próprio ambiente e criar um cluster como parte da nossa abordagem "Qualquer sistema operacional, qualquer cloud" com o Service Fabric. Este tutorial mostra como criar a infraestrutura do AWS necessário para alojar este cluster autónomo.

Na terceira parte da série, ficará a saber como:

> [!div class="checklist"]
> * Transferir a aplicação de exemplo
> * Implementar no cluster

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* [Instalar o Visual Studio 2019](https://www.visualstudio.com/) e instale o **desenvolvimento do Azure** e **desenvolvimento na web e ASP.NET** cargas de trabalho.
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