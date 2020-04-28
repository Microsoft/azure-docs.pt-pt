---
title: 'Debug e iterado em Kubernetes: Visual Studio & .NET Core'
services: azure-dev-spaces
ms.date: 11/13/2019
ms.topic: quickstart
description: Este quickstart mostra-lhe como usar os Espaços Azure Dev e o Estúdio Visual para depurar e iterar rapidamente uma aplicação .NET Core no Serviço Azure Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores, Helm, malha de serviço, encaminhamento de malha de serviço, kubectl, k8s
manager: gwallace
ms.custom: vs-azure
ms.workload: azure-vs
ms.openlocfilehash: e6fa490285582c827b8704496fb7a20a7eb9cfbc
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "82166024"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-visual-studio--net-core---azure-dev-spaces"></a>Quickstart: Debug e iterate em Kubernetes: Visual Studio & .NET Core - Azure Dev Spaces

Neste guia, vai aprender a:

- Configurar os Espaços de Programador do Azure com um cluster Kubernetes gerido no Azure.
- Utilizar o Visual Studio para desenvolver iterativamente código em contentores.
- Depurar código em execução no seu cluster usando o Estúdio Visual.

Os Espaços Azure Dev também lhe permitem depurar e iterar usando:
- [Código do Estúdio Java e Visual](quickstart-java.md)
- [Node.js e Código de Estúdio Visual](quickstart-nodejs.md)
- [.NET Core e Visual Studio Code](quickstart-netcore.md)

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma, pode criar uma [conta gratuita.](https://azure.microsoft.com/free)
- Visual Studio 2019 no Windows com a carga de trabalho do Desenvolvimento Azure instalada. Também pode utilizar o Visual Studio 2017 no Windows com a carga de trabalho de Desenvolvimento Web e [ferramentas de estúdio visual para Kubernetes instaladas.](https://aka.ms/get-vsk8stools) Se não tiver o Visual Studio instalado, descarregue-o [aqui.](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)

## <a name="create-an-azure-kubernetes-service-cluster"></a>Criar um cluster de serviço Azure Kubernetes

É preciso criar um aglomerado AKS numa [região apoiada.][supported-regions] Para criar um cluster:

1. Inscreva-se no [portal Azure](https://portal.azure.com)
1. Selecione *+ Crie um recurso > Serviço Kubernetes*. 
1. Introduza o nome de _subscrição_, _Grupo de Recursos,_ nome de _cluster Kubernetes,_ _versão Região,_ _Kubernetes_e _prefixo de nome DNS_.

    ![Criar AKS no portal Azure](media/get-started-netcore-visualstudio/create-aks-portal.png)

1. Clique em *Rever + criar*.
1. Clique em *Criar*.

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Enable Azure Dev Spaces no seu cluster AKS

Navegue para o seu cluster AKS no portal Azure e clique em *Espaços Dev*. Mude *use espaços de dev* para *sim* e clique em *Guardar*.

![Ativar espaços de dev no portal Azure](media/get-started-netcore-visualstudio/enable-dev-spaces-portal.png)

## <a name="create-a-new-aspnet-web-app"></a>Criar uma nova ASP.NET aplicação web

1. Abra o Visual Studio.
1. Criar um projeto novo.
1. Escolha *ASP.NET Aplicação Web Core* e clique em *Seguinte*.
1. Nomeie o seu *webfrontend* do seu projeto e clique *em Criar*.
1. Quando solicitado, escolha *a Aplicação Web (Model-View-Controller)* para o modelo.
1. Selecione *.NET Core* e *ASP.NET Core 2.1* na parte superior.
1. Clique em *Criar*.

## <a name="connect-your-project-to-your-dev-space"></a>Ligue o seu projeto ao seu espaço de dev

No seu projeto, selecione **Azure Dev Spaces** a partir das definições de lançamento, como mostrado abaixo.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

No diálogo Azure Dev Spaces, selecione a sua *Subscrição* e *o Cluster Azure Kubernetes*. Deixe o *Espaço* definido *para defeito* e ative a caixa de verificação *acessível ao público.* Clique em *OK*.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

Este processo implementa o seu serviço para o espaço de v *padrão* com um URL acessível ao público. Se optar por um cluster que não tenha sido configurado para funcionar com Azure Dev Spaces, verá uma mensagem a perguntar se pretende configurá-lo. Clique em *OK*.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

O URL público para o serviço em execução no espaço de v *predefinido* é apresentado na janela *Saída:*

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

No exemplo acima, o `http://default.webfrontend.1234567890abcdef1234.eus.azds.io/`URL público é . 

Selecione **Debug** e **comece a depurar**. Após alguns segundos, o seu serviço começará e o Visual Studio abrirá um navegador com o URL público do serviço. Se um navegador não abrir automaticamente, navegue para o URL público do seu serviço num browser e interaja com o serviço que funciona no seu espaço de dev.

Este processo pode ter desativado o acesso público ao seu serviço. Para permitir o acesso do público, pode atualizar o valor de [ingresso nos *valores.yaml*][ingress-update].

## <a name="update-code"></a>Atualizar código

Se o Visual Studio ainda estiver ligado ao seu espaço de dev, clique no botão stop. Alterar `Controllers/HomeController.cs` a linha 20 para:
    
```csharp
ViewData["Message"] = "Your application description page in Azure.";
```

Guarde as suas alterações e selecione **Debug** **e comece a depurar**. Após alguns segundos, o seu serviço começará e o Visual Studio abrirá um navegador com o URL público do serviço. Se um navegador não abrir automaticamente, navegue o URL público do seu serviço num browser e clique em *About*. Observe que a sua mensagem atualizada aparece.

Em vez de reconstruir e reimplantar uma nova imagem de recipiente cada vez que são feitas edições de código, a Azure Dev Spaces recompila gradualmente o código dentro do recipiente existente para fornecer um ciclo de edição/depuração mais rápido.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Definição e utilização de pontos de rutura para depuração

Se o Visual Studio ainda estiver ligado ao seu espaço de dev, clique no botão stop. Abra `Controllers/HomeController.cs` e clique em algum lugar na linha 20 para colocar o cursor lá. Para definir um ponto de *rutura,* clique em *Debug* e *toggle Breakpoint*. Para iniciar o seu serviço no modo de depuração no seu espaço de v, clique em *F5* ou clique em *Debug* e, em seguida, *inicie depuração*.

Abra o seu serviço num browser e note que não é apresentada nenhuma mensagem. O regresso ao Estúdio Visual e a linha 20 de observação estão em destaque. O ponto de rutura que definiu fez uma pausa no serviço na linha 20. Para retomar o serviço, clique em *F5* ou clique em *Debug* *e*continue . Volte ao seu navegador e note que a mensagem está agora apresentada.

Enquanto executa o seu serviço em Kubernetes com um debugger anexado, você tem acesso total a informações dedepuração tais como a pilha de chamadas, variáveis locais e informações de exceção.

Retire o ponto de rutura colocando o `Controllers/HomeController.cs` cursor na linha 20 e batendo *em F9*.

## <a name="clean-up-your-azure-resources"></a>Limpe os seus recursos Azure

Navegue para o seu grupo de recursos no portal Azure e clique em *Eliminar o grupo de recursos*. Em alternativa, pode utilizar o comando [az aks eliminar:](/cli/azure/aks#az-aks-delete)

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Working with multiple containers and team development](multi-service-netcore-visualstudio.md) (Trabalhar com vários contentores e o desenvolvimento em equipa)

[ingress-update]: how-dev-spaces-works-up.md#how-running-your-code-is-configured
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
