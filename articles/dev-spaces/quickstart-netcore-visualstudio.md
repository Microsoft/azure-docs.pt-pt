---
title: 'Debug and iterate on Kubernetes: Visual Studio & .NET Core'
services: azure-dev-spaces
ms.date: 11/13/2019
ms.topic: quickstart
description: Desenvolvimento rápido da Kubernetes com contentores e microsserviços no Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s
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
# <a name="quickstart-debug-and-iterate-on-kubernetes-visual-studio--net-core---azure-dev-spaces"></a>Quickstart: Debug and iterate on Kubernetes: Visual Studio & .NET Core - Azure Dev Spaces

Neste guia, vai aprender a:

- Configurar os Espaços de Programador do Azure com um cluster Kubernetes gerido no Azure.
- Utilizar o Visual Studio para desenvolver iterativamente código em contentores.
- Debug code running in your cluster using Visual Studio.

Azure Dev Spaces also allows you debug and iterate using:
- [Java and Visual Studio Code](quickstart-java.md)
- [Node.js and Visual Studio Code](quickstart-nodejs.md)
- [.NET Core and Visual Studio Code](quickstart-netcore.md)

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma, poderá [criar uma conta gratuita](https://azure.microsoft.com/free).
- Visual Studio 2019 on Windows with the Azure Development workload installed. You can also use Visual Studio 2017 on Windows with the Web Development workload and [Visual Studio Tools for Kubernetes](https://aka.ms/get-vsk8stools) installed. If you don't have Visual Studio installed, download it [here](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Create an Azure Kubernetes Service cluster

You must create an AKS cluster in a [supported region][supported-regions]. To create a cluster:

1. Inicie sessão no [portal do Azure](https://portal.azure.com)
1. Select *+ Create a resource > Kubernetes Service*. 
1. Enter the _Subscription_, _Resource Group_, _Kubernetes cluster name_, _Region_, _Kubernetes version_, and _DNS name prefix_.

    ![Create AKS in the Azure portal](media/get-started-netcore-visualstudio/create-aks-portal.png)

1. Clique em *Rever + criar*.
1. Clique em *Criar*.

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Enable Azure Dev Spaces on your AKS cluster

Navigate to your AKS cluster in the Azure portal and click *Dev Spaces*. Change *Use Dev Spaces* to *Yes* and click *Save*.

![Enable Dev Spaces in the Azure portal](media/get-started-netcore-visualstudio/enable-dev-spaces-portal.png)

## <a name="create-a-new-aspnet-web-app"></a>Create a new ASP.NET web app

1. Abra o Visual Studio.
1. Criar um projeto novo.
1. Choose *ASP.NET Core Web Application* and click *Next*.
1. Name your project *webfrontend* and click *Create*.
1. When prompted, choose *Web Application (Model-View-Controller)* for the template.
1. Select *.NET Core* and *ASP.NET Core 2.1* at the top.
1. Clique em *Criar*.

## <a name="connect-your-project-to-your-dev-space"></a>Connect your project to your dev space

In your project, select **Azure Dev Spaces** from the launch settings dropdown as shown below.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

In the Azure Dev Spaces dialog, select your *Subscription* and *Azure Kubernetes Cluster*. Leave *Space* set to *default* and enable the *Publicly Accessible* checkbox. Clique em *OK*.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

This process deploys your service to the *default* dev space with a publicly accessible URL. Se optar por um cluster que não tenha sido configurado para funcionar com Azure Dev Spaces, verá uma mensagem a perguntar se pretende configurá-lo. Clique em *OK*.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

The public URL for the service running in the *default* dev space is displayed in the *Output* window:

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

In the above example, the public URL is http://default.webfrontend.1234567890abcdef1234.eus.azds.io/. Navigate to your service's public URL and interact with the service running in your dev space.

This process may have disabled public access to your service. To enable public access, you can update the [ingress value in the *values.yaml*][ingress-update].

## <a name="update-code"></a>Atualizar código

If Visual Studio is still connected to your dev space, click the stop button. Change line 20 in `Controllers/HomeController.cs` to:
    
```csharp
ViewData["Message"] = "Your application description page in Azure.";
```

Save your changes and start your service using **Azure Dev Spaces** from the launch settings dropdown. Open the public URL of your service in a browser and click *About*. Observe that your updated message appears.

Instead of rebuilding and redeploying a new container image each time code edits are made, Azure Dev Spaces incrementally recompiles code within the existing container to provide a faster edit/debug loop.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Setting and using breakpoints for debugging

If Visual Studio is still connected to your dev space, click the stop button. Open `Controllers/HomeController.cs` and click somewhere on line 20 to put your cursor there. To set a breakpoint hit *F9* or click *Debug* then *Toggle Breakpoint*. To start your service in debugging mode in your dev space, hit *F5* or click *Debug* then *Start Debugging*.

Open your service in a browser and notice no message is displayed. Return to Visual Studio and observe line 20 is highlighted. The breakpoint you set has paused the service at line 20. To resume the service, hit *F5* or click *Debug* then *Continue*. Return to your browser and notice the message is now displayed.

While running your service in Kubernetes with a debugger attached, you have full access to debug information such as the call stack, local variables, and exception information.

Remove the breakpoint by putting your cursor on line 20 in `Controllers/HomeController.cs` and hitting *F9*.

## <a name="clean-up-your-azure-resources"></a>Clean up your Azure resources

Navigate to your resource group in the Azure portal and click *Delete resource group*. Alternatively, you can use the [az aks delete](/cli/azure/aks#az-aks-delete) command:

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Working with multiple containers and team development](multi-service-netcore-visualstudio.md) (Trabalhar com vários contentores e o desenvolvimento em equipa)

[ingress-update]: how-dev-spaces-works.md#how-running-your-code-is-configured
[supported-regions]: about.md#supported-regions-and-configurations
