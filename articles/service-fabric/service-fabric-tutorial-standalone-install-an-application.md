---
title: Instale uma aplicação num cluster autónomo
description: Neste tutorial, aprende-se a instalar uma aplicação no seu cluster de Tecido de Serviço autónomo.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: ae946321b34f12c816a717db4a3d07f57feefe52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96485365"
---
# <a name="tutorial-deploy-an-application-on-your-service-fabric-standalone-cluster"></a>Tutorial: implementar uma aplicação no cluster autónomo do Service Fabric

Os clusters autónomos do Service Fabric oferecem a opção de escolher o seu ambiente e criar um cluster como parte da abordagem "qualquer SO, qualquer cloud" que o Service Fabric está a realizar. Nesta série tutorial, você cria um cluster autónomo hospedado na AWS e implementa uma aplicação nele.

Este tutorial é a terceira parte de uma série.  Os clusters autónomos de tecido de serviço oferecem-lhe a opção de escolher o seu próprio ambiente e criar um cluster como parte da nossa abordagem "qualquer SISTEMA, qualquer nuvem" com o Service Fabric. Este tutorial mostra como criar a infraestrutura do AWS necessário para alojar este cluster autónomo.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Transferir a aplicação de exemplo
> * Implementar no cluster

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* [Instale o Visual Studio 2019](https://www.visualstudio.com/) e instale as cargas de trabalho de desenvolvimento do **Azure** e **ASP.NET e desenvolvimento web.**
* [Instalar o Service Fabric SDK](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>Transferir o exemplo de aplicação de votação

Numa janela do comando, execute o seguinte comando para clonar o repositório da aplicação de exemplo para o seu computador local.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

### <a name="deploy-the-app-to-the-service-fabric-cluster"></a>Implementar a aplicação no cluster do Service Fabric

Agora que a aplicação foi transferida, pode implementá-la num cluster diretamente a partir do Visual Studio.

1. Abrir o Visual Studio

2. Selecione **Abrir**  >  **ficheiros**

3. Navegue para a pasta para onde clonou o repositório de git para e selecione Voting.sln

4. Clique com o botão direito no projeto de `Voting` aplicação no Solution Explorer e escolha **Publicar**

5. Selecione o menu pendente do **Ponto Final de Ligação** e introduza o nome DNS público de um dos nós no cluster.  Por exemplo, `ec2-34-215-183-77.us-west-2.compute.amazonaws.com:19000`. Em Azure, um nome de domínio totalmente qualificado (FQDN) não é dado automaticamente, mas pode ser facilmente [definido na página de visão geral vm.](../virtual-machines/create-fqdn.md)

6. Abra o browser preferido e escreva o endereço do cluster (ponto final da ligação, esta aplicação implementa na porta 8080, por exemplo, ec2-34-215-183-77.us-west-2.compute.amazonaws.com:8080).

    ![Resposta da API a partir do Cluster](./media/service-fabric-tutorial-standalone-cluster/deployed-app.png)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a implementar uma aplicação no seu cluster:

> [!div class="checklist"]
> * Transferir a aplicação de exemplo
> * Implementar no cluster

Avance para a quarta parte da série para apagar o cluster.

> [!div class="nextstepaction"]
> [Limpar os seus recursos](service-fabric-tutorial-standalone-clean-up.md)