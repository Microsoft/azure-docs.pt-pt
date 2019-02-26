---
title: Criar um espaço de programador do Kubernetes na cloud | Microsoft Docs
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
ms.subservice: azds-kubernetes
ms.author: zarhoads
ms.date: 07/09/2018
ms.topic: quickstart
description: Desenvolvimento rápido da Kubernetes com contentores e microsserviços no Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores
ms.openlocfilehash: 89fd64be0ea7b5b02ed446f2ee6d4c6a9c69dd0e
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56819689"
---
# <a name="quickstart-create-a-kubernetes-dev-space-with-azure-dev-spaces-net-core-and-visual-studio"></a>Início rápido: Criar um espaço de desenvolvimento de Kubernetes com espaços de desenvolvimento do Azure (.NET Core e o Visual Studio)

Neste guia, vai aprender a:

- Configurar os Espaços de Programador do Azure com um cluster Kubernetes gerido no Azure.
- Utilizar o Visual Studio para desenvolver iterativamente código em contentores.
- Depurar o código em execução no cluster.

> [!Note]
> **Se ficar bloqueado** em qualquer altura, veja a secção [Resolução de problemas](troubleshooting.md) ou publique um comentário nesta página. Também pode consultar o [tutorial](get-started-netcore-visualstudio.md) mais detalhado.

## <a name="prerequisites"></a>Pré-requisitos

- Um cluster do Kubernetes em execução no Kubernetes 1.9.6 ou posterior, na região EastUS, Eualeste2, CentralUS, WestUS2, WestEurope, SoutheastAsia, CanadaCentral ou CanadaEast.

- Visual Studio 2017 com a carga de trabalho de Desenvolvimento Web instalada. Se não a tiver instalado, transfira-a [aqui](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

## <a name="set-up-azure-dev-spaces"></a>Configurar os Espaços de Programador do Azure

Instale as [Ferramentas do Visual Studio para Kubernetes](https://aka.ms/get-vsk8stools).

## <a name="connect-to-a-cluster"></a>Ligar a um cluster

Em seguida, vai criar e configurar um projeto para Espaços de Programador do Azure.

### <a name="create-an-aspnet-web-app"></a>Criar uma aplicação Web ASP.NET

No Visual Studio 2017, crie um projeto novo. Atualmente, o projeto tem de ser uma **aplicação Web ASP.NET Core**. Atribua o nome **webfrontend** ao projeto.

Selecione o modelo **Aplicação Web (Modelo-Vista-Controlador)** e verifique se está a apontar para **.NET Core** e **ASP.NET Core 2.0**.

### <a name="enable-dev-spaces-for-an-aks-cluster"></a>Ativar os Espaços de Programador para um cluster do AKS

Com o projeto que acabou de criar, selecione **Azure Dev Spaces** no menu pendente de definições de início, conforme mostrado abaixo.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

Na caixa de diálogo que é apresentada, confirme que tem sessão iniciada com a conta adequada e, em seguida, selecione um cluster existente.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

Por agora, mantenha o `default` no menu pendente **Espaço**. Selecione a caixa de verificação **Publicly Accessible** (Acessível Publicamente) para que a aplicação Web seja acessível a partir de um ponto final público.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog2.png)

Clique em **OK** para selecionar ou criar o cluster.

Se optar por um cluster que não tenha sido configurado para funcionar com Azure Dev Spaces, verá uma mensagem a perguntar se pretende configurá-lo.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

Escolha **OK**. 

### <a name="look-at-the-files-added-to-project"></a>Ver os ficheiros adicionados ao projeto
Enquanto aguarda pela criação do espaço de programador, veja os ficheiros que foram adicionados ao projeto quando optou por utilizar os Espaços de Programador do Azure.

- Pode ver que foi adicionada uma pasta com o nome `charts` e que, dentro desta, foi estruturado um [gráfico do Helm](https://docs.helm.sh) para a sua aplicação. Estes ficheiros são utilizados para implementar a aplicação no espaço de programador.
- `Dockerfile` tem as informações necessárias para empacotar a aplicação no formato padrão do Docker.
- `azds.yaml` contém a configuração em tempo de desenvolvimento que é exigida pelo espaço de programador.

![](media/get-started-netcore-visualstudio/ProjectFiles.png)

## <a name="debug-a-container-in-kubernetes"></a>Depurar um contentor no Kubernetes
Assim que o ambiente de programador estiver criado, pode depurar a aplicação. Defina um ponto de interrupção no código, por exemplo, na linha 20 do ficheiro `HomeController.cs` no qual está definida a variável `Message`. Clique em **F5** para iniciar a depuração. 

O Visual Studio vai comunicar com o espaço de programador para criar e implementar a aplicação e, em seguida, vai abrir um browser com a aplicação Web em execução. Poderá parecer que o contentor está a ser executado localmente. Contudo, na verdade, está a ser executado no espaço de programador no Azure. O motivo para o endereço localhost deve-se ao facto de o Azure Dev Spaces criar um túnel SSH temporário para o contentor que está a ser executado no AKS.

Clique na ligação **About** (Acerca de) na parte superior da página para acionar o ponto de interrupção. Tem acesso total às informações de depuração, tal como aconteceria se o código estivesse a ser executado localmente, como, por exemplo, a pilha de chamadas, as variáveis locais, informações de exceção, etc.


## <a name="iteratively-develop-code"></a>Desenvolver código de forma iterativa

O Azure Dev Spaces não se limita apenas a pôr o código em execução no Kubernetes. Tem que ver com permitir-lhe ver, de forma rápida e iterativa, as alterações ao código serem aplicadas num ambiente do Kubernetes na cloud.

### <a name="update-a-content-file"></a>Atualizar um ficheiro de conteúdo
1. Localize o ficheiro `./Views/Home/Index.cshtml` e faça uma edição ao HTML. Por exemplo, altere a linha 70 que lê `<h2>Application uses</h2>` para algo semelhante a `<h2>Hello k8s in Azure!</h2>`
1. Guarde o ficheiro.
1. Aceda ao seu browser e atualize a página. Deverá ver a página Web mostrar o código HTML atualizado.

O que aconteceu? As edições aos ficheiros de conteúdos, como HTML e CSS, não requerem a recompilação numa aplicação Web .NET Core, pelo que uma sessão F5 ativa sincroniza automaticamente qualquer ficheiro de conteúdos modificado com o contentor em execução no AKS, de modo a que possa ver as edições aos conteúdos de imediato.

### <a name="update-a-code-file"></a>Atualizar um ficheiro de código
A atualização de ficheiros de código exige mais algum trabalho, porque a aplicação .NET Core tem de ser recompilada e produzir binários de aplicação atualizados.

1. Pare o depurador no Visual Studio.
1. Abra o ficheiro de código com o nome `Controllers/HomeController.cs` e edite a mensagem que a página About (Sobre) vai apresentar: `ViewData["Message"] = "Your application description page.";`
1. Guarde o ficheiro.
1. Prima **F5** para iniciar novamente a depuração. 

Em vez de reconstruir e reimplementar uma imagem de contentor nova sempre que forem feitas edições ao código, o que, muitas vezes, irá demorar um tempo considerável, o Azure Dev Spaces recompilará incrementalmente o código dentro do contentor existente para proporcionar um ciclo de edição/depuração mais rápido.

Atualize a aplicação Web no browser e aceda à página About (Sobre). Deverá ver a mensagem personalizada apresentada na IU.


## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Working with multiple containers and team development](multi-service-netcore-visualstudio.md) (Trabalhar com vários contentores e o desenvolvimento em equipa)
