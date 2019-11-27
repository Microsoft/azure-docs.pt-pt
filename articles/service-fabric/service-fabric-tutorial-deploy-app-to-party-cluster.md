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
* [Instale o Visual Studio 2019](https://www.visualstudio.com/)e instale as cargas de trabalho de desenvolvimento e **ASP.net e** desenvolvimento para a Web **do Azure** .
* [Instale o SDK do Service Fabric](service-fabric-get-started.md).

> [!NOTE]
> Uma conta gratuita pode não atender aos requisitos para criar uma máquina virtual. Isso impedirá a conclusão do tutorial. Além disso, uma conta não corporativa ou não de estudante pode encontrar problemas de permissão ao criar o certificado no keyvault associado ao cluster. Se você tiver um erro relacionado à criação de certificado, use o portal para criar o cluster. 

## <a name="download-the-voting-sample-application"></a>Transferir o exemplo de aplicação de votação

Se não conseguiu criar a aplicação de votação de exemplo na [primeira parte desta série de tutoriais](service-fabric-tutorial-create-dotnet-app.md), pode transferi-la. Em uma janela de comando, execute o código a seguir para clonar o repositório de aplicativos de exemplo em seu computador local.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart 
```

Abra o aplicativo no Visual Studio, executando como administrador e compile o aplicativo.

## <a name="create-a-cluster"></a>Criar um cluster

Agora que o aplicativo está pronto, você cria um Cluster Service Fabric e, em seguida, implanta o aplicativo no cluster. Um [cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-anywhere) é um conjunto ligado à rede de máquinas virtuais ou físicas, no qual os microsserviços são implementados e geridos.

Neste tutorial, você cria um novo cluster de teste de três nós no IDE do Visual Studio e, em seguida, publica o aplicativo nesse cluster. Consulte o [tutorial criar e gerenciar um cluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) para obter informações sobre como criar um cluster de produção. Você também pode implantar o aplicativo em um cluster existente que você criou anteriormente por meio do [portal do Azure](https://portal.azure.com), usando o [PowerShell](./scripts/service-fabric-powershell-create-secure-cluster-cert.md) ou scripts de [CLI do Azure](./scripts/cli-create-cluster.md) ou de um [modelo de Azure Resource Manager](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

> [!NOTE]
> O aplicativo de votação e muitos outros aplicativos usam o Service Fabric proxy reverso para se comunicar entre os serviços. Os clusters criados a partir do Visual Studio têm o proxy reverso habilitado por padrão. Se estiver implantando em um cluster existente, você deverá [habilitar o proxy reverso no cluster](service-fabric-reverseproxy-setup.md) para que o aplicativo de votação funcione.


### <a name="find-the-votingweb-service-endpoint"></a>Localizar o ponto final de serviço do VotingWeb

O serviço Web front-end do aplicativo de votação está escutando em uma porta específica (8080 se você seguiu as etapas na [parte um desta série de tutoriais](service-fabric-tutorial-create-dotnet-app.md). Quando implementa a aplicação num cluster do Azure, tanto o cluster como a aplicação são executados atrás de um balanceador de carga do Azure. A porta do aplicativo deve ser aberta no Azure Load Balancer usando uma regra. A regra envia o tráfego de entrada por meio do balanceador de carga para o serviço Web. A porta encontra-se no ficheiro **VotingWeb/PackageRoot/ServiceManifest.xml** no elemento **Endpoint**. 

```xml
<Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
```

Anote o ponto de extremidade do serviço, que é necessário em uma etapa posterior.  Se você estiver implantando em um cluster existente, abra essa porta criando uma regra de balanceamento de carga e investigação no balanceador de carga do Azure usando um [script do PowerShell](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) ou por meio do balanceador de carga para esse cluster no [portal do Azure](https://portal.azure.com).

### <a name="create-a-test-cluster-in-azure"></a>Criar um cluster de teste no Azure
No Explorador de Soluções, clique com o botão direito do rato em **Voting** e selecione **Publish** (Publicar).

Em **ponto de extremidade de conexão**, selecione **criar novo cluster**.  Se você estiver implantando em um cluster existente, selecione o ponto de extremidade do cluster na lista.  A caixa de diálogo Criar Service Fabric cluster é aberta.

Na guia **cluster** , insira o **nome do cluster** (por exemplo, "mytestcluster"), selecione sua assinatura, selecione uma região para o cluster (como EUA Central do Sul), insira o número de nós de cluster (recomendamos três nós para um cluster de teste) e insira um grupo de recursos (como "mytestclustergroup"). Clique em **Seguinte**.

![Criar um cluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/create-cluster.png)

Na guia **certificado** , insira a senha e o caminho de saída para o certificado do cluster. Um certificado autoassinado é criado como um arquivo PFX e salvo no caminho de saída especificado.  O certificado é usado para segurança de nó para nó e de cliente para nó.  Não use um certificado autoassinado para clusters de produção.  Esse certificado é usado pelo Visual Studio para se autenticar com o cluster e implantar aplicativos. Selecione **importar certificado** para instalar o pfx no repositório de certificados do CurrentUser\My do seu computador.  Clique em **Seguinte**.

![Criar um cluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/certificate.png)

Na guia **detalhes da VM** , insira o **nome de usuário** e a **senha** para a conta de administrador do cluster.  Selecione a **imagem de máquina virtual** para os nós de cluster e o **tamanho da máquina virtual** para cada nó de cluster.  Clique na guia **avançado** .

![Criar um cluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/vm-detail.png)

Em **portas**, insira o ponto de extremidade do serviço VotingWeb da etapa anterior (por exemplo, 8080).  Quando o cluster é criado, essas portas de aplicativo são abertas no balanceador de carga do Azure para encaminhar o tráfego para o cluster.  Clique em **criar** para criar o cluster, o que leva vários minutos.

![Criar um cluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/advanced.png)

## <a name="publish-the-application-to-the-cluster"></a>Publicar o aplicativo no cluster

Quando o novo cluster estiver pronto, você poderá implantar o aplicativo de votação diretamente do Visual Studio.

No Explorador de Soluções, clique com o botão direito do rato em **Voting** e selecione **Publish** (Publicar). É apresentada a caixa de diálogo **Publicar**.

Em **ponto de extremidade de conexão**, selecione o ponto de extremidade para o cluster que você criou na etapa anterior.  Por exemplo, "mytestcluster.southcentral.cloudapp.azure.com:19000". Se você selecionar **parâmetros de conexão avançados**, as informações do certificado deverão ser preenchidas automaticamente.  
![publicar um aplicativo Service Fabric](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)

Selecione **Publicar**.

Depois que o aplicativo for implantado, abra um navegador e insira o endereço do cluster seguido por **: 8080**. Ou introduza outra porta, se estiver configurada uma. Um exemplo é `http://mytestcluster.southcentral.cloudapp.azure.com:8080`. Deverá ver a aplicação em execução no cluster no Azure. Na página Web de votação, experimente adicionar e eliminar as opções de votação e votar numa ou em várias destas opções.

![Exemplo de votação do Service Fabric](./media/service-fabric-tutorial-deploy-app-to-party-cluster/application-screenshot-new-azure.png)


## <a name="next-steps"></a>Passos seguintes
Nesta parte do tutorial, ficou a saber como:

> [!div class="checklist"]
> * Crie um cluster.
> * Implementar uma aplicação num cluster remoto com o Visual Studio.

Avance para o tutorial seguinte:
> [!div class="nextstepaction"]
> [Ativar HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
