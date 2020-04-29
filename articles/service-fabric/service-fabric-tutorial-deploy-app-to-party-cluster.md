---
title: Implementar uma aplicação de Tecido de Serviço para um cluster em Azure
description: Saiba como implementar uma aplicação existente para um cluster de Tecido de Serviço Azure recém-criado do Visual Studio.
author: athinanthny
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: mikhegn
ms.custom: mvc
ms.openlocfilehash: 9951610732cbb1c5884a7b7e830033f427db0ab1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75646012"
---
# <a name="tutorial-deploy-a-service-fabric-application-to-a-cluster-in-azure"></a>Tutorial: Implementar uma aplicação do Service Fabric num cluster no Azure

Este tutorial é a segunda parte de uma série. Mostra-lhe como implementar uma aplicação do Azure Service Fabric num novo cluster no Azure.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Criar um aglomerado.
> * Implementar uma aplicação num cluster remoto com o Visual Studio.

Nesta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * [Construa uma aplicação .NET Service Fabric](service-fabric-tutorial-create-dotnet-app.md).
> * Implementar a aplicação num cluster remoto.
> * [Adicione um ponto final HTTPS a um ASP.NET serviço frontal Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md).
> * [Configurar CI/CD com o Azure Pipelines](service-fabric-tutorial-deploy-app-with-cicd-vsts.md).
> * [Configurar a monitorização e](service-fabric-tutorial-monitoring-aspnet.md)o diagnóstico para a aplicação .

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se não tiver uma subscrição Azure, crie uma [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Instale o Visual Studio 2019](https://www.visualstudio.com/)e **instale** as cargas de trabalho de desenvolvimento e desenvolvimento ASP.NET e **web.**
* [Instale o SDK do Service Fabric](service-fabric-get-started.md).

> [!NOTE]
> Uma conta gratuita pode não satisfazer os requisitos para criar uma máquina virtual. Isto impedirá a conclusão do tutorial. Além disso, uma conta não-laboral ou não escolar pode encontrar problemas de permissão enquanto cria o certificado no cofre associado ao cluster. Se experimentar um erro relacionado com a criação de certificados, utilize o Portal para criar o cluster. 

## <a name="download-the-voting-sample-application"></a>Transferir o exemplo de aplicação de votação

Se não conseguiu criar a aplicação de votação de exemplo na [primeira parte desta série de tutoriais](service-fabric-tutorial-create-dotnet-app.md), pode transferi-la. Numa janela de comando, execute o seguinte código para clonar o repositório de aplicação da amostra à sua máquina local.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart 
```

Abra a aplicação no Visual Studio, funcionando como administrador, e construa a aplicação.

## <a name="create-a-cluster"></a>Criar um cluster

Agora que a aplicação está pronta, cria um cluster de Tecido de Serviço e, em seguida, implanta a aplicação para o cluster. Um [cluster de tecido](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-anywhere) de serviço é um conjunto de máquinas virtuais ou físicas ligadas à rede, nas quais os seus microserviços são implantados e geridos.

Neste tutorial, você cria um novo cluster de teste de três nós no Estúdio Visual IDE e, em seguida, publica a aplicação para esse cluster. Consulte a [Create e gerencie um tutorial](service-fabric-tutorial-create-vnet-and-windows-cluster.md) de cluster para obter informações sobre a criação de um cluster de produção. Também pode implementar a aplicação para um cluster existente que criou anteriormente através do [portal Azure,](https://portal.azure.com)utilizando scripts [PowerShell](./scripts/service-fabric-powershell-create-secure-cluster-cert.md) ou [Azure CLI,](./scripts/cli-create-cluster.md) ou a partir de um modelo de Gestor de [Recursos Azure.](service-fabric-tutorial-create-vnet-and-windows-cluster.md)

> [!NOTE]
> A aplicação de voto, e muitas outras aplicações, utilizam o Serviço Fabric de procuração inversa para comunicar entre serviços. Os clusters criados a partir do Visual Studio têm o proxy inverso ativado por padrão. Se estiver a implantar-se num cluster existente, deve [ativar o proxy inverso no cluster](service-fabric-reverseproxy-setup.md) para que a aplicação de voto funcione.


### <a name="find-the-votingweb-service-endpoint"></a>Localizar o ponto final de serviço do VotingWeb

O serviço web frontal da aplicação De voto está a ouvir numa porta específica (8080 se seguir os passos na [parte um desta série tutorial](service-fabric-tutorial-create-dotnet-app.md). Quando implementa a aplicação num cluster do Azure, tanto o cluster como a aplicação são executados atrás de um balanceador de carga do Azure. A porta de aplicação deve ser aberta no equilibrador de carga Azure utilizando uma regra. A regra envia tráfego de entrada através do equilibrador de carga para o serviço web. A porta encontra-se no ficheiro **VotingWeb/PackageRoot/ServiceManifest.xml** no elemento **Endpoint**. 

```xml
<Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
```

Tome nota do ponto final do serviço, que é necessário num passo posterior.  Se estiver a implantar para um cluster existente, abra esta porta criando uma regra de equilíbrio de carga e sonda no equilíbrio de carga Azure utilizando um [script PowerShell](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) ou através do equilibrador de carga para este cluster no [portal Azure](https://portal.azure.com).

### <a name="create-a-test-cluster-in-azure"></a>Criar um cluster de teste em Azure
No Explorador de Soluções, clique com o botão direito do rato em **Voting** e selecione **Publish** (Publicar).

No **ponto final de ligação,** selecione Criar novo **cluster**.  Se estiver a implantar para um cluster existente, selecione o ponto final do cluster da lista.  Abre-se o diálogo Create Fabric Cluster.

No separador **Cluster,** introduza o **nome Cluster** (por exemplo, "mytestcluster"), selecione a sua subscrição, selecione uma região para o cluster (como o Centro Sul dos EUA), introduza o número de nós de cluster (recomendamos três nós para um cluster de teste) e insira um grupo de recursos (como "mytestcluster"). Clique em **Seguinte**.

![Criar um cluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/create-cluster.png)

No separador **Certificado,** introduza a palavra-passe e o caminho de saída para o certificado de cluster. Um certificado auto-assinado é criado como um ficheiro PFX e guardado para o caminho de saída especificado.  O certificado é utilizado tanto para a segurança do nó ao nó como para a segurança cliente-a-nó.  Não use um certificado auto-assinado para aglomerados de produção.  Este certificado é utilizado pela Visual Studio para autenticar com o cluster e implementar aplicações. Selecione **certificado de importação** para instalar o PFX no CurrentUser\My certificate store do seu computador.  Clique em **Seguinte**.

![Criar um cluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/certificate.png)

No **separador De pormenor VM,** introduza o nome de **utilizador** e **a palavra-passe** para a conta de administração do cluster.  Selecione a imagem da **máquina Virtual** para os nós do cluster e o tamanho da **máquina Virtual** para cada nó de cluster.  Clique no separador **Avançado.**

![Criar um cluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/vm-detail.png)

Nas **Portas,** introduza o ponto final do serviço VotingWeb a partir do escalão anterior (por exemplo, 8080).  Quando o cluster é criado, estas portas de aplicação são abertas no equilibrador de carga Azure para encaminhar o tráfego para o cluster.  Clique em **Criar** para criar o cluster, que leva vários minutos.

![Criar um cluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/advanced.png)

## <a name="publish-the-application-to-the-cluster"></a>Publicar a aplicação ao cluster

Quando o novo cluster estiver pronto, pode implementar a aplicação De Voto diretamente do Estúdio Visual.

No Explorador de Soluções, clique com o botão direito do rato em **Voting** e selecione **Publish** (Publicar). É apresentada a caixa de diálogo **Publicar**.

No Ponto final de **Ligação,** selecione o ponto final para o cluster que criou no passo anterior.  Por exemplo, "mytestcluster.southcentral.cloudapp.azure.com:19000". Se selecionar Parâmetros de **Ligação Avançada,** as informações do certificado devem ser preenchidas automaticamente.  
![Publicar uma aplicação do Service Fabric](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)

Selecione **Publicar**.

Uma vez implementada a aplicação, abra um browser e introduza o endereço do cluster seguido de **:8080**. Ou introduza outra porta, se estiver configurada uma. Um exemplo é `http://mytestcluster.southcentral.cloudapp.azure.com:8080`. Deverá ver a aplicação em execução no cluster no Azure. Na página Web de votação, experimente adicionar e eliminar as opções de votação e votar numa ou em várias destas opções.

![Exemplo de votação do Service Fabric](./media/service-fabric-tutorial-deploy-app-to-party-cluster/application-screenshot-new-azure.png)


## <a name="next-steps"></a>Passos seguintes
Nesta parte do tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar um aglomerado.
> * Implementar uma aplicação num cluster remoto com o Visual Studio.

Avance para o tutorial seguinte:
> [!div class="nextstepaction"]
> [Ativar HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
