---
title: 'Depurar e iterar em kubernetes: Visual Studio & .NET Core'
services: azure-dev-spaces
ms.date: 11/13/2019
ms.topic: quickstart
description: Desenvolvimento rápido do Kubernetes com contentores e microsserviços no Azure
keywords: Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, K8S
manager: gwallace
ms.custom: vs-azure
ms.workload: azure-vs
ms.openlocfilehash: a151314bef14e302879f4db0f7c0094779bdcfec
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325612"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-visual-studio--net-core---azure-dev-spaces"></a>Início rápido: Depurar e iterar em kubernetes: Visual Studio & .NET Core-Azure Dev Spaces

Neste guia, vai aprender a:

- Configurar os Espaços de Programador do Azure com um cluster Kubernetes gerido no Azure.
- Utilizar o Visual Studio para desenvolver iterativamente código em contentores.
- Depurar código em execução no cluster usando o Visual Studio.

Azure Dev Spaces também permite depurar e iterar usando:
- [Java e Visual Studio Code](quickstart-java.md)
- [Node. js e Visual Studio Code](quickstart-nodejs.md)
- [.NET Core e Visual Studio Code](quickstart-netcore.md)

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma, poderá [criar uma conta gratuita](https://azure.microsoft.com/free).
- Visual Studio 2019 no Windows com a carga de trabalho de desenvolvimento do Azure instalada. Você também pode usar o Visual Studio 2017 no Windows com a carga de trabalho de desenvolvimento para a Web e [Ferramentas do Visual Studio para kubernetes](https://aka.ms/get-vsk8stools) instalado. Se você não tiver o Visual Studio instalado, baixe-o [aqui](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Criar um cluster do serviço kubernetes do Azure

Você deve criar um cluster AKS em uma [região com suporte][supported-regions]. Para criar um cluster:

1. Inicie sessão no [portal do Azure](https://portal.azure.com)
1. Selecione *+ criar um recurso > serviço kubernetes*. 
1. Insira a _assinatura_, o _grupo de recursos_, o nome do _cluster kubernetes_, a _região_, a _versão do kubernetes_e o prefixo do _nome DNS_.

    ![Criar AKS no portal do Azure](media/get-started-netcore-visualstudio/create-aks-portal.png)

1. Clique em *Rever + criar*.
1. Clique em *Criar*.

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Habilitar Azure Dev Spaces em seu cluster AKS

Navegue até o cluster AKS na portal do Azure e clique em *espaços de desenvolvimento*. Altere *usar espaços de desenvolvimento* para *Sim* e clique em *salvar*.

![Habilitar espaços de desenvolvimento no portal do Azure](media/get-started-netcore-visualstudio/enable-dev-spaces-portal.png)

## <a name="create-a-new-aspnet-web-app"></a>Criar um novo aplicativo Web ASP.NET

1. Abra o Visual Studio.
1. Criar um projeto novo.
1. Escolha *ASP.NET Core aplicativo Web* e clique em *Avançar*.
1. Nomeie o seu projeto de *WebFrontEnd* e clique em *criar*.
1. Quando solicitado, escolha *aplicativo Web (Model-View-Controller)* para o modelo.
1. Selecione *.NET Core* e *ASP.NET Core 2,1* na parte superior.
1. Clique em *Criar*.

## <a name="connect-your-project-to-your-dev-space"></a>Conectar seu projeto ao seu espaço de desenvolvimento

Em seu projeto, selecione **Azure dev Spaces** na lista suspensa configurações de inicialização, conforme mostrado abaixo.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

Na caixa de diálogo Azure Dev Spaces, selecione sua *assinatura* e o *cluster kubernetes do Azure*. Deixe *espaço* definido como *padrão* e habilite a caixa de seleção *publicamente acessível* . Clique em *OK*.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

Esse processo implanta o serviço no espaço de desenvolvimento *padrão* com uma URL acessível publicamente. Se optar por um cluster que não tenha sido configurado para funcionar com Azure Dev Spaces, verá uma mensagem a perguntar se pretende configurá-lo. Clique em *OK*.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

A URL pública para o serviço em execução no espaço de desenvolvimento *padrão* é exibida na janela de *saída* :

```cmd
Starting warmup for project 'webfrontend'.
Waiting for namespace to be provisioned.
Using dev space 'default' with target 'MyAKS'
...
Successfully built 1234567890ab
Successfully tagged webfrontend:devspaces-11122233344455566
Built container image in 39s
Waiting for container...
36s

Service 'webfrontend' port 'http' is available at http://default.webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (http) is available at http://localhost:62266
Completed warmup for project 'webfrontend' in 125 seconds.
```

No exemplo acima, a URL pública é http://default.webfrontend.1234567890abcdef1234.eus.azds.io/. Navegue até a URL pública do serviço e interaja com o serviço em execução no seu espaço de desenvolvimento.

Esse processo pode ter desabilitado o acesso público ao seu serviço. Para habilitar o acesso público, você pode atualizar o [valor de entrada nos *valores. YAML*][ingress-update].

## <a name="update-code"></a>Atualizar código

Se o Visual Studio ainda estiver conectado ao seu espaço de desenvolvimento, clique no botão parar. Altere a linha 20 em `Controllers/HomeController.cs` para:
    
```csharp
ViewData["Message"] = "Your application description page in Azure.";
```

Salve as alterações e inicie o serviço usando **Azure dev Spaces** na lista suspensa configurações de inicialização. Abra a URL pública do seu serviço em um navegador e clique em *sobre*. Observe que a mensagem atualizada é exibida.

Em vez de recompilar e reimplantar uma nova imagem de contêiner cada vez que são feitas edições de código, Azure Dev Spaces recompila incrementalmente o código dentro do contêiner existente para fornecer um loop de edição/depuração mais rápido.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Configurando e usando pontos de interrupção para depuração

Se o Visual Studio ainda estiver conectado ao seu espaço de desenvolvimento, clique no botão parar. Abra `Controllers/HomeController.cs` e clique em algum lugar na linha 20 para colocar o cursor lá. Para definir um ponto de interrupção, pressione *F9* ou clique em *depurar* e *alternar ponto de interrupção*. Para iniciar o serviço no modo de depuração em seu espaço de desenvolvimento, pressione *F5* ou clique em *depurar* e *Iniciar Depuração*.

Abra seu serviço em um navegador e observe que nenhuma mensagem é exibida. Retorne ao Visual Studio e observe que a linha 20 está realçada. O ponto de interrupção que você definiu pausou o serviço na linha 20. Para retomar o serviço, pressione *F5* ou clique em *depurar* e *continuar*. Retorne ao seu navegador e observe que a mensagem agora é exibida.

Ao executar o serviço no kubernetes com um depurador anexado, você tem acesso completo para depurar informações como a pilha de chamadas, variáveis locais e informações de exceção.

Remova o ponto de interrupção, colocando o cursor na linha 20 em `Controllers/HomeController.cs` e pressionando *F9*.

## <a name="clean-up-your-azure-resources"></a>Limpar os recursos do Azure

Navegue até o grupo de recursos na portal do Azure e clique em *excluir grupo de recursos*. Como alternativa, você pode usar o comando [AZ AKs Delete](/cli/azure/aks#az-aks-delete) :

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Working with multiple containers and team development](multi-service-netcore-visualstudio.md) (Trabalhar com vários contentores e o desenvolvimento em equipa)

[ingress-update]: how-dev-spaces-works.md#how-running-your-code-is-configured
[supported-regions]: about.md#supported-regions-and-configurations
