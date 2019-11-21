---
title: Implementar uma aplicação do Service Fabric num cluster no Azure | Microsoft Docs
description: Saiba como implementar uma aplicação num cluster a partir do Visual Studio.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/22/2019
ms.author: mikhegn
ms.custom: mvc
ms.openlocfilehash: 37c27ae71eddcb5a35b9baeae250bee232c7acb7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74213204"
---
# <a name="tutorial-deploy-a-service-fabric-application-to-a-cluster-in-azure"></a>Tutorial: Implementar uma aplicação do Service Fabric num cluster no Azure

Este tutorial é a segunda parte de uma série. Mostra-lhe como implementar uma aplicação do Azure Service Fabric num novo cluster no Azure.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Create a cluster.
> * Implementar uma aplicação num cluster remoto com o Visual Studio.

Nesta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * [Criar uma aplicação .NET do Service Fabric](service-fabric-tutorial-create-dotnet-app.md).
> * Implementar a aplicação num cluster remoto.
> * [Adicionar um ponto final HTTPS a um serviço de front-end ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md).
> * [Configurar CI/CD com o Azure Pipelines](service-fabric-tutorial-deploy-app-with-cicd-vsts.md).
> * [Configurar a monitorização e os diagnósticos da aplicação](service-fabric-tutorial-monitoring-aspnet.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Install Visual Studio 2019](https://www.visualstudio.com/), and install the **Azure development** and **ASP.NET and web development** workloads.
* [Instale o SDK do Service Fabric](service-fabric-get-started.md).

> [!NOTE]
> A free account may not meet the requirements to create a virtual machine. This will prevent the completion of the tutorial. In addition, a non-work or non-school account may encounter permission issues while creating the certificate on the keyvault associated with the cluster. If you experience an error related to certificate creation use the Portal to create the cluster instead. 

## <a name="download-the-voting-sample-application"></a>Transferir o exemplo de aplicação de votação

Se não conseguiu criar a aplicação de votação de exemplo na [primeira parte desta série de tutoriais](service-fabric-tutorial-create-dotnet-app.md), pode transferi-la. In a command window, run the following code to clone the sample application repository to your local machine.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart 
```

Open the application in Visual Studio, running as administrator, and build the application.

## <a name="create-a-cluster"></a>Criar um cluster

Now that the application is ready, you create a Service Fabric cluster and then deploy the application to the cluster. Um [cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-anywhere) é um conjunto ligado à rede de máquinas virtuais ou físicas, no qual os microsserviços são implementados e geridos.

In this tutorial, you create a new three node test cluster in the Visual Studio IDE and then publish the application to that cluster. See the [Create and manage a cluster tutorial](service-fabric-tutorial-create-vnet-and-windows-cluster.md) for information on creating a production cluster. You can also deploy the application to an existing cluster that you previously created through the [Azure portal](https://portal.azure.com), by using [PowerShell](./scripts/service-fabric-powershell-create-secure-cluster-cert.md) or [Azure CLI](./scripts/cli-create-cluster.md) scripts, or from an [Azure Resource Manager template](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

> [!NOTE]
> The Voting application, and many other applications, use the Service Fabric reverse proxy to communicate between services. Clusters created from Visual Studio have the reverse proxy enabled by default. If you're deploying to an existing cluster, you must [enable the reverse proxy in the cluster](service-fabric-reverseproxy-setup.md) for the Voting application to work.


### <a name="find-the-votingweb-service-endpoint"></a>Localizar o ponto final de serviço do VotingWeb

The front-end web service of the Voting application is listening on a specific port (8080 if you in followed the steps in [part one of this tutorial series](service-fabric-tutorial-create-dotnet-app.md). Quando implementa a aplicação num cluster do Azure, tanto o cluster como a aplicação são executados atrás de um balanceador de carga do Azure. The application port must be opened in the Azure load balancer by using a rule. The rule sends inbound traffic through the load balancer to the web service. A porta encontra-se no ficheiro **VotingWeb/PackageRoot/ServiceManifest.xml** no elemento **Endpoint**. 

```xml
<Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
```

Take note of the service endpoint, which is needed in a later step.  If you're deploying to an existing cluster, open this port by creating a load-balancing rule and probe in the Azure load balancer using a [PowerShell script](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) or via the load balancer for this cluster in the [Azure portal](https://portal.azure.com).

### <a name="create-a-test-cluster-in-azure"></a>Create a test cluster in Azure
No Explorador de Soluções, clique com o botão direito do rato em **Voting** e selecione **Publish** (Publicar).

In **Connection Endpoint**, select **Create New Cluster**.  If you're deploying to an existing cluster, select the cluster endpoint from the list.  The Create Service Fabric Cluster dialog opens.

In the **Cluster** tab, enter the **Cluster name** (for example, "mytestcluster"), select your subscription, select a region for the cluster (such as South Central US), enter the number of cluster nodes (we recommend three nodes for a test cluster), and enter a resource group (such as "mytestclustergroup"). Clique em **Seguinte**.

![Criar um cluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/create-cluster.png)

In the **Certificate** tab, enter the password and output path for the cluster certificate. A self-signed certificate is created as a PFX file and saved to the specified output path.  The certificate is used for both node-to-node and client-to-node security.  Don't use a self-signed certificate for production clusters.  This certificate is used by Visual Studio to authenticate with the cluster and deploy applications. Select **Import certificate** to install the PFX in the CurrentUser\My certificate store of your computer.  Clique em **Seguinte**.

![Criar um cluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/certificate.png)

In the **VM Detail** tab, enter the **User name** and **Password** for the cluster admin account.  Select the **Virtual machine image** for the cluster nodes and the **Virtual machine size** for each cluster node.  Click the **Advanced** tab.

![Criar um cluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/vm-detail.png)

In **Ports**, enter the VotingWeb service endpoint from the previous step (for example, 8080).  When the cluster is created, these application ports are opened in the Azure load balancer to forward traffic to the cluster.  Click **Create** to create the cluster, which takes several minutes.

![Criar um cluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/advanced.png)

## <a name="publish-the-application-to-the-cluster"></a>Publish the application to the cluster

When the new cluster is ready, you can deploy the Voting application directly from Visual Studio.

No Explorador de Soluções, clique com o botão direito do rato em **Voting** e selecione **Publish** (Publicar). É apresentada a caixa de diálogo **Publicar**.

In **Connection Endpoint**, select the endpoint for the cluster you created in the previous step.  For example, "mytestcluster.southcentral.cloudapp.azure.com:19000". If you select **Advanced Connection Parameters**, the certificate information should be auto-filled.  
![Publish a Service Fabric application](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)

Selecione **Publicar**.

Once the application is deployed, open a browser and enter the cluster address followed by **:8080**. Ou introduza outra porta, se estiver configurada uma. Um exemplo é `http://mytestcluster.southcentral.cloudapp.azure.com:8080`. Deverá ver a aplicação em execução no cluster no Azure. Na página Web de votação, experimente adicionar e eliminar as opções de votação e votar numa ou em várias destas opções.

![Exemplo de votação do Service Fabric](./media/service-fabric-tutorial-deploy-app-to-party-cluster/application-screenshot-new-azure.png)


## <a name="next-steps"></a>Passos seguintes
Nesta parte do tutorial, ficou a saber como:

> [!div class="checklist"]
> * Create a cluster.
> * Implementar uma aplicação num cluster remoto com o Visual Studio.

Avance para o tutorial seguinte:
> [!div class="nextstepaction"]
> [Ativar HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
