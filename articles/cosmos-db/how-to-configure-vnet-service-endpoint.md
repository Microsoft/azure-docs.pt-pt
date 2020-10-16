---
title: Configurar o acesso à rede virtual para uma conta Azure Cosmos
description: Este documento descreve os passos necessários para configurar um ponto final de serviço de rede virtual para a Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/04/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 995e5a1a87ee332c48641f42c4134e3e58f11cfa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87495425"
---
# <a name="configure-access-from-virtual-networks-vnet"></a>Configure o acesso a partir de redes virtuais (VNet)

Pode configurar as contas do Azure Cosmos DB para permitirem o acesso apenas a uma sub-rede específica de uma rede virtual do Azure. Para limitar o acesso a uma conta DB da Azure Cosmos com ligações a partir de uma sub-rede numa rede virtual:

1. Ative a sub-rede para enviar a sub-rede e identidade de rede virtual para Azure Cosmos DB. Pode conseguir isso ativando um ponto final de serviço para a Azure Cosmos DB na sub-rede específica.

1. Adicione uma regra na conta DB da Azure Cosmos para especificar a sub-rede como uma fonte a partir da qual a conta pode ser acedida.

> [!NOTE]
> Quando um ponto final de serviço para a sua conta DB Azure Cosmos é ativado numa sub-rede, a fonte do tráfego que atinge a Azure Cosmos DB muda de um IP público para uma rede virtual e sub-rede. A comutação de tráfego aplica-se a qualquer conta DB Azure Cosmos que tenha acesso a partir desta sub-rede. Se as suas contas DB da Azure Cosmos tiverem uma firewall baseada em IP para permitir esta sub-rede, os pedidos da sub-rede ativada pelo serviço já não correspondem às regras de firewall IP, e são rejeitados.
>
> Para saber mais, consulte os passos descritos na [migração de uma regra de firewall IP para uma](#migrate-from-firewall-to-vnet) secção de lista de controlo de acesso à rede virtual deste artigo.

As secções seguintes descrevem como configurar um ponto final de serviço de rede virtual para uma conta DB Azure Cosmos.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-a-service-endpoint-by-using-the-azure-portal"></a><a id="configure-using-portal"></a>Configure um ponto final de serviço utilizando o portal Azure

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Configurar um ponto final de serviço para uma rede virtual Azure existente e sub-rede

1. A partir da lâmina **de todos os recursos,** encontre a conta DB Azure Cosmos que você quer proteger.

1. Selecione **Firewalls e redes virtuais** a partir do menu de definições e opte por permitir o acesso a partir de **redes selecionadas**.

1. Para conceder acesso à sub-rede virtual existente, em **redes Virtuais,** selecione **Adicionar a rede virtual Azure existente**.

1. Selecione a **Subscrição** a partir da qual pretende adicionar uma rede virtual Azure. Selecione as **redes Eretivas** Azure Virtual e **Subnets** que pretende fornecer acesso à sua conta DB Azure Cosmos. Em seguida, **selecione Ativar** para ativar redes selecionadas com pontos finais de serviço para "Microsoft.AzureCosmosDB". Quando estiver completo, **selecione Add**.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png" alt-text="Selecione rede virtual e sub-rede":::

1. Depois de a conta DB Azure Cosmos ter sido ativada para acesso a partir de uma rede virtual, permitirá o tráfego a partir apenas desta sub-rede escolhida. A rede virtual e a sub-rede que adicionou devem aparecer como mostrado na seguinte imagem:

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png" alt-text="Selecione rede virtual e sub-rede":::

> [!NOTE]
> Para ativar os pontos finais do serviço de rede virtual, precisa das seguintes permissões de subscrição:
>   * Assinatura com rede virtual: Colaborador de rede
>   * Subscrição com conta DB da Azure Cosmos: Contribuinte de conta DocumentDB
>   * Se a sua rede virtual e a conta DB do Azure Cosmos estiverem em diferentes subscrições, certifique-se de que a subscrição que tem rede virtual também tem `Microsoft.DocumentDB` fornecedor de recursos registado. Para registar um fornecedor de recursos, consulte [os fornecedores de recursos Azure e artigo de tipos.](../azure-resource-manager/management/resource-providers-and-types.md)

Aqui estão as instruções para registar a subscrição com o fornecedor de recursos.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Configure um ponto final de serviço para uma nova rede virtual Azure e sub-rede

1. A partir da lâmina **de todos os recursos,** encontre a conta DB Azure Cosmos que você quer proteger.  

1. Selecione **Firewalls e redes virtuais Azure** a partir do menu de definições e opte por permitir o acesso a partir de **redes selecionadas**.  

1. Para conceder acesso a uma nova rede virtual Azure, em **redes Virtuais,** selecione **Adicionar nova rede virtual**.  

1. Forneça os detalhes necessários para criar uma nova rede virtual e, em seguida, selecione **Criar**. A sub-rede será criada com um ponto final de serviço para "Microsoft.AzureCosmosDB" ativado.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png" alt-text="Selecione rede virtual e sub-rede":::

Se a sua conta DB Azure Cosmos for utilizada por outros serviços Azure, como a Azure Cognitive Search, ou tiver acesso a partir de análises de stream ou Power BI, permite o acesso selecionando **ligações aceitar ligações dentro dos centros de dados globais do Azure.**

Para garantir que tem acesso às métricas DB do Azure Cosmos a partir do portal, precisa de ativar o acesso a partir das opções **do portal Azure.** Para saber mais sobre estas opções, consulte o artigo configurar um artigo [de firewall IP.](how-to-configure-firewall.md) Depois de ativar o acesso, **selecione Guardar** para guardar as definições.

## <a name="remove-a-virtual-network-or-subnet"></a><a id="remove-vnet-or-subnet"></a>Remover uma rede virtual ou sub-rede

1. A partir da lâmina **de todos os recursos,** encontre a conta DB Azure Cosmos para a qual atribuiu pontos finais de serviço.  

1. Selecione **Firewalls e redes virtuais** a partir do menu de definições.  

1. Para remover uma regra de rede virtual ou sub-rede, selecione... ao lado da rede virtual ou sub-rede, e selecione **Remover**. **...**

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png" alt-text="Selecione rede virtual e sub-rede":::

1. Selecione **Guardar** para aplicar as suas alterações.

## <a name="configure-a-service-endpoint-by-using-azure-powershell"></a><a id="configure-using-powershell"></a>Configure um ponto final de serviço utilizando a Azure PowerShell

> [!NOTE]
> Quando estiver a utilizar o PowerShell ou o Azure CLI, certifique-se de especificar a lista completa de filtros IP e ACLs de rede virtual em parâmetros, e não apenas os que precisam de ser adicionados.

Utilize os seguintes passos para configurar um ponto final de serviço numa conta DB da Azure Cosmos utilizando a Azure PowerShell:  

1. Instale [a Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) e [inscreva-se em](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  

1. Ativar o ponto final de serviço para uma sub-rede existente de uma rede virtual.  

   ```powershell
   $resourceGroupName = "<Resource group name>"
   $vnetName = "<Virtual network name>"
   $subnetName = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"
   $serviceEndpoint = "Microsoft.AzureCosmosDB"

   Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName | Set-AzVirtualNetworkSubnetConfig `
      -Name $subnetName `
      -AddressPrefix $subnetPrefix `
      -ServiceEndpoint $serviceEndpoint | Set-AzVirtualNetwork
   ```

1. Obtenha informações de rede virtual.

   ```powershell
   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Preparar uma regra de rede virtual Cosmos DB

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Atualize as propriedades da conta DB da Azure Cosmos com a nova configuração do ponto final da Rede Virtual: 

   ```powershell
   $accountName = "<Cosmos DB account name>"

   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Executar o seguinte comando para verificar se a sua conta DB Azure Cosmos é atualizada com o ponto final do serviço de rede virtual que configuraste no passo anterior:

   ```powershell
   $account = Get-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName

   $account.IsVirtualNetworkFilterEnabled
   $account.VirtualNetworkRules
   ```

## <a name="configure-a-service-endpoint-by-using-the-azure-cli"></a><a id="configure-using-cli"></a>Configure um ponto final de serviço utilizando o CLI Azure

As contas do Azure Cosmos podem ser configuradas para os pontos finais de serviço quando são criadas ou atualizadas posteriormente se a sub-rede já estiver configurada para os mesmos. Os pontos finais de serviço também podem ser ativados na conta Cosmos, onde a sub-rede ainda não está configurada para eles e, em seguida, começará a funcionar quando a sub-rede for configurada mais tarde. Esta flexibilidade permite que os administradores que não têm acesso à conta Cosmos e recursos de rede virtual tornem as suas configurações independentes umas das outras.

### <a name="create-a-new-cosmos-account-and-connect-it-to-a-back-end-subnet-for-a-new-virtual-network"></a>Crie uma nova conta Cosmos e conecte-a a uma sub-rede traseira para uma nova rede virtual

Neste exemplo, a rede virtual e a sub-rede são criadas com pontos finais de serviço ativados para ambos quando são criados.

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

### <a name="connect-and-configure-a-cosmos-account-to-a-back-end-subnet-independently"></a>Conecte e configuure uma conta Cosmos para uma sub-rede de extremidade traseira independentemente

Esta amostra destina-se a mostrar como ligar uma conta Azure Cosmos a uma nova rede virtual existente onde a sub-rede ainda não está configurada para os pontos finais de serviço. Isto é feito usando o `--ignore-missing-vnet-service-endpoint` parâmetro. Isto permite que a configuração da conta Cosmos seja completada sem erro antes da configuração da sub-rede da rede virtual estar completa. Uma vez concluída a configuração da sub-rede, a conta Cosmos será então acessível através da sub-rede configurada.

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

## <a name="port-range-when-using-direct-mode"></a>Gama de portas ao utilizar o modo direto

Quando estiver a utilizar pontos finais de serviço com uma conta Azure Cosmos através de uma ligação de modo direto, tem de se certificar de que a faixa da porta TCP está aberta entre 10000 e 20000.

## <a name="migrating-from-an-ip-firewall-rule-to-a-virtual-network-acl"></a><a id="migrate-from-firewall-to-vnet"></a>Migrando de uma regra de firewall IP para uma rede virtual ACL

Para migrar uma conta DB da Azure Cosmos de usar regras de firewall IP para usar pontos finais de serviço de rede virtual, utilize os seguintes passos.

Depois de uma conta DB da Azure Cosmos ser configurada para um ponto final de serviço para uma sub-rede, os pedidos dessa sub-rede são enviados para Azure Cosmos DB com informações de rede virtual e fonte de sub-rede em vez de um endereço IP público de origem. Estes pedidos deixarão de corresponder a um filtro IP configurado na conta DB do Azure Cosmos, razão pela qual são necessários os seguintes passos para evitar o tempo de inatividade.

Antes de prosseguir, ative o ponto de terminação do serviço Azure Cosmos DB na rede virtual e na sub-rede utilizando o passo acima indicado em "Ativar o ponto final de serviço para uma sub-rede existente de uma rede virtual".

1. Obtenha informações de rede virtual e sub-redes:

   ```powershell
   $resourceGroupName = "myResourceGroup"
   $accountName = "mycosmosaccount"
   $vnetName = "myVnet"
   $subnetName = "mySubnet"

   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Prepare um novo objeto de regra de Rede Virtual para a conta DB da Azure Cosmos:

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Atualize a conta DB da Azure Cosmos para permitir o acesso ao ponto final do serviço a partir da sub-rede:

   ```powershell
   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Repita os passos anteriores para todas as contas DB do Azure Cosmos acedidas a partir da sub-rede.

1. Remova a regra de firewall IP para a sub-rede das regras de Firewall da conta Azure Cosmos.

## <a name="next-steps"></a>Passos seguintes

* Para configurar uma firewall para Azure Cosmos DB, consulte o artigo de suporte da [Firewall.](firewall-support.md)
