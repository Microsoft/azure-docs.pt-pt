---
title: Tutorial – Depurar uma aplicação Web do Azure Service Fabric Mesh em execução no cluster de desenvolvimento local | Microsoft Docs
description: Neste tutorial, vai depurar uma aplicação do Azure Service Fabric Mesh em execução no seu cluster local.
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/31/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: bef86b189064a82b6605e8b99a374b1ee92682e2
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805125"
---
# <a name="tutorial-debug-a-service-fabric-mesh-application-running-in-your-local-development-cluster"></a>Tutorial: Depurar uma aplicação de malha de recursos de infraestrutura do serviço de mensagens em fila em execução no cluster de desenvolvimento local

Este tutorial é a segunda parte de uma série e mostra-lhe como criar e depurar uma aplicação do Azure Service Fabric Mesh no cluster de desenvolvimento local.

Neste tutorial irá aprender:

> [!div class="checklist"]
> * O que acontece quando cria uma aplicação do Azure Service Fabric Mesh
> * Como definir um ponto de interrupção para observar uma chamada serviço a serviço

Nesta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * [Criar uma aplicação do Service Fabric Mesh no Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * Depurar uma aplicação do Azure Service Fabric Mesh em execução no cluster de desenvolvimento local
> * [Implementar uma aplicação do Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [Atualizar uma aplicação do Service Fabric Mesh](service-fabric-mesh-tutorial-upgrade.md)
> * [Limpar os recursos do Service Fabric Mesh](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Certifique-se de que [configurou o ambiente de desenvolvimento](service-fabric-mesh-howto-setup-developer-environment-sdk.md), que inclui a instalação do runtime do Service Fabric, o SDK, o Docker e o Visual Studio 2017.

## <a name="download-the-to-do-sample-application"></a>Transferir a aplicação de tarefas de exemplo

Se não tiver criado o aplicativo de exemplo de tarefas pendentes na [primeira parte desta série de tutoriais](service-fabric-mesh-tutorial-create-dotnetcore.md), pode baixá-lo. Numa janela do comando, execute o seguinte comando para clonar o repositório da aplicação de exemplo para o seu computador local.

```
git clone https://github.com/azure-samples/service-fabric-mesh
```

A aplicação está no diretório `src\todolistapp`.

## <a name="build-and-debug-on-your-local-cluster"></a>Criar e depurar no seu cluster local

É automaticamente criada e implementada uma imagem do Docker no seu cluster local, assim que o projeto é carregado. Este processo pode demorar algum tempo. Para monitorizar o progresso no painel **Output** (Saída) do Visual Studio, defina o menu pendente **Show output from:** (Mostrar saída de) como **Service Fabric Tools** (Ferramentas do Service Fabric).

Prima **F5** para compilar e executar o serviço localmente. Quando o projeto é executado e depurado localmente, o Visual Studio irá:

* Garantir que o Docker para Windows está em execução e definir como utilizar o Windows como sistema operativo do contentor.
* Transferir quaisquer imagens base do Docker em falta. Esta parte pode demorar algum tempo
* Criar (ou recriar) a imagem do Docker utilizada para alojar o seu projeto de código.
* Implementar e executar o contentor no cluster de desenvolvimento local do Service Fabric.
* Executar os seus serviços e atingir os pontos de interrupção definidos.

Depois de a implementação local estar concluída e o Visual Studio estar a executar a aplicação, será aberta uma janela do browser numa página Web de exemplo predefinida.

## <a name="debugging-tips"></a>Dicas de depuração

Tornar a sua depuração primeiro executar (F5) muito mais rapidamente ao seguir as instruções em [desempenho de otimizar o Visual Studio](service-fabric-mesh-howto-optimize-vs.md).

Está a ocorrer um problema que faz com que a chamada para `using (HttpResponseMessage response = client.GetAsync("").GetAwaiter().GetResult())` falhe ligar ao serviço. Isto pode acontecer sempre que altera o seu endereço IP do anfitrião. Para resolver isto:

1. Remova a aplicação do cluster local (no Visual Studio, **Criar** > **Limpar Solução**).
2. No Gestor do Cluster Local do Service Fabric, selecione **Parar Cluster Local** e, em seguida, **Iniciar Cluster Local**.
3. Reimplementar a aplicação (no Visual Studio, **F5**).

Se receber o erro **Nenhum cluster local do Service Fabric em execução**, certifique-se de que o Service Fabric Local Cluster Manager (LCM) está em execução, clique com o botão direito do rato no ícone do LCM na barra de tarefas e, em seguida, clique em **Iniciar Cluster Local**. Depois de ser iniciado, regresse ao Visual Studio e prima **F5**.

Se receber um erro **404** quando a aplicação for iniciada, pode significar que as variáveis de ambiente em **service.yaml** estão incorretas. Certifique-se de que `ApiHostPort` e `ToDoServiceName` estão definidos corretamente de acordo com as instruções em [Criar variáveis de ambiente](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-tutorial-create-dotnetcore#create-environment-variables).

Se ocorrerem erros de compilação em **service.yaml**, certifique-se de que são utilizados espaços, não separadores, para avançar as linhas. Além disso, por agora, tem de criar a aplicação com o idioma inglês.

### <a name="debug-in-visual-studio"></a>Depurar no Visual Studio

Ao depurar uma aplicação de malha do Service Fabric no Visual Studio, está a utilizar um cluster de desenvolvimento do Service Fabric local. Para ver como os itens de tarefas são obtidos do serviço de back-end, faça a depuração para o método OnGet().
1. Na **WebFrontEnd** projeto aberto **páginas** > **Index. cshtml** > **Index.cshtml.cs** e defina um ponto de interrupção no **OnGet** método (linha 17).
2. Na **ToDoService** projeto aberto **TodoController.cs** e defina um ponto de interrupção no **obter** método (linha 15).
3. Regresse ao seu browser e atualize a página. Atingiu o ponto de interrupção no método de front-end da Web `OnGet()`. Pode inspecionar a variável `backendUrl` para ver como as variáveis de ambiente que definiu no ficheiro **service.yaml** são combinadas para o URL utilizado para contactar o serviço de back-end.
4. Ignore (F10) a chamada `client.GetAsync(backendUrl).GetAwaiter().GetResult())` e irá atingir o ponto de interrupção do controlador `Get()`. Neste método, pode ver como é obtida a lista de itens de tarefas a partir da lista na memória.
5. Quando tiver terminado, pare a depuração de seu projeto no Visual Studio ao premir **SHIFT+F5**.

## <a name="next-steps"></a>Passos Seguintes

Nesta parte do tutorial, ficou a saber como:

> [!div class="checklist"]
> * O que acontece quando cria uma aplicação do Azure Service Fabric Mesh
> * Como definir um ponto de interrupção para observar uma chamada serviço a serviço

Avance para o tutorial seguinte:
> [!div class="nextstepaction"]
> [Implementar uma aplicação do Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
