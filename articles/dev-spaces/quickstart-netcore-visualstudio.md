---
title: 'Debug e iterato em Kubernetes: Visual Studio & .NET Core'
services: azure-dev-spaces
ms.date: 11/13/2019
ms.topic: quickstart
description: Este quickstart mostra-lhe como usar Azure Dev Spaces e Visual Studio para depurar e iterar rapidamente uma aplicação .NET Core no Serviço Azure Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores, Helm, malha de serviço, encaminhamento de malha de serviço, kubectl, k8s
manager: gwallace
ms.custom: vs-azure
ms.workload: azure-vs
ms.openlocfilehash: 8279a32ece16209c1dd5bca13d08e22b283677ee
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87007009"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-visual-studio--net-core---azure-dev-spaces"></a>Quickstart: Debug and iterate on Kubernetes: Visual Studio & .NET Core - Azure Dev Spaces

Neste guia, vai aprender a:

- Configurar os Espaços de Programador do Azure com um cluster Kubernetes gerido no Azure.
- Utilizar o Visual Studio para desenvolver iterativamente código em contentores.
- Código de depuração em execução no seu cluster usando o Visual Studio.

A azure Dev Spaces também permite depurar e iterar usando:
- [Código de estúdio java e visual](quickstart-java.md)
- [CódigoNode.js e Estúdio Visual](quickstart-nodejs.md)
- [.NET Core e Visual Studio Code](quickstart-netcore.md)

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma, pode criar uma [conta gratuita.](https://azure.microsoft.com/free)
- Visual Studio 2019 no Windows com a carga de trabalho do Azure Development instalada. Se não tiver o Visual Studio instalado, descarregue-o [aqui.](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)
- A [CLI do Azure instalada](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Criar um cluster do Azure Kubernetes Service

Você precisa criar um cluster AKS em uma [região apoiada][supported-regions]. Os comandos abaixo criam um grupo de recursos chamado *MyResourceGroup* e um cluster AKS chamado *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Ativar espaços Azure Dev no seu cluster AKS

Utilize o `use-dev-spaces` comando para ativar os Espaços Dev no seu cluster AKS e siga as indicações. O comando abaixo permite espaços Dev no cluster *MyAKS* no grupo *MyResourceGroup* e cria um espaço dev *predefinido.*

> [!NOTE]
> O `use-dev-spaces` comando também instalará o CLI dos Espaços Azure Dev se ainda não estiver instalado. Não é possível instalar o Azure Dev Spaces CLI na Concha da Nuvem Azure.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

```output
'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="create-a-new-aspnet-web-app"></a>Criar uma nova aplicação web ASP.NET

1. Abra o Visual Studio.
1. Criar um projeto novo.
1. Escolha *ASP.NET Aplicação Web Core* e clique em *Seguinte*.
1. Nomeie o seu projeto *webfrontend* e clique em *Criar*.
1. Quando solicitado, escolha *a Aplicação Web (Model-View-Controller)* para o modelo.
1. Selecione *.NET Core* e *ASP.NET Core 2.1* no topo.
1. Clique em *Create* (Criar).

## <a name="connect-your-project-to-your-dev-space"></a>Ligue o seu projeto ao seu espaço dev

No seu projeto, selecione **Azure Dev Spaces** a partir das definições de lançamento dropdown como mostrado abaixo.

![Screenshot do Visual Studio UI com a opção IIS Express em destaque e selecionado e a opção Azure Dev Spaces em destaque.](media/get-started-netcore-visualstudio/LaunchSettings.png)

No diálogo Azure Dev Spaces, selecione a sua *Subscrição* e *o Cluster Azure Kubernetes*. Deixe *o Espaço* definido de forma *predefinido* e ative a caixa de verificação *acessível ao público.* Clique em *OK*.

![Screenshot da caixa de diálogo Azure Dev Spaces.](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

Este processo implementa o seu serviço no espaço dev *predefinido* com um URL acessível ao público. Se optar por um cluster que não tenha sido configurado para funcionar com Azure Dev Spaces, verá uma mensagem a perguntar se pretende configurá-lo. Clique em *OK*.

![Screenshot da caixa de diálogo de recursos Add Azure Spaces.](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

O URL público para o serviço em execução no espaço dev *predefinido* é apresentado na janela *saída:*

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

Service 'webfrontend' port 'http' is available at `http://default.webfrontend.1234567890abcdef1234.eus.azds.io/`
Service 'webfrontend' port 80 (http) is available at http://localhost:62266
Completed warmup for project 'webfrontend' in 125 seconds.
```

No exemplo acima, a URL pública é `http://default.webfrontend.1234567890abcdef1234.eus.azds.io/` . 

Selecione **Debug** e, em **seguida, Comece a depurar**. Após alguns segundos, o seu serviço será iniciado e o Visual Studio abrirá um navegador com o URL público do serviço. Se um navegador não abrir automaticamente, navegue para o URL público do seu serviço num browser e interaja com o serviço em execução no seu espaço dev.

Este processo pode ter desativado o acesso do público ao seu serviço. Para permitir o acesso do público, pode atualizar o valor de [entrada nos *valores.yaml*][ingress-update].

## <a name="update-code"></a>Atualizar código

Se o Visual Studio ainda estiver ligado ao seu espaço dev, clique no botão stop. Alterar a linha 20 `Controllers/HomeController.cs` para:
    
```csharp
ViewData["Message"] = "Your application description page in Azure.";
```

Guarde as suas alterações e selecione **Debug** e, em seguida, **Comece a depurar**. Após alguns segundos, o seu serviço será iniciado e o Visual Studio abrirá um navegador com o URL público do serviço. Se um navegador não abrir automaticamente, navegue no URL público do seu serviço num browser e clique em *Cerca de*. Observe que a sua mensagem atualizada aparece.

Em vez de reconstruir e redistribuir uma nova imagem de recipiente cada vez que são feitas edições de código, a Azure Dev Spaces recomplesca gradualmente o código dentro do recipiente existente para fornecer um loop de edição/depuração mais rápido.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Definição e utilização de pontos de rutura para depuração

Se o Visual Studio ainda estiver ligado ao seu espaço dev, clique no botão stop. Abra `Controllers/HomeController.cs` e clique em algum lugar na linha 20 para colocar o seu cursor lá. Para definir um breakpoint, clique em *Debug* e depois *F9* *Toggle Breakpoint*. Para iniciar o seu serviço no modo de depuragem no seu espaço dev, clique em *F5* ou clique em *Debug* e comece *a depurar*.

Abra o seu serviço num browser e note que não é apresentada nenhuma mensagem. O Regresso ao Visual Studio e a linha de observação 20 está em destaque. O ponto de rutura que definiu fez uma pausa no serviço na linha 20. Para retomar o serviço, clique em *F5* ou clique em *Debug* *e*continue . Volte ao seu navegador e note que a mensagem já está exibida.

Enquanto executa o seu serviço em Kubernetes com um depurador anexado, tem acesso total a informações de depurar, tais como a pilha de chamadas, variáveis locais e informações de exceção.

Retire o ponto de rutura colocando o cursor na linha 20 `Controllers/HomeController.cs` e atingindo *F9*.

## <a name="clean-up-your-azure-resources"></a>Limpe os seus recursos Azure

Navegue para o seu grupo de recursos no portal Azure e clique em *Eliminar grupo de recursos*. Em alternativa, pode utilizar o comando [az aks delete:](/cli/azure/aks#az-aks-delete)

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Working with multiple containers and team development](multi-service-netcore-visualstudio.md) (Trabalhar com vários contentores e o desenvolvimento em equipa)

[ingress-update]: how-dev-spaces-works-up.md#how-running-your-code-is-configured
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
