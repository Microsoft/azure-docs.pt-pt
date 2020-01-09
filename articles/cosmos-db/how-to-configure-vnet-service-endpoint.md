---
title: Configurar o acesso baseado em rede virtual para uma conta do Azure Cosmos
description: Este documento descreve as etapas necessárias para configurar um ponto de extremidade de serviço de rede virtual para Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 80e77e2caacfed7d662fdfa04aa9524bd7c2083a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445418"
---
# <a name="configure-access-from-virtual-networks-vnet"></a>Configurar o acesso de redes virtuais (VNet)

Pode configurar as contas do Azure Cosmos DB para permitirem o acesso apenas a uma sub-rede específica de uma rede virtual do Azure. Para limitar o acesso a uma conta de Azure Cosmos DB com conexões de uma sub-rede em uma rede virtual:

1. Habilite a sub-rede para enviar a identidade de rede virtual e de sub-rede para Azure Cosmos DB. Você pode conseguir isso habilitando um ponto de extremidade de serviço para Azure Cosmos DB na sub-rede específica.

1. Adicione uma regra na conta de Azure Cosmos DB para especificar a sub-rede como uma fonte da qual a conta pode ser acessada.

> [!NOTE]
> Quando um ponto de extremidade de serviço para sua conta de Azure Cosmos DB está habilitado em uma sub-rede, a origem do tráfego que atinge Azure Cosmos DB alterna de um IP público para uma rede virtual e sub-rede. A alternância de tráfego se aplica a qualquer conta de Azure Cosmos DB que é acessada por essa sub-rede. Se suas contas de Azure Cosmos DB tiverem um firewall baseado em IP para permitir essa sub-rede, as solicitações da sub-rede habilitada para serviço não corresponderão mais às regras de firewall de IP e serão rejeitadas.
>
> Para saber mais, consulte as etapas descritas na seção [migrando de uma regra de firewall de IP para uma lista de controle de acesso à rede virtual](#migrate-from-firewall-to-vnet) deste artigo.

As seções a seguir descrevem como configurar um ponto de extremidade de serviço de rede virtual para uma conta de Azure Cosmos DB.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a id="configure-using-portal"></a>Configurar um ponto de extremidade de serviço usando o portal do Azure

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Configurar um ponto de extremidade de serviço para uma sub-rede e rede virtual do Azure existente

1. Na folha **todos os recursos** , localize a conta de Azure Cosmos DB que você deseja proteger.

1. Selecione **firewalls e redes virtuais** no menu configurações e escolha permitir o acesso de **redes selecionadas**.

1. Para conceder acesso a uma sub-rede de uma rede virtual existente, em **redes virtuais**, selecione **Adicionar rede virtual do Azure existente**.

1. Selecione a **assinatura** da qual você deseja adicionar uma rede virtual do Azure. Selecione as **redes virtuais** e **sub-redes** do Azure que você deseja fornecer acesso à sua conta de Azure Cosmos DB. Em seguida, selecione **habilitar** para habilitar redes selecionadas com pontos de extremidade de serviço para "Microsoft. AzureCosmosDB". Quando estiver concluído, selecione **Adicionar**.

   ![Selecionar rede virtual e sub-rede](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)

1. Depois que a conta de Azure Cosmos DB estiver habilitada para acesso de uma rede virtual, ela permitirá o tráfego somente dessa sub-rede escolhida. A rede virtual e a sub-rede que você adicionou devem aparecer conforme mostrado na seguinte captura de tela:

   ![Rede virtual e sub-rede configuradas com êxito](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> Para habilitar pontos de extremidade de serviço de rede virtual, você precisa das seguintes permissões de assinatura:
>   * Assinatura com rede virtual: colaborador de rede
>   * Assinatura com conta de Azure Cosmos DB: colaborador de conta do DocumentDB
>   * Se sua rede virtual e Azure Cosmos DB conta estiverem em assinaturas diferentes, certifique-se de que a assinatura que tem a rede virtual também tenha `Microsoft.DocumentDB` provedor de recursos registrado. Para registrar um provedor de recursos, consulte o artigo [provedores e tipos de recursos do Azure](../azure-resource-manager/resource-manager-supported-services.md) .

Aqui estão as instruções para registrar a assinatura com o provedor de recursos.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Configurar um ponto de extremidade de serviço para uma nova sub-rede e rede virtual do Azure

1. Na folha **todos os recursos** , localize a conta de Azure Cosmos DB que você deseja proteger.  

1. Selecione **firewalls e redes virtuais do Azure** no menu configurações e escolha permitir o acesso de **redes selecionadas**.  

1. Para conceder acesso a uma nova rede virtual do Azure, em **redes virtuais**, selecione **Adicionar nova rede virtual**.  

1. Forneça os detalhes necessários para criar uma nova rede virtual e, em seguida, selecione **criar**. A sub-rede será criada com um ponto de extremidade de serviço para "Microsoft. AzureCosmosDB" habilitado.

   ![Selecionar uma rede virtual e uma sub-rede para uma nova rede virtual](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

Se sua conta de Azure Cosmos DB for usada por outros serviços do Azure, como o Pesquisa Cognitiva do Azure, ou for acessada do Stream Analytics ou Power BI, você permitirá o acesso selecionando **aceitar conexões de dentro de datacenters globais do Azure**.

Para garantir que você tenha acesso a Azure Cosmos DB métricas no portal, você precisa habilitar o **acesso de permitir de portal do Azure** opções. Para saber mais sobre essas opções, consulte o artigo [configurar um firewall de IP](how-to-configure-firewall.md) . Depois de habilitar o acesso, selecione **salvar** para salvar as configurações.

## <a id="remove-vnet-or-subnet"></a>Remover uma rede virtual ou sub-rede

1. Na folha **todos os recursos** , localize a conta de Azure Cosmos DB para a qual você atribuiu pontos de extremidade de serviço.  

2. Selecione **firewalls e redes virtuais** no menu configurações.  

3. Para remover uma rede virtual ou regra de sub-rede, selecione **...** ao lado da rede virtual ou sub-rede e selecione **remover**.

   ![Remover uma rede virtual](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

4. Selecione **Guardar** para aplicar as suas alterações.

## <a id="configure-using-powershell"></a>Configurar um ponto de extremidade de serviço usando Azure PowerShell

> [!NOTE]
> Quando você estiver usando o PowerShell ou o CLI do Azure, certifique-se de especificar a lista completa de filtros IP e ACLs de rede virtual em parâmetros, não apenas aqueles que precisam ser adicionados.

Use as etapas a seguir para configurar um ponto de extremidade de serviço para uma conta de Azure Cosmos DB usando Azure PowerShell:  

1. Instale [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) e [entre](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  

1. Habilite o ponto de extremidade de serviço para uma sub-rede existente de uma rede virtual.  

   ```powershell
   $rgname = "<Resource group name>"
   $vnName = "<Virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzVirtualNetworkSubnetConfig `
    -Name $sname  `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzVirtualNetwork
   ```

1. Obter informações da rede virtual.

   ```powershell
   $vnProp = Get-AzVirtualNetwork `
     -Name $vnName `
     -ResourceGroupName $rgName
   ```

1. Obtenha as propriedades da conta de Azure Cosmos DB executando o seguinte cmdlet:  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. Inicialize as variáveis para uso posterior. Configure todas as variáveis da definição de conta existente.

   ```powershell
   $locations = @()

   foreach ($readLocation in $cosmosDBConfiguration.Properties.readLocations) {
      $locations += , @{
         locationName     = $readLocation.locationName;
         failoverPriority = $readLocation.failoverPriority;
      }
   }

   $virtualNetworkRules = @(@{
      id = "$($vnProp.Id)/subnets/$sname";
   })

   if ($cosmosDBConfiguration.Properties.isVirtualNetworkFilterEnabled) {
      $virtualNetworkRules = $cosmosDBConfiguration.Properties.virtualNetworkRules + $virtualNetworkRules
   }
   ```

1. Atualize Azure Cosmos DB Propriedades da conta com a nova configuração executando os seguintes cmdlets: 

   ```powershell
   $cosmosDBProperties = @{
      databaseAccountOfferType      = $cosmosDBConfiguration.Properties.databaseAccountOfferType;
      consistencyPolicy             = $cosmosDBConfiguration.Properties.consistencyPolicy;
      ipRangeFilter                 = $cosmosDBConfiguration.Properties.ipRangeFilter;
      locations                     = $locations;
      virtualNetworkRules           = $virtualNetworkRules;
      isVirtualNetworkFilterEnabled = $True;
   }

   Set-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName `
     -Properties $CosmosDBProperties
   ```

1. Execute o comando a seguir para verificar se sua conta de Azure Cosmos DB está atualizada com o ponto de extremidade de serviço de rede virtual que você configurou na etapa anterior:

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a id="configure-using-cli"></a>Configurar um ponto de extremidade de serviço usando o CLI do Azure

As contas do Azure Cosmos podem ser configuradas para pontos de extremidade de serviço quando forem criadas ou atualizadas posteriormente se a sub-rede já estiver configurada para elas. Os pontos de extremidade de serviço também podem ser habilitados na conta Cosmos em que a sub-rede ainda não está configurada para eles e começará a funcionar quando a sub-rede for configurada posteriormente. Essa flexibilidade permite aos administradores que não têm acesso à conta Cosmos e aos recursos de rede virtual para tornar suas configurações independentes umas das outras.

### <a name="create-a-new-cosmos-account-and-connect-it-to-a-back-end-subnet-for-a-new-virtual-network"></a>Criar uma nova conta do cosmos e conectá-la a uma sub-rede de back-end para uma nova rede virtual

Neste exemplo, a rede virtual e a sub-rede são criadas com pontos de extremidade de serviço habilitados para ambos quando eles são criados.

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet with service endpoints enabled for Cosmos DB
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values and service endpoints
az cosmosdb create \
   -n $accountName \
   -g $resourceGroupName \
   --enable-virtual-network true \
   --virtual-network-rules $svcEndpoint
```

### <a name="connect-and-configure-a-cosmos-account-to-a-back-end-subnet-independently"></a>Conectar e configurar uma conta do cosmos para uma sub-rede de back-end independentemente

Este exemplo destina-se a mostrar como conectar uma conta do Azure Cosmos a uma nova rede virtual existente na qual a sub-rede ainda não está configurada para pontos de extremidade de serviço. Isso é feito usando o parâmetro `--ignore-missing-vnet-service-endpoint`. Isso permite que a configuração da conta Cosmos seja concluída sem erros antes que a configuração da sub-rede da rede virtual seja concluída. Depois que a configuração de sub-rede for concluída, a conta Cosmos será acessível por meio da sub-rede configurada.

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet
# that is not yet enabled for service endpoints.

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet but without configuring service endpoints (--service-endpoints Microsoft.AzureCosmosDB)
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values
az cosmosdb create -n $accountName -g $resourceGroupName

# Add the virtual network rule but ignore the missing service endpoint on the subnet
az cosmosdb network-rule add \
   -n $accountName \
   -g $resourceGroupName \
   --virtual-network $vnetName \
   --subnet svcEndpoint \
   --ignore-missing-vnet-service-endpoint true

read -p'Press any key to now configure the subnet for service endpoints'

az network vnet subnet update \
   -n $backEnd \
   -g $resourceGroupName \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB
```

## <a id="migrate-from-firewall-to-vnet"></a>Migrando de uma regra de firewall IP para uma ACL de rede virtual

Use as etapas a seguir apenas para contas Azure Cosmos DB com regras de firewall IP existentes que permitem uma sub-rede, quando você quiser usar as ACLs baseadas em sub-rede e rede virtual em vez de uma regra de firewall IP.

Depois que um ponto de extremidade de serviço para uma conta de Azure Cosmos DB é ativado para uma sub-rede, as solicitações são enviadas com uma fonte que contém informações de rede virtual e de sub-rede em vez de um IP público. Essas solicitações não correspondem a um filtro IP. Esse comutador de origem ocorre para todas as contas de Azure Cosmos DB acessadas da sub-rede com um ponto de extremidade de serviço habilitado. Para evitar o tempo de inatividade, use as seguintes etapas:

1. Obtenha as propriedades da conta de Azure Cosmos DB executando o seguinte cmdlet:

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. Inicialize as variáveis para usá-las mais tarde. Configure todas as variáveis da definição de conta existente. Adicione a ACL de rede virtual a todas as contas de Azure Cosmos DB que estão sendo acessadas da sub-rede com `ignoreMissingVNetServiceEndpoint` sinalizador.

   ```powershell
   $locations = @()

   foreach ($readLocation in $cosmosDBConfiguration.Properties.readLocations) {
      $locations += , @{
         locationName     = $readLocation.locationName;
         failoverPriority = $readLocation.failoverPriority;
      }
   }

   $subnetID = "Subnet ARM URL" e.g "/subscriptions/f7ddba26-ab7b-4a36-a2fa-7d01778da30b/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/subnet1"

   $virtualNetworkRules = @(@{
      id = $subnetID;
      ignoreMissingVNetServiceEndpoint = "True";
   })

   if ($cosmosDBConfiguration.Properties.isVirtualNetworkFilterEnabled) {
      $virtualNetworkRules = $cosmosDBConfiguration.Properties.virtualNetworkRules + $virtualNetworkRules
   }
   ```

1. Atualize Azure Cosmos DB Propriedades da conta com a nova configuração executando os seguintes cmdlets:

   ```powershell
   $cosmosDBProperties = @{
      databaseAccountOfferType      = $cosmosDBConfiguration.Properties.databaseAccountOfferType;
      consistencyPolicy             = $cosmosDBConfiguration.Properties.consistencyPolicy;
      ipRangeFilter                 = $cosmosDBConfiguration.Properties.ipRangeFilter;
      locations                     = $locations;
      virtualNetworkRules           = $virtualNetworkRules;
      isVirtualNetworkFilterEnabled = $True;
   }

   Set-AzResource `
      -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
      -ApiVersion $apiVersion `
      -ResourceGroupName $rgName `
      -Name $acctName `
      -Properties $CosmosDBProperties
   ```

1. Repita as etapas 1-3 para todas as contas de Azure Cosmos DB que você acessa da sub-rede.

1.  Aguarde 15 minutos e, em seguida, atualize a sub-rede para habilitar o ponto de extremidade de serviço.

1.  Habilite o ponto de extremidade de serviço para uma sub-rede existente de uma rede virtual.

    ```powershell
    $rgname= "<Resource group name>"
    $vnName = "<virtual network name>"
    $sname = "<Subnet name>"
    $subnetPrefix = "<Subnet address range>"

    Get-AzVirtualNetwork `
       -ResourceGroupName $rgname `
       -Name $vnName | Set-AzVirtualNetworkSubnetConfig `
       -Name $sname `
       -AddressPrefix $subnetPrefix `
       -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzVirtualNetwork
    ```

1. Remova a regra de firewall IP para a sub-rede.

## <a name="next-steps"></a>Passos seguintes

* Para configurar um firewall para Azure Cosmos DB, consulte o artigo de [suporte do firewall](firewall-support.md) .
