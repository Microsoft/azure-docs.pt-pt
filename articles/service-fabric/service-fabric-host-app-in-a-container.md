---
title: Implementar uma aplicação .NET num recipiente para o Azure Service Fabric
description: Saiba como colocar uma aplicação .NET existente num contentor com o Visual Studio e depurar contentores no Service Fabric localmente. A aplicação em contentor é enviada para um registo de contentor do Azure e implementada num cluster do Service Fabric. Quando implementada no Azure, a aplicação utiliza a BD SQL do Azure para manter os dados.
ms.topic: tutorial
ms.date: 07/08/2019
ms.openlocfilehash: 7930651a0faa5f37336c15557e2a0f068d613011
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98936709"
---
# <a name="tutorial-deploy-a-net-application-in-a-windows-container-to-azure-service-fabric"></a>Tutorial: Implementar uma aplicação .NET num contentor do Windows no Azure Service Fabric

Este tutorial explica como colocar uma aplicação ASP.NET existente num contentor e empacotá-la como uma aplicação do Service Fabric.  Execute os contentores localmente no cluster de desenvolvimento do Service Fabric e, em seguida, implemente a aplicação no Azure.  A aplicação mantém os dados na [Base de Dados SQL do Azure](../azure-sql/database/sql-database-paas-overview.md).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Colocar uma aplicação existente num contentor com o Visual Studio
> * Criar uma base de dados na Base de Dados Azure SQL
> * Criar um registo de contentor do Azure
> * Implementar uma aplicação do Service Fabric no Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

1. Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
2. Ativar as funcionalidades **Hyper-V** e **Containers**.
3. Instale [o Docker Desktop para windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description) para que possa executar recipientes no Windows 10.
4. Instale o [runtime do Service Fabric, versão 6.2 ou posterior](service-fabric-get-started.md) e o [SDK do Service Fabric, versão 3.1](service-fabric-get-started.md) ou posterior.
5. Instale [o Visual Studio 2019 Versão 16.1](https://www.visualstudio.com/) ou posterior com o **desenvolvimento do Azure** e ASP.NET e trabalhos **de desenvolvimento web.**
6. Instalar [Azure PowerShell][link-azure-powershell-install]

## <a name="download-and-run-fabrikam-fiber-callcenter"></a>Transferir e executar o Fabrikam Fiber CallCenter

1. Transfira a aplicação de exemplo [Fabrikam Fiber CallCenter][link-fabrikam-github].  Clique na ligação **transferir arquivo**.  A partir do diretório *sourceCode* no ficheiro *fabrikam.zip*, extraia o ficheiro *sourceCode.zip* e, em seguida, extraia o diretório *VS2015* para o seu computador.

2. Certifique-se de que a aplicação Fabrikam Fiber CallCenter é compilada e executada sem erros.  Inicie o Visual Studio como **administrador** e abra o ficheiro [FabrikamFiber.CallCenter.sln][link-fabrikam-github].  Prima F5 para depurar e executar a aplicação.

   ![Screenshot da página inicial da aplicação Fabrikam Fiber CallCenter em execução no anfitrião local. A página mostra um dashboard com uma lista de chamadas de apoio.][fabrikam-web-page]

## <a name="create-an-azure-sql-db"></a>Criar uma BD SQL do Azure

Quando executa a aplicação Fabrikam Fiber CallCenter em produção, os dados têm de ser mantidos numa base de dados. Não existe atualmente nenhuma forma de garantir que os dados fiquem num contentor, por isso não pode armazenar dados de produção no SQL Server num contentor.

Recomendamos a [Base de Dados SQL do Azure](../azure-sql/database/powershell-script-content-guide.md). Para configurar e executar uma Base de Dados SQL Server gerida no Azure, execute o seguinte script.  Modifique as variáveis do script conforme necessário. *clientIP* é o endereço IP do seu computador de desenvolvimento. Tome nota do nome do servidor descoduído pelo script.

```powershell
$subscriptionID="<subscription ID>"

# Sign in to your Azure account and select your subscription.
Login-AzAccount -SubscriptionId $subscriptionID

# The data center and resource name for your resources.
$dbresourcegroupname = "fabrikam-fiber-db-group"
$location = "southcentralus"

# The server name: Use a random value or replace with your own value (do not capitalize).
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
New-AzResourceGroup -Name $dbresourcegroupname -Location $location

# Create the SQL server.
New-AzSqlServer -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

# Create the firewall rule to allow your development computer to access the server.
New-AzSqlServerFirewallRule -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowClient" -StartIpAddress $clientIP -EndIpAddress $clientIP

# Create the database in the server.
New-AzSqlDatabase  -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -RequestedServiceObjectiveName "S0"

Write-Host "Server name is $servername"
```

> [!TIP]
> Se estiver protegido por uma firewall de empresa, o endereço IP do seu computador de desenvolvimento não pode ser exposto à Internet. Para verificar que a base de dados tem o endereço IP correto para a regra de firewall, vá para o [portal do Azure](https://portal.azure.com) e encontre a sua base de dados na secção Bases de Dados SQL. Clique no respetivo nome e, em seguida, na secção Descrição geral, clique em "Definir firewall do servidor". O "endereço IP do cliente" é o endereço IP do seu computador de desenvolvimento. Certifique-se de que corresponde ao endereço IP na regra "AllowClient".

## <a name="update-the-web-config"></a>Atualizar a configuração da Web

Volte para o projeto **FabrikamFiber.Web**, atualize a cadeia de ligação no ficheiro **web.config**, para que encaminhe para o SQL Server no contentor.  Atualize a parte do *Servidor* da cadeia de ligação para ser o nome do servidor criado pelo script anterior. Deve ser algo como "fab-fiber-751718376.database.windows.net". No XML seguinte, é necessário atualizar apenas o `connectionString` atributo; os `providerName` atributos e `name` atributos não precisam de ser alterados.

```xml
<add name="FabrikamFiber-Express" connectionString="Server=<server name>,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
<add name="FabrikamFiber-DataWarehouse" connectionString="Server=<server name>,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
  
```

>[!NOTE]
>Pode utilizar qualquer SQL Server que preferir para depuração local, desde que seja acessível a partir do seu anfitrião. No entanto, o **localdb** não suporta comunicação `container -> host`. Se pretender utilizar uma base de dados SQL diferente ao criar uma versão de compilação da aplicação Web, adicione outra cadeia de ligação ao seu ficheiro *web.release.config*.

## <a name="containerize-the-application"></a>Colocar a aplicação num contentor

1. Clique com o botão direito do rato em **FabrikamFiber.Web** projeto > **Adicionar** > **Suporte de Container Orchestrator**.  Selecione **Service Fabric** como o orquestrador de contentores e clique em **OK**.

2. Se solicitado, clique em **Sim** para mudar o Docker para os recipientes do Windows agora.

   É criado um novo projeto da aplicação Service Fabric **FabrikamFiber.CallCenterApplication** na solução.  Um Dockerfile é adicionado ao projeto **FabrikamFiber.Web** existente.  Um diretório **PackageRoot** também é adicionado ao projeto **FabrikamFiber.Web**, que contém o manifesto do serviço e as definições para o novo serviço FabrikamFiber.Web.

   O contentor está agora pronto para ser criado e empacotado numa aplicação do Service Fabric. Assim que tiver a imagem de contentor incorporada no seu computador, pode enviá-la para qualquer registo de contentor e pedir a qualquer anfitrião para executar.

## <a name="run-the-containerized-application-locally"></a>Executar a aplicação em contentor localmente

Prima **F5** para executar e depurar a aplicação num contentor no cluster de desenvolvimento do Service Fabric local. Clique em **Sim** se for apresentada uma caixa de mensagem a pedir para conceder permissões de leitura e execução do grupo “ServiceFabricAllowedUsers” ao diretório do projeto do Visual Studio.

Se a execução de F5 lançar uma exceção como segue, então o IP correto não foi adicionado à firewall da base de dados Azure.

```text
System.Data.SqlClient.SqlException
HResult=0x80131904
Message=Cannot open server 'fab-fiber-751718376' requested by the login. Client with IP address '123.456.789.012' is not allowed to access the server.  To enable access, use the Windows Azure Management Portal or run sp_set_firewall_rule on the master database to create a firewall rule for this IP address or address range.  It may take up to five minutes for this change to take effect.
Source=.Net SqlClient Data Provider
StackTrace:
<Cannot evaluate the exception stack trace>
```

Para adicionar o IP apropriado à firewall da base de dados Azure, executar o seguinte comando.

```powershell
# The IP address of your development computer that accesses the SQL DB.
$clientIPNew = "<client IP from the Error Message>"

# Create the firewall rule to allow your development computer to access the server.
New-AzSqlServerFirewallRule -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowClientNew" -StartIpAddress $clientIPNew -EndIpAddress $clientIPNew
```

## <a name="create-a-container-registry"></a>Criar um registo de contentor

Agora que a aplicação é executada localmente, comece a preparar a implementação do Azure.  As imagens de contentor têm de ser armazenadas num registo de contentor.  Crie um [registo de contentor do Azure](../container-registry/container-registry-intro.md) com o script seguinte. O nome de registo do contentor é visível para as outras subscrições do Azure, pelo que tem de ser exclusivo.
Antes de implementar a aplicação no Azure, coloque a imagem de contentor neste registo.  Quando a aplicação é implementada no cluster no Azure, a imagem de contentor é retirada deste registo.

```powershell
# Variables
$acrresourcegroupname = "fabrikam-acr-group"
$location = "southcentralus"
$registryname="fabrikamregistry$(Get-Random)"

New-AzResourceGroup -Name $acrresourcegroupname -Location $location

$registry = New-AzContainerRegistry -ResourceGroupName $acrresourcegroupname -Name $registryname -EnableAdminUser -Sku Basic
```

## <a name="create-a-service-fabric-cluster-on-azure"></a>Criar um cluster do Service Fabric no Azure

As aplicações do Service Fabric são executadas num cluster, um conjunto de máquinas virtuais ou físicas ligadas à rede.  Antes de poder implementar a aplicação para a Azure, crie um cluster de Tecido de Serviço em Azure.

Pode:

* Criar um cluster de teste a partir do Visual Studio. Esta opção permite-lhe criar um cluster seguro diretamente a partir do Visual Studio com as suas configurações preferidas.
* [Criar um cluster seguro a partir de um modelo](service-fabric-tutorial-create-vnet-and-windows-cluster.md)

Este tutorial cria um cluster a partir do Visual Studio, que é ideal para cenários de teste. Se criar um cluster de outra forma ou utilizar um cluster existente, pode copiar e colar o ponto final de ligação ou escolhê-lo a partir da sua subscrição.

Antes de começar, abra FabrikamFiber.Web -> PackageRoot -> ServiceManifest.xml no Solution Explorer. Tome nota da porta para a porta frontal listada em **Endpoint**.

Ao criar o cluster:

1. Clique com o botão direito do rato no projeto de aplicação **FabrikamFiber.CallCenterApplication** no Explorador de Soluções e escolha **Publicar**.
2. Inicie sessão com a sua conta do Azure para poder aceder às suas subscrições.
3. Abaixo do dropdown para o ponto final de **ligação**, selecione a opção **Criar Novo Cluster...**
4. No diálogo **de cluster Create,** modifique as seguintes definições:

    a. Especifique o nome do seu cluster no campo **Nome cluster,** bem como a subscrição e localização que pretende utilizar. Tome nota do nome do seu grupo de recursos de cluster.

    b. Opcional: pode modificar o número de nós. Por predefinição, tem três nós, o mínimo exigido para testar cenários do Service Fabric.

    c. Selecione o **separador Certificado.** Neste separador, digite uma palavra-passe para proteger o certificado do seu cluster. Este certificado ajuda a tornar o seu cluster seguro. Também pode modificar o caminho para o local onde pretende guardar o certificado. O Visual Studio também pode importar o certificado por si, uma vez que este é um passo obrigatório para publicar a aplicação no cluster.

    >[!NOTE]
    >Tome nota do caminho da pasta onde este certificado é importado. O próximo passo após a criação do cluster é importar este certificado.

    d. Selecione o **separador detalhes VM.** Especifique a palavra-passe que gostaria de usar para as Máquinas Virtuais (VM) que compõem o cluster. O nome de utilizador e a palavra-passe podem ser utilizados para ligar remotamente às VMs. Também tem de selecionar um tamanho de VM e pode alterar a imagem da VM, se necessário.

    > [!IMPORTANT]
    > Escolha um SKU que suporte a funcionamento de contentores. O SO do Windows Server nos nós do cluster tem de ser compatível com o SO do Windows Server do seu contentor. Para obter mais informações, veja [Compatibilidade do sistema operativo do contentor do Windows Server e do sistema operativo do sistema anfitrião ](service-fabric-get-started-containers.md#windows-server-container-os-and-host-os-compatibility). Por predefinição, este tutorial cria uma imagem do Docker com base no Windows Server 2016 LTSC. Os contentores com base nesta imagem serão executados nos clusters criados com o Windows Server 2016 Datacenter com Contentores. No entanto, se criar um cluster ou utilizar um cluster existente com base numa versão diferente do Windows Server, tem de alterar a imagem de SO em que o recipiente se baseia. Abra o ficheiro de **estiva** no projeto **FabrikamFiber.Web,** comente qualquer declaração existente `FROM` com base numa versão anterior do Windows Server e adicione uma declaração com base na etiqueta da versão `FROM` desejada a partir da página do Núcleo [do DockerHub](https://hub.docker.com/_/microsoft-windows-servercore)do Windows Server . Para obter informações adicionais sobre as versões do Núcleo do Servidor do Windows, linhas de tempo de suporte e versão, consulte a [informação de lançamento do Windows Server Core](/windows-server/get-started/windows-server-release-info). 

    e. No separador **Avançadas**, indique a porta de aplicação a abrir no balanceador de carga quando o cluster é implementado. Esta é a porta que tomou nota antes de começar a criar o cluster. Também pode adicionar uma chave do Application Insights existente para ser utilizada no encaminhamento dos ficheiros de registo da aplicação.

    f. Quando terminar de modificar as definições, selecione o botão **Criar.**

5. A criação demora vários minutos a ser concluída; a janela de saída indicará quando o cluster estiver totalmente criado.

## <a name="install-the-imported-certificate"></a>Instalar o certificado importado

Instale o certificado importado como parte do passo de criação do cluster, para a localização da loja **do Utilizador Atual** e forneça a senha de acesso privada que forneceu.

Pode confirmar a instalação abrindo certificados de **utilizador geridos** a partir do painel de controlo e confirmando que o certificado está instalado nos **Certificados -**  ->  Certificados **Pessoais** do Utilizador Atual  ->  . O certificado deve ser como *[Nome do Cluster]*. *[Localização do Cluster]*.cloudapp.azure.com, por *exemplo, fabrikamfibercallcenter.southcentralus.cloudapp.azure.com*. 

## <a name="allow-your-application-running-in-azure-to-access-sql-database"></a>Permitir que a sua aplicação em funcionamento em Azure aceda à Base de Dados SQL

Anteriormente, criou uma regra de firewall de SQL para dar acesso à sua aplicação em execução localmente.  Em seguida, precisa de ativar a aplicação em execução no Azure para aceder à BD SQL.  Crie um [ponto final de serviço de rede virtual](../azure-sql/database/vnet-service-endpoint-rule-overview.md) para o cluster do Service Fabric e, em seguida, crie uma regra para permitir que esse ponto final aceda à BD SQL. Certifique-se de especificar a variável do grupo de recursos de cluster que tomou nota ao criar o cluster.

```powershell
# Create a virtual network service endpoint
$clusterresourcegroup = "<cluster resource group>"
$resource = Get-AzResource -ResourceGroupName $clusterresourcegroup -ResourceType Microsoft.Network/virtualNetworks | Select-Object -first 1
$vnetName = $resource.Name

Write-Host 'Virtual network name: ' $vnetName

# Get the virtual network by name.
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $clusterresourcegroup `
  -Name              $vnetName

Write-Host "Get the subnet in the virtual network:"

# Get the subnet, assume the first subnet contains the Service Fabric cluster.
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet | Select-Object -first 1

$subnetName = $subnet.Name
$subnetID = $subnet.Id
$addressPrefix = $subnet.AddressPrefix

Write-Host "Subnet name: " $subnetName " Address prefix: " $addressPrefix " ID: " $subnetID

# Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.
$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $subnetName `
  -AddressPrefix   $addressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint Microsoft.Sql | Set-AzVirtualNetwork

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

# Add a SQL DB firewall rule for the virtual network service endpoint
$subnet = Get-AzVirtualNetworkSubnetConfig `
  -Name           $subnetName `
  -VirtualNetwork $vnet;

$VNetRuleName="ServiceFabricClusterVNetRule"
$vnetRuleObject1 = New-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $dbresourcegroupname `
  -ServerName             $servername `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnetID;
```

## <a name="deploy-the-application-to-azure"></a>Implementar a aplicação no Azure

Agora que a aplicação está pronta, pode implementá-la no cluster diretamente no Azure a partir do Visual Studio.  No Explorador de Soluções, clique com o botão direito do rato no projeto de aplicação **FabrikamFiber.CallCenterApplication** e selecione **Publicar**.  Em **Ponto Final de Ligação**, selecione o ponto final do cluster que criou anteriormente.  Em **Azure Container Registry**, selecione o registo de contentor que criou anteriormente.  Clique em **Publicar** para implementar a aplicação no cluster no Azure.

![Publicar a aplicação][publish-app]

Siga o progresso da implementação na janela de saída. Quando a aplicação for implementada, abra um browser e escreva o endereço do cluster e a porta da aplicação. Por exemplo, `http://fabrikamfibercallcenter.southcentralus.cloudapp.azure.com:8659/`.

![Screenshot da página inicial da aplicação Fabrikam Fiber CallCenter em execução no azure.com. A página mostra um dashboard com uma lista de chamadas de apoio.][fabrikam-web-page-deployed]

Se a página não carregar ou não solicitar o certificado, tente abrir o caminho do Explorer, por exemplo, `https://fabrikamfibercallcenter.southcentralus.cloudapp.azure.com:19080/Explorer` e selecione o certificado recém-instalado.

## <a name="set-up-continuous-integration-and-deployment-cicd-with-a-service-fabric-cluster"></a>Configurar a Integração e a Implementação Contínua (CI/CD) com um cluster do Service Fabric

Para saber como utilizar o Azure DevOps para configurar a implementação de aplicação de CI/CD num cluster do Service Fabric, veja o [Tutorial: Implementar uma aplicação com a CI/CD num cluster do Service Fabric](service-fabric-tutorial-deploy-app-with-cicd-vsts.md). O processo descrito no tutorial é o mesmo para este projeto (FabrikamFiber), basta ignorar a transferência do exemplo de Voto e substituir o FabrikamFiber pelo nome do repositório, em vez de Voto.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar, lembre-se de remover todos os recursos que criou.  A forma mais simples de fazê-lo consiste em remover os grupos de recursos que contêm o cluster do Service Fabric, a BD SQL do Azure e o Azure Container Registry.

```powershell
$dbresourcegroupname = "fabrikam-fiber-db-group"
$acrresourcegroupname = "fabrikam-acr-group"
$clusterresourcegroupname="fabrikamcallcentergroup"

# Remove the Azure SQL DB
Remove-AzResourceGroup -Name $dbresourcegroupname

# Remove the container registry
Remove-AzResourceGroup -Name $acrresourcegroupname

# Remove the Service Fabric cluster
Remove-AzResourceGroup -Name $clusterresourcegroupname
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
>
> * Colocar uma aplicação existente num contentor com o Visual Studio
> * Criar uma base de dados na Base de Dados Azure SQL
> * Criar um registo de contentor do Azure
> * Implementar uma aplicação do Service Fabric no Azure

Na parte seguinte do tutorial, saiba como [Implementar uma aplicação de contentor com CI/CD num cluster do Service Fabric](service-fabric-tutorial-deploy-container-app-with-cicd-vsts.md).

[link-fabrikam-github]: https://aka.ms/fabrikamcontainer
[link-azure-powershell-install]: /powershell/azure/install-Az-ps
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