---
title: Implementar uma aplicação do Service Fabric num cluster no Azure | Microsoft Docs
description: Saiba como implementar uma aplicação num cluster a partir do Visual Studio.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/14/2019
ms.author: aljo,mikhegn
ms.custom: mvc
ms.openlocfilehash: 451cfde133955b987b97bc2447724d2e00010892
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61391405"
---
# <a name="tutorial-deploy-a-service-fabric-application-to-a-cluster-in-azure"></a>Tutorial: Implementar uma aplicação do Service Fabric num cluster no Azure

Este tutorial é a segunda parte de uma série. Mostra-lhe como implementar uma aplicação do Azure Service Fabric num novo cluster no Azure.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Crie um cluster.
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
* [Instale o Visual Studio 2017](https://www.visualstudio.com/) e as cargas de trabalho de **desenvolvimento no Azure** e **desenvolvimento na Web e em ASP.NET**.
* [Instale o SDK do Service Fabric](service-fabric-get-started.md).

## <a name="download-the-voting-sample-application"></a>Transferir o exemplo de aplicação de votação

Se não conseguiu criar a aplicação de votação de exemplo na [primeira parte desta série de tutoriais](service-fabric-tutorial-create-dotnet-app.md), pode transferi-la. Na janela de comando, execute o seguinte código para clonar o repositório da aplicação de exemplo no seu computador local.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart 
```

Abra a aplicação no Visual Studio, em execução como administrador e criar a aplicação.

## <a name="create-a-cluster"></a>Criar um cluster

Agora que a aplicação está pronta, pode criar um cluster do Service Fabric e, em seguida, implementar a aplicação no cluster. Um [cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-anywhere) é um conjunto ligado à rede de máquinas virtuais ou físicas, no qual os microsserviços são implementados e geridos.

Neste tutorial, crie um novo cluster de teste de três nós no IDE do Visual Studio e, em seguida, publicar a aplicação desse cluster. Consulte a [criar e gerir um tutorial de cluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) para obter informações sobre como criar um cluster de produção. Também pode implementar a aplicação a um cluster existente que criou anteriormente por meio dos [portal do Azure](https://portal.azure.com), utilizando [PowerShell](./scripts/service-fabric-powershell-create-secure-cluster-cert.md) ou [da CLI do Azure](./scripts/cli-create-cluster.md) scripts, ou a partir de um [Modelo azure Resource Manager](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

> [!NOTE]
> A aplicação de votação e muitas outras aplicações, utilizam o proxy inverso do Service Fabric para a comunicação entre serviços. Os clusters criados a partir do Visual Studio tem o proxy inverso ativado por predefinição. Se estiver a implementar um cluster existente, tem [ativar o proxy inverso no cluster](service-fabric-reverseproxy-setup.md) para a aplicação de voto trabalhar.


### <a name="find-the-votingweb-service-endpoint"></a>Localizar o ponto final de serviço do VotingWeb

O serviço web de front-end da aplicação Voting está à escuta numa porta específica (8080 se em seguiu os passos em [primeira parte desta série de tutoriais](service-fabric-tutorial-create-dotnet-app.md). Quando implementa a aplicação num cluster do Azure, tanto o cluster como a aplicação são executados atrás de um balanceador de carga do Azure. A porta da aplicação tem de ser aberta no balanceador de carga do Azure utilizando uma regra. A regra envia o tráfego de entrada através do Balanceador de carga para o serviço web. A porta encontra-se no ficheiro **VotingWeb/PackageRoot/ServiceManifest.xml** no elemento **Endpoint**. 

```xml
<Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
```

Tome nota do ponto final do serviço, que é necessária num passo posterior.  Se estiver a implementar um cluster existente, abra esta porta através da criação de uma regra e pesquisa de balanceamento de carga no balanceador de carga do Azure utilizando uma [script do PowerShell](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) ou através do Balanceador de carga para este cluster no [portal do Azure ](https://portal.azure.com).

### <a name="create-a-test-cluster-in-azure"></a>Criar um cluster de teste no Azure
No Explorador de Soluções, clique com o botão direito do rato em **Voting** e selecione **Publish** (Publicar).

Na **ponto final da ligação**, selecione **criar o novo Cluster**.  Se estiver a implementar um cluster existente, selecione o ponto de extremidade do cluster na lista.  Abre a caixa de diálogo Criar Cluster do Service Fabric.

Na **Cluster** separador, introduza o **nome do Cluster** (por exemplo, "mytestcluster"), selecione a sua subscrição, selecione uma região para o cluster (por exemplo, sul do Centro-Oeste), introduza o número de nós de cluster (nós Recomendamos três nós para um cluster de teste) e introduza um grupo de recursos (por exemplo, "mytestclustergroup"). Clique em **Seguinte**.

![Criar um cluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/create-cluster.png)

Na **certificado** separador, introduza o caminho de palavra-passe e de saída para o certificado de cluster. Um certificado autoassinado é criado como um ficheiro PFX e guardado para o caminho de saída especificado.  O certificado é utilizado para segurança de nó para nó e o nó de cliente.  Um certificado autoassinado não deve ser utilizado para clusters de produção.  Este certificado é utilizado pelo Visual Studio para autenticar com o cluster e implementar aplicações. Selecione **importar certificado** para instalar o PFX no arquivo de certificados CurrentUser\My do seu computador.  Clique em **Seguinte**.

![Criar um cluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/certificate.png)

Na **VM Detail** separador, introduza o **nome de utilizador** e **palavra-passe** para a conta de administrador de cluster.  Selecione o **imagem de Máquina Virtual** para os nós de cluster e o **tamanho da Máquina Virtual** para cada nó de cluster.  Clique nas **avançadas** separador.

![Criar um cluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/vm-detail.png)

Na **portas**, introduza o ponto de extremidade de serviço VotingWeb do passo anterior (por exemplo, 8080).  Quando o cluster é criado, estas portas de aplicação estão abertas no balanceador de carga do Azure para reencaminhar tráfego para o cluster.  Clique em **criar** para criar o cluster, o que demora vários minutos.

![Criar um cluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/advanced.png)

## <a name="publish-the-application-to-the-cluster"></a>Publicar a aplicação no cluster

Quando o novo cluster estiver pronto, pode implementar a aplicação de votação diretamente a partir do Visual Studio.

No Explorador de Soluções, clique com o botão direito do rato em **Voting** e selecione **Publish** (Publicar). É apresentada a caixa de diálogo **Publicar**.

Na **ponto final de ligação**, selecione o ponto final para o cluster que criou no passo anterior.  Por exemplo, "mytestcluster.southcentral.cloudapp.azure.com:19000". Se selecionou **parâmetros de ligação avançada**, as informações do certificado devem ser preenchido automaticamente.  
![Publicar uma aplicação do Service Fabric](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)

Selecione **Publicar**.

Assim que a aplicação é implementada, abra um browser e introduza o endereço do cluster seguido **: 8080**. Ou introduza outra porta, se estiver configurada uma. Um exemplo é `http://mytestcluster.southcentral.cloudapp.azure.com:8080`. Deverá ver a aplicação em execução no cluster no Azure. Na página Web de votação, experimente adicionar e eliminar as opções de votação e votar numa ou em várias destas opções.

![Exemplo de votação do Service Fabric](./media/service-fabric-tutorial-deploy-app-to-party-cluster/application-screenshot-new-azure.png)


## <a name="next-steps"></a>Passos Seguintes
Nesta parte do tutorial, ficou a saber como:

> [!div class="checklist"]
> * Crie um cluster.
> * Implementar uma aplicação num cluster remoto com o Visual Studio.

Avance para o tutorial seguinte:
> [!div class="nextstepaction"]
> [Ativar HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
