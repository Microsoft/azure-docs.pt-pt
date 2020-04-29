---
title: Instale uma aplicação num cluster autónomo
description: Neste tutorial, aprende a instalar uma aplicação no seu cluster de Tecido de Serviço autónomo.
author: dkkapur
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 124fc6035c1d0ad504a34b7db227f0f4675f24f7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75613963"
---
# <a name="tutorial-deploy-an-application-on-your-service-fabric-standalone-cluster"></a>Tutorial: implementar uma aplicação no cluster autónomo do Service Fabric

Os clusters autónomos do Service Fabric oferecem a opção de escolher o seu ambiente e criar um cluster como parte da abordagem "qualquer SO, qualquer cloud" que o Service Fabric está a realizar. Nesta série tutorial, você cria um cluster autónomo hospedado na AWS e implementa uma aplicação nele.

Este tutorial é a terceira parte de uma série.  Os clusters autónomos service Fabric oferecem-lhe a opção de escolher o seu próprio ambiente e criar um cluster como parte da nossa abordagem "qualquer SISTEMA, qualquer nuvem" com o Tecido de Serviço. Este tutorial mostra como criar a infraestrutura do AWS necessário para alojar este cluster autónomo.

Na terceira parte da série, ficará a saber como:

> [!div class="checklist"]
> * Transferir a aplicação de exemplo
> * Implementar no cluster

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* [Instale o Visual Studio 2019](https://www.visualstudio.com/) e instale o **desenvolvimento do Azure** e ASP.NET e trabalhos de **desenvolvimento web.**
* [Instale o SDK de Tecido de Serviço](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>Transferir o exemplo de aplicação de votação

Numa janela do comando, execute o seguinte comando para clonar o repositório da aplicação de exemplo para o seu computador local.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

### <a name="deploy-the-app-to-the-service-fabric-cluster"></a>Implementar a aplicação no cluster do Service Fabric

Agora que a aplicação foi transferida, pode implementá-la num cluster diretamente a partir do Visual Studio.

1. Abrir o Visual Studio

2. Selecione **File** > **Open**

3. Navegue para a pasta para onde clonou o repositório de git para e selecione Voting.sln

4. Clique no projeto `Voting` de candidatura no Solution Explorer e escolha **Publicar**

5. Selecione o menu pendente do **Ponto Final de Ligação** e introduza o nome DNS público de um dos nós no cluster.  Por exemplo, `ec2-34-215-183-77.us-west-2.compute.amazonaws.com:19000`. Note que em Azure, um nome de domínio totalmente qualificado (FQDN) não é dado automaticamente, mas pode ser facilmente [definido na página de Visão Geral vM.](https://docs.microsoft.com/azure/virtual-machines/linux/portal-create-fqdn)

6. Abra o browser preferido e escreva o endereço do cluster (ponto final da ligação, esta aplicação implementa na porta 8080, por exemplo, ec2-34-215-183-77.us-west-2.compute.amazonaws.com:8080).

    ![Resposta da API a partir do Cluster](./media/service-fabric-tutorial-standalone-cluster/deployed-app.png)

## <a name="next-steps"></a>Passos seguintes

Na terceira parte da série, aprendeu a implementar uma aplicação para o cluster:

> [!div class="checklist"]
> * Transferir a aplicação de exemplo
> * Implementar no cluster

Avance para a quarta parte da série para apagar o cluster.

> [!div class="nextstepaction"]
> [Apague os seus recursos](service-fabric-tutorial-standalone-clean-up.md)