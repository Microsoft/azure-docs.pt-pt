---
title: Implementar uma aplicação para um cluster gerido por Tecido de Serviço via PowerShell (pré-visualização)
description: Neste tutorial, irá ligar-se a um cluster gerido por Tecido de Serviço e implementar uma aplicação via PowerShell.
ms.topic: tutorial
ms.date: 09/28/2020
ms.openlocfilehash: 36a91d2852bcda5f958441b48ef4721d6ccc83c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91410458"
---
# <a name="tutorial-deploy-an-app-to-a-service-fabric-managed-cluster-preview"></a>Tutorial: Implementar uma aplicação para um cluster gerido por Tecido de Serviço (pré-visualização)

Nesta série tutorial vamos discutir:

> [!div class="checklist"]
> * [Como implementar um cluster gerido por Tecido de Serviço](tutorial-managed-cluster-deploy.md)
> * [Como escalar um cluster gerido de tecido de serviço](tutorial-managed-cluster-scale.md)
> * [Como adicionar e remover os nódes num cluster gerido por Tecido de Serviço](tutorial-managed-cluster-add-remove-node-type.md)
> * Como implementar uma aplicação para um cluster gerido por Tecido de Serviço

Esta parte da série abrange como:

> [!div class="checklist"]
> * Ligue-se ao seu cluster gerido de tecido de serviço
> * Faça upload de uma aplicação para um cluster
> * Instantaneamente uma aplicação em um cluster
> * Remover uma aplicação de um cluster

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster gerido por tecido de serviço (ver [*Implementar um cluster gerido).*](tutorial-managed-cluster-deploy.md)

## <a name="connect-to-your-cluster"></a>Conecte-se ao seu cluster

Para se ligar ao seu cluster, vai precisar da impressão digital do certificado de cluster. Pode encontrar este valor na produção de propriedades de cluster da sua implementação de recursos ou consultando as propriedades do cluster num recurso existente.

O seguinte comando pode ser utilizado para consultar o seu recurso de cluster para a impressão digital do certificado de cluster.

```powershell
$serverThumbprint = (Get-AzResource -ResourceId /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ServiceFabric/managedclusters/mysfcluster).Properties.clusterCertificateThumbprint
```

Com a impressão digital do certificado de cluster, está pronto para se ligar ao seu cluster.

```powershell
$connectionEndpoint = "mysfcluster.eastus2.cloudapp.azure.com:19000"
Connect-ServiceFabricCluster -ConnectionEndpoint $connectionEndpoint -KeepAliveIntervalInSec 10 `
      -X509Credential `
      -ServerCertThumbprint $serverThumbprint  `
      -FindType FindByThumbprint `
      -FindValue $clientThumbprint `
      -StoreLocation CurrentUser `
      -StoreName My

```

### <a name="upload-an-application-package"></a>Faça upload de um pacote de aplicações

Neste tutorial, vamos utilizar a amostra de [Aplicação de Voto de Tecido de Serviço.](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/voting-sample-no-reverse-proxy) Para obter mais detalhes sobre a implementação da aplicação do Service Fabric através do PowerShell, consulte [o Tecido de Serviço para implementar e remover aplicações](service-fabric-deploy-remove-applications.md).

> [!NOTE]
> Na pré-visualização do cluster gerido do Service Fabric não poderá publicar aplicações diretamente do Visual Studio.

Primeiro terá de [embalar o pedido de implantação.](service-fabric-package-apps.md) Para este tutorial, siga os passos para embalar uma aplicação do Interior do Estúdio Visual. É importante tomar nota do caminho onde a aplicação foi embalada, uma vez que será utilizada para o caminho abaixo.

Uma vez criado o pacote de aplicações, pode enviar o pacote de aplicações para o seu cluster. Atualize o `$path` valor para representar o caminho onde o seu pacote de candidatura existe, e execute o seguinte:

```powershell
$path = "C:\Users\<user>\Documents\service-fabric-dotnet-quickstart\Voting\pkg\Debug"
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -CompressPackage
Register-ServiceFabricApplicationType -ApplicationPathInImageStore Debug
```

### <a name="create-an-application"></a>Criar uma aplicação

Pode instantaneamente uma aplicação a partir de qualquer versão do tipo de aplicação que tenha sido registada com sucesso utilizando o New-ServiceFabricApplication cmdlet. O nome de cada pedido deve começar pelo esquema "tecido:" e deve ser único para cada instância de aplicação. São também criados quaisquer serviços predefinidos definidos no manifesto de aplicação do tipo de aplicação-alvo.

```powershell
New-ServiceFabricApplication fabric:/Voting VotingType 1.0.0
```

Uma vez concluída esta operação, deverá ver as suas instâncias de aplicação a decorrer no Explorador de Tecidos de Serviço.

### <a name="remove-an-application"></a>Remover uma aplicação

Quando uma instância de aplicação já não é necessária, pode removê-la permanentemente pelo nome usando o `Remove-ServiceFabricApplication` cmdlet, que também remove automaticamente todos os serviços que pertencem à aplicação, removendo permanentemente todo o estado de serviço.

```powershell
Remove-ServiceFabricApplication fabric:/Voting
```

## <a name="next-steps"></a>Passos seguintes

Neste passo, implementamos uma app para um cluster gerido por Tecidos de Serviço. Para saber mais sobre clusters geridos pela Service Fabric, consulte:

> [!div class="nextstepaction"]
> [Clusters geridos de tecido de serviço frequentemente fazem perguntas](faq-managed-cluster.md)
