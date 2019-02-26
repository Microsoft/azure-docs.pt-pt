---
title: Implementar uma aplicação .NET num contentor no Azure Service Fabric | Microsoft Docs
description: Saiba como colocar uma aplicação .NET existente num contentor com o Visual Studio e depurar contentores no Service Fabric localmente. A aplicação em contentor é enviada para um registo de contentor do Azure e implementada num cluster do Service Fabric. Quando implementada no Azure, a aplicação utiliza a BD SQL do Azure para manter os dados.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/18/2018
ms.author: aljo
ms.openlocfilehash: bd92ac2a36fdb15741799571d3ceff7fbbb36eef
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56807529"
---
# <a name="tutorial-deploy-a-net-application-in-a-windows-container-to-azure-service-fabric"></a>Tutorial: Implementar uma aplicação .NET num contentor do Windows no Azure Service Fabric

Este tutorial explica como colocar uma aplicação ASP.NET existente num contentor e empacotá-la como uma aplicação do Service Fabric.  Execute os contentores localmente no cluster de desenvolvimento do Service Fabric e, em seguida, implemente a aplicação no Azure.  A aplicação mantém os dados na [Base de Dados SQL do Azure](/azure/sql-database/sql-database-technical-overview). 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Colocar uma aplicação existente num contentor com o Visual Studio
> * Criar uma SQL Database do Azure
> * Criar um registo de contentor do Azure
> * Implementar uma aplicação do Service Fabric no Azure

## <a name="prerequisites"></a>Pré-requisitos

1. Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
2. Instale o [Docker CE para Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description), para que possam executar contentores no Windows 10.
3. Instale o [runtime do Service Fabric, versão 6.2 ou posterior](service-fabric-get-started.md) e o [SDK do Service Fabric, versão 3.1](service-fabric-get-started.md) ou posterior.
4. Instale o [Visual Studio 2017, versão 15.7](https://www.visualstudio.com/) ou posterior com as cargas de trabalho de **desenvolvimento do Azure** e de **desenvolvimento Web e em ASP.NET**.
5. Instale o [Azure PowerShell][link-azure-powershell-install]
 

## <a name="download-and-run-fabrikam-fiber-callcenter"></a>Transferir e executar o Fabrikam Fiber CallCenter
Transfira a aplicação de exemplo [Fabrikam Fiber CallCenter][link-fabrikam-github].  Clique na ligação **transferir arquivo**.  A partir do diretório *sourceCode* no ficheiro *fabrikam.zip*, extraia o ficheiro *sourceCode.zip* e, em seguida, extraia o diretório *VS2015* para o seu computador.

Certifique-se de que a aplicação Fabrikam Fiber CallCenter é compilada e executada sem erros.  Inicie o Visual Studio como **administrador** e abra o ficheiro [FabrikamFiber.CallCenter.sln][link-fabrikam-github].  Prima F5 para depurar e executar a aplicação.

![Exemplo de Fabrikam Web][fabrikam-web-page]

## <a name="containerize-the-application"></a>Colocar a aplicação num contentor
Clique com o botão direito do rato em **FabrikamFiber.Web** projeto > **Adicionar** > **Suporte de Container Orchestrator**.  Selecione **Service Fabric** como o orquestrador de contentores e clique em **OK**.

Clique em **Sim** para mudar agora dos contentores do Docker para os do Windows.

É criado um novo projeto da aplicação Service Fabric **FabrikamFiber.CallCenterApplication** na solução.  Um Dockerfile é adicionado ao projeto **FabrikamFiber.Web** existente.  Um diretório **PackageRoot** também é adicionado ao projeto **FabrikamFiber.Web**, que contém o manifesto do serviço e as definições para o novo serviço FabrikamFiber.Web. 

O contentor está agora pronto para ser criado e empacotado numa aplicação do Service Fabric. Assim que tiver a imagem de contentor incorporada no seu computador, pode enviá-la para qualquer registo de contentor e pedir a qualquer anfitrião para executar.

## <a name="create-an-azure-sql-db"></a>Criar uma BD SQL do Azure
Quando executa a aplicação Fabrikam Fiber CallCenter em produção, os dados têm de ser mantidos numa base de dados. Não existe atualmente nenhuma forma de garantir que os dados fiquem num contentor, por isso não pode armazenar dados de produção no SQL Server num contentor.

Recomendamos a [Base de Dados SQL do Azure](/azure/sql-database/sql-database-get-started-powershell). Para configurar e executar uma Base de Dados SQL Server gerida no Azure, execute o seguinte script.  Modifique as variáveis do script conforme necessário. *clientIP* é o endereço IP do seu computador de desenvolvimento. Tome nota do nome do servidor produzido pelo script. 

```powershell
$subscriptionID="<subscription ID>"

# Sign in to your Azure account and select your subscription.
Login-AzureRmAccount -SubscriptionId $subscriptionID 

# The data center and resource name for your resources.
$dbresourcegroupname = "fabrikam-fiber-db-group"
$location = "southcentralus"

# The logical server name: Use a random value or replace with your own value (do not capitalize).
$servername = "fab-fiber-$(Get-Random)"

# Set an admin login and password for your database.
# The login information for the server.
$adminlogin = "ServerAdmin"
$password = "Password@123"

# The IP address of your development computer that accesses the SQL DB.
$clientIP = "<client IP>"

# The database name.
$databasename = "call-center-db"

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $dbresourcegroupname -Location $location

# Create the SQL server.
New-AzureRmSqlServer -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

# Create the firewall rule to allow your development computer to access the server.
New-AzureRmSqlServerFirewallRule -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowClient" -StartIpAddress $clientIP -EndIpAddress $clientIP

# Creeate the database in the server.
New-AzureRmSqlDatabase  -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -RequestedServiceObjectiveName "S0"

Write-Host "Server name is $servername"
```
> [!TIP]
> Se estiver protegido por uma firewall de empresa, o endereço IP do seu computador de desenvolvimento não pode ser exposto à Internet. Para verificar que a base de dados tem o endereço IP correto para a regra de firewall, vá para o [portal do Azure](https://portal.azure.com) e encontre a sua base de dados na secção Bases de Dados SQL. Clique no respetivo nome e, em seguida, na secção Descrição geral, clique em "Definir firewall do servidor". O "endereço IP do cliente" é o endereço IP do seu computador de desenvolvimento. Certifique-se de que corresponde ao endereço IP na regra "AllowClient".

## <a name="update-the-web-config"></a>Atualizar a configuração da Web
Volte para o projeto **FabrikamFiber.Web**, atualize a cadeia de ligação no ficheiro **web.config**, para que encaminhe para o SQL Server no contentor.  Atualização do *servidor* parte da cadeia de ligação para ser o nome de servidor criado pelo script anterior. Deve ser algo como "fab-fiber-751718376.database.windows.net".

```xml
<add name="FabrikamFiber-Express" connectionString="Server=<server name>,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
<add name="FabrikamFiber-DataWarehouse" connectionString="Server=<server name>,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
  
```
>[!NOTE]
>Pode utilizar qualquer SQL Server que preferir para depuração local, desde que seja acessível a partir do seu anfitrião. No entanto, o **localdb** não suporta comunicação `container -> host`. Se pretender utilizar uma base de dados SQL diferente ao criar uma versão de compilação da aplicação Web, adicione outra cadeia de ligação ao seu ficheiro *web.release.config*.

## <a name="run-the-containerized-application-locally"></a>Executar a aplicação em contentor localmente
Prima **F5** para executar e depurar a aplicação num contentor no cluster de desenvolvimento do Service Fabric local. Clique em **Sim** se for apresentada uma caixa de mensagem a pedir para conceder permissões de leitura e execução do grupo “ServiceFabricAllowedUsers” ao diretório do projeto do Visual Studio.

## <a name="create-a-container-registry"></a>Criar um registo de contentores
Agora que a aplicação é executada localmente, comece a preparar a implementação do Azure.  As imagens de contentor têm de ser armazenadas num registo de contentor.  Crie um [registo de contentor do Azure](/azure/container-registry/container-registry-intro) com o script seguinte. O nome de registo do contentor é visível para as outras subscrições do Azure, pelo que tem de ser exclusivo.
Antes de implementar a aplicação no Azure, coloque a imagem de contentor neste registo.  Quando a aplicação é implementada no cluster no Azure, a imagem de contentor é retirada deste registo.

```powershell
# Variables
$acrresourcegroupname = "fabrikam-acr-group"
$location = "southcentralus"
$registryname="fabrikamregistry$(Get-Random)"

New-AzureRmResourceGroup -Name $acrresourcegroupname -Location $location

$registry = New-AzureRMContainerRegistry -ResourceGroupName $acrresourcegroupname -Name $registryname -EnableAdminUser -Sku Basic
```

## <a name="create-a-service-fabric-cluster-on-azure"></a>Criar um cluster do Service Fabric no Azure
As aplicações do Service Fabric são executadas num cluster, um conjunto de máquinas virtuais ou físicas ligadas à rede.  Antes de poder implementar a aplicação no Azure, crie um cluster do Service Fabric no Azure.

Pode:
- Criar um cluster de teste a partir do Visual Studio. Esta opção permite-lhe criar um cluster seguro diretamente a partir do Visual Studio com as suas configurações preferidas. 
- [Criar um cluster seguro a partir de um modelo](service-fabric-tutorial-create-vnet-and-windows-cluster.md)

Este tutorial cria um cluster a partir do Visual Studio, que é ideal para cenários de teste. Se criar um cluster de outra forma ou utilizar um cluster existente, pode copiar e colar o ponto final de ligação ou escolhê-lo a partir da sua subscrição. 

Antes de Fabrikamfiber inicial, abra -> PackageRoot -> servicemanifest. XML no Explorador de soluções. Tome nota da porta para o front-end da web listada na **ponto final**. 

Ao criar o cluster, 

1. Clique com o botão direito do rato no projeto de aplicação **FabrikamFiber.CallCenterApplication** no Explorador de Soluções e escolha **Publicar**.

2. Inicie sessão com a sua conta do Azure para poder aceder às suas subscrições. 

3. Selecione a lista pendente de **Ponto Final de Ligação** e selecione a opção **Criar Novo Cluster...**.    
        
4. Na caixa de diálogo **Criar cluster**, modifique as seguintes definições:

    a. Especifique o nome do cluster no campo **Nome do Cluster**, bem como a subscrição e a localização que pretende utilizar. Tome nota do nome do seu grupo de recursos do cluster.

    b. Opcional: Pode modificar o número de nós. Por predefinição, tem três nós, o mínimo exigido para testar cenários do Service Fabric.

    c. Selecione o separador **Certificado**. Neste separador, escreva uma palavra-passe a utilizar para proteger o certificado do seu cluster. Este certificado ajuda a tornar o seu cluster seguro. Também pode modificar o caminho para o local onde pretende guardar o certificado. O Visual Studio também pode importar o certificado por si, uma vez que este é um passo obrigatório para publicar a aplicação no cluster.

    d. Selecione o separador **Detalhes da VM**. Especifique a palavra-passe que pretende utilizar para as máquinas virtuais (VM) que compõem o cluster. O nome de utilizador e a palavra-passe podem ser utilizados para ligar remotamente às VMs. Também tem de selecionar um tamanho de VM e pode alterar a imagem da VM, se necessário. 

    > [!IMPORTANT]
    >Escolha um SKU que suporte contentores em execução. O SO do Windows Server nos nós do cluster tem de ser compatível com o SO do Windows Server do seu contentor. Para obter mais informações, veja [Compatibilidade do sistema operativo do contentor do Windows Server e do sistema operativo do sistema anfitrião ](service-fabric-get-started-containers.md#windows-server-container-os-and-host-os-compatibility). Por predefinição, este tutorial cria uma imagem do Docker com base no Windows Server 2016 LTSC. Os contentores com base nesta imagem serão executados nos clusters criados com o Windows Server 2016 Datacenter com Contentores. No entanto, se criar um cluster ou utilizar um cluster existente com base no Windows Server Datacenter Core 1709 com Contentores, terá de alterar a imagem do SO do Windows Server em que se baseia o contentor. Abra o **Dockerfile** no projeto **FabrikamFiber.Web**, comente a instrução `FROM` existente (com base em `windowsservercore-ltsc`) e anule os comentários na instrução `FROM` baseada em `windowsservercore-1709`. 

    e. No separador **Avançadas**, indique a porta de aplicação a abrir no balanceador de carga quando o cluster é implementado. Esta é a porta que tomaram nota de antes de começar a criar o cluster. Também pode adicionar uma chave do Application Insights existente para ser utilizada no encaminhamento dos ficheiros de registo da aplicação.

    f. Quando terminar de modificar as definições, selecione o botão **Criar**. 
1. A criação demora vários minutos a ser concluída; a janela de saída indicará quando o cluster estiver totalmente criado.
    

## <a name="allow-your-application-running-in-azure-to-access-the-sql-db"></a>Permitir que a aplicação em execução no Azure aceda à BD SQL
Anteriormente, criou uma regra de firewall de SQL para dar acesso à sua aplicação em execução localmente.  Em seguida, precisa de ativar a aplicação em execução no Azure para aceder à BD SQL.  Crie um [ponto final de serviço de rede virtual](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview) para o cluster do Service Fabric e, em seguida, crie uma regra para permitir que esse ponto final aceda à BD SQL. Certifique-se de que especifique a variável de grupo de recursos de cluster que tirou nota ao criar o cluster. 

```powershell
# Create a virtual network service endpoint
$clusterresourcegroup = "<cluster resource group>"
$resource = Get-AzureRmResource -ResourceGroupName $clusterresourcegroup -ResourceType Microsoft.Network/virtualNetworks | Select-Object -first 1
$vnetName = $resource.Name

Write-Host 'Virtual network name: ' $vnetName 

# Get the virtual network by name.
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName $clusterresourcegroup `
  -Name              $vnetName

Write-Host "Get the subnet in the virtual network:"

# Get the subnet, assume the first subnet contains the Service Fabric cluster.
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet | Select-Object -first 1

$subnetName = $subnet.Name
$subnetID = $subnet.Id
$addressPrefix = $subnet.AddressPrefix

Write-Host "Subnet name: " $subnetName " Address prefix: " $addressPrefix " ID: " $subnetID

# Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.
$vnet = Set-AzureRmVirtualNetworkSubnetConfig `
  -Name            $subnetName `
  -AddressPrefix   $addressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint Microsoft.Sql | Set-AzureRmVirtualNetwork

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

# Add a SQL DB firewall rule for the virtual network service endpoint
$subnet = Get-AzureRmVirtualNetworkSubnetConfig `
  -Name           $subnetName `
  -VirtualNetwork $vnet;

$VNetRuleName="ServiceFabricClusterVNetRule"
$vnetRuleObject1 = New-AzureRmSqlServerVirtualNetworkRule `
  -ResourceGroupName      $dbresourcegroupname `
  -ServerName             $servername `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnetID;
```
## <a name="deploy-the-application-to-azure"></a>Implementar a aplicação no Azure
Agora que a aplicação está pronta, pode implementá-la no cluster diretamente no Azure a partir do Visual Studio.  No Explorador de Soluções, clique com o botão direito do rato no projeto de aplicação **FabrikamFiber.CallCenterApplication** e selecione **Publicar**.  Em **Ponto Final de Ligação**, selecione o ponto final do cluster que criou anteriormente.  Em **Azure Container Registry**, selecione o registo de contentor que criou anteriormente.  Clique em **Publicar** para implementar a aplicação no cluster no Azure.

![Publicar a aplicação][publish-app]

Siga o progresso da implementação na janela de saída.  Quando a aplicação for implementada, abra um browser e escreva o endereço do cluster e a porta da aplicação. Por exemplo, http://fabrikamfibercallcenter.southcentralus.cloudapp.azure.com:8659/.

![Exemplo de Fabrikam Web][fabrikam-web-page-deployed]

## <a name="set-up-continuous-integration-and-deployment-cicd-with-a-service-fabric-cluster"></a>Configurar a Integração e a Implementação Contínua (CI/CD) com um cluster do Service Fabric
Para saber como utilizar o Azure DevOps para configurar a implementação de aplicação de CI/CD para um cluster do Service Fabric, veja [Tutorial: Implementar uma aplicação com CI/CD para um cluster do Service Fabric](service-fabric-tutorial-deploy-app-with-cicd-vsts.md). O processo descrito no tutorial é o mesmo para este projeto (FabrikamFiber), basta ignorar a transferência do exemplo de Voto e substituir o FabrikamFiber pelo nome do repositório, em vez de Voto.

## <a name="clean-up-resources"></a>Limpar recursos
Quando terminar, lembre-se de remover todos os recursos que criou.  A forma mais simples de fazê-lo consiste em remover os grupos de recursos que contêm o cluster do Service Fabric, a BD SQL do Azure e o Azure Container Registry.

```powershell
$dbresourcegroupname = "fabrikam-fiber-db-group"
$acrresourcegroupname = "fabrikam-acr-group"
$clusterresourcegroupname="fabrikamcallcentergroup"

# Remove the Azure SQL DB
Remove-AzureRmResourceGroup -Name $dbresourcegroupname

# Remove the container registry
Remove-AzureRmResourceGroup -Name $acrresourcegroupname

# Remove the Service Fabric cluster
Remove-AzureRmResourceGroup -Name $clusterresourcegroupname
```

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Colocar uma aplicação existente num contentor com o Visual Studio
> * Criar uma SQL Database do Azure
> * Criar um registo de contentor do Azure
> * Implementar uma aplicação do Service Fabric no Azure

Na parte seguinte do tutorial, saiba como [Implementar uma aplicação de contentor com CI/CD num cluster do Service Fabric](service-fabric-tutorial-deploy-container-app-with-cicd-vsts.md).

[link-fabrikam-github]: https://aka.ms/fabrikamcontainer
[link-azure-powershell-install]: /powershell/azure/azurerm/install-azurerm-ps
[link-servicefabric-create-secure-clusters]: service-fabric-cluster-creation-via-arm.md
[link-visualstudio-cd-extension]: https://aka.ms/cd4vs
[link-servicefabric-containers]: service-fabric-get-started-containers.md
[link-azure-portal]: https://portal.azure.com
[link-sf-clustertemplate]: https://aka.ms/securepreviewonelineclustertemplate
[link-azure-pricing-calculator]: https://azure.microsoft.com/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /azure/sql-database/

[fabrikam-web-page]: media/service-fabric-host-app-in-a-container/fabrikam-web-page.png
[fabrikam-web-page-deployed]: media/service-fabric-host-app-in-a-container/fabrikam-web-page-deployed.png
[publish-app]: media/service-fabric-host-app-in-a-container/publish-app.png
