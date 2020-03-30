---
title: Configure o acesso baseado em rede virtual para uma conta Azure Cosmos
description: Este documento descreve os passos necessários para criar um ponto final de serviço de rede virtual para o Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mjbrown
ms.openlocfilehash: 442623880c1b95f3d7e038ae44832b74853d2c4a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366228"
---
# <a name="configure-access-from-virtual-networks-vnet"></a>Configure o acesso a partir de redes virtuais (VNet)

Pode configurar as contas do Azure Cosmos DB para permitirem o acesso apenas a uma sub-rede específica de uma rede virtual do Azure. Para limitar o acesso a uma conta Azure Cosmos DB com ligações de uma subnet numa rede virtual:

1. Permitir que a subnet envie a subnete e a identidade de rede virtual para o Azure Cosmos DB. Pode fazê-lo permitindo um ponto final de serviço para o Azure Cosmos DB na subnet específica.

1. Adicione uma regra na conta Azure Cosmos DB para especificar a subnet como fonte a partir da qual a conta pode ser acedida.

> [!NOTE]
> Quando um ponto final de serviço para a sua conta Azure Cosmos DB é ativado numa subnet, a fonte do tráfego que chega ao Azure Cosmos DB passa de um IP público para uma rede virtual e subnet. A troca de tráfego aplica-se a qualquer conta Azure Cosmos DB que tenha acesso a partir desta subnet. Se as suas contas Azure Cosmos DB tiverem uma firewall baseada em IP para permitir esta sub-rede, os pedidos da subnet ativada pelo serviço já não correspondem às regras de firewall IP, e são rejeitados.
>
> Para saber mais, consulte os passos delineados na [migração de uma regra de firewall IP para uma](#migrate-from-firewall-to-vnet) secção de controlo de acesso de rede virtual deste artigo.

As seguintes secções descrevem como configurar um ponto final de serviço de rede virtual para uma conta Azure Cosmos DB.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-a-service-endpoint-by-using-the-azure-portal"></a><a id="configure-using-portal"></a>Configure um ponto final de serviço utilizando o portal Azure

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Configure um ponto final de serviço para uma rede virtual e sub-rede azure existentes

1. A partir da lâmina **de todos os recursos,** encontre a conta Azure Cosmos DB que quer garantir.

1. Selecione **Firewalls e redes virtuais** a partir do menu de definições e opte por permitir o acesso a partir de **redes Selecionadas**.

1. Para garantir o acesso à subnet de uma rede virtual existente, em **redes virtuais,** selecione **Adicionar rede virtual Azure existente**.

1. Selecione a **Subscrição** a partir da qual pretende adicionar uma rede virtual Azure. Selecione as redes e **subnets** **Azure Virtual** que pretende fornecer acesso à sua conta Azure Cosmos DB. Em seguida, selecione **Enable** para ativar redes selecionadas com pontos finais de serviço para "Microsoft.AzureCosmosDB". Quando estiver completo, selecione **Adicionar**.

   ![Selecione rede virtual e sub-rede](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)

1. Depois de a conta Azure Cosmos DB estar ativada para acesso a partir de uma rede virtual, permitirá o tráfego a partir apenas desta subnet escolhida. A rede virtual e a sub-rede que adicionou devem aparecer como mostrado na seguinte imagem:

   ![Rede virtual e subnet configurada com sucesso](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> Para ativar pontos finais de serviço de rede virtual, necessita das seguintes permissões de subscrição:
>   * Subscrição com rede virtual: Colaborador da rede
>   * Subscrição com conta Azure Cosmos DB: Contribuinte de conta DocumentDB
>   * Se a sua rede virtual e a conta Azure Cosmos DB estiverem em `Microsoft.DocumentDB` subscrições diferentes, certifique-se de que a subscrição que tem rede virtual também tem fornecedor de recursos registado. Para registar um fornecedor de recursos, consulte [os fornecedores de recursos do Azure e o](../azure-resource-manager/management/resource-providers-and-types.md) artigo de tipos.

Aqui estão as instruções para registar a subscrição com o fornecedor de recursos.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Configure um ponto final de serviço para uma nova rede virtual Azure e subnet

1. A partir da lâmina **de todos os recursos,** encontre a conta Azure Cosmos DB que quer garantir.  

1. Selecione **Firewalls e redes virtuais Azure** a partir do menu de definições e opte por permitir o acesso a partir de **redes Selecionadas**.  

1. Para garantir o acesso a uma nova rede virtual Azure, em **redes virtuais,** selecione **Adicionar nova rede virtual**.  

1. Forneça os detalhes necessários para criar uma nova rede virtual e, em seguida, selecione **Criar**. A subnet será criada com um ponto final de serviço para "Microsoft.AzureCosmosDB" ativado.

   ![Selecione uma rede virtual e subnet para uma nova rede virtual](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

Se a sua conta Azure Cosmos DB for utilizada por outros serviços Azure, como a Azure Cognitive Search, ou for acedida a partir de Stream analytics ou Power BI, permite o acesso selecionando **ligações De Aceitação dentro de centros de dados azure globais**.

Para garantir que tem acesso às métricas Do DD Do Portal, é necessário permitir o acesso a partir das opções **do portal Azure.** Para saber mais sobre estas opções, consulte o Configure um artigo de [firewall IP.](how-to-configure-firewall.md) Depois de ativar o acesso, selecione **Guardar** para guardar as definições.

## <a name="remove-a-virtual-network-or-subnet"></a><a id="remove-vnet-or-subnet"></a>Remover uma rede virtual ou sub-rede

1. A partir da lâmina **de todos os recursos,** encontre a conta Azure Cosmos DB para a qual atribuiu pontos finais de serviço.  

1. Selecione **Firewalls e redes virtuais** a partir do menu de definições.  

1. Para remover uma regra de rede virtual ou sub-rede, selecione... ao lado da rede virtual ou subnet, e selecione **Remover**. **...**

   ![Remover uma rede virtual](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

1. Selecione **Guardar** para aplicar as suas alterações.

## <a name="configure-a-service-endpoint-by-using-azure-powershell"></a><a id="configure-using-powershell"></a>Configure um ponto final de serviço utilizando o Azure PowerShell

> [!NOTE]
> Quando estiver a utilizar o PowerShell ou o Azure CLI, não se esqueça de especificar a lista completa de filtros IP e ACLs de rede virtual em parâmetros, e não apenas os que precisam de ser adicionados.

Utilize os seguintes passos para configurar um ponto final de serviço para uma conta Azure Cosmos DB utilizando o Azure PowerShell:  

1. Instale [o Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) e [inscreva-se](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  

1. Ative o ponto final do serviço para uma subrede existente de uma rede virtual.  

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

1. Obtenha informações de rede virtuais.

   ```powershell
   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Prepare uma Regra de Rede Virtual Cosmos DB

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Atualizar propriedades da conta Azure Cosmos DB com a nova configuração de ponto final da Rede Virtual: 

   ```powershell
   $accountName = "<Cosmos DB account name>"

   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Execute o seguinte comando para verificar se a sua conta Azure Cosmos DB é atualizada com o ponto final do serviço de rede virtual que configurado na etapa anterior:

   ```powershell
   $account = Get-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName

   $account.IsVirtualNetworkFilterEnabled
   $account.VirtualNetworkRules
   ```

## <a name="configure-a-service-endpoint-by-using-the-azure-cli"></a><a id="configure-using-cli"></a>Configure um ponto final de serviço utilizando o Azure CLI

As contas da Azure Cosmos podem ser configuradas para pontos finais de serviço quando são criadas ou atualizadas posteriormente se a subnet já estiver configurada para os mesmos. Os pontos finais do serviço também podem ser ativados na conta Cosmos onde a subnet ainda não está configurada para eles e, em seguida, começará a funcionar quando a subnet for configurada mais tarde. Esta flexibilidade permite que os administradores que não tenham acesso à conta Cosmos e aos recursos de rede virtuais tornem as suas configurações independentes umas das outras.

### <a name="create-a-new-cosmos-account-and-connect-it-to-a-back-end-subnet-for-a-new-virtual-network"></a>Crie uma nova conta Cosmos e conecte-a a uma subnet traseira para uma nova rede virtual

Neste exemplo, a rede virtual e a sub-rede são criadas com pontos finais de serviço habilitados tanto para quando são criados.

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

### <a name="connect-and-configure-a-cosmos-account-to-a-back-end-subnet-independently"></a>Conecte e configure uma conta Cosmos para uma subnet de back end de forma independente

Esta amostra destina-se a mostrar como ligar uma conta Azure Cosmos a uma nova rede virtual existente, onde a subnet ainda não está configurada para pontos finais de serviço. Isto é feito `--ignore-missing-vnet-service-endpoint` usando o parâmetro. Isto permite que a configuração da conta Cosmos seja completada sem erros antes da configuração da subnet da rede virtual estar completa. Uma vez concluída a configuração da sub-rede, a conta Cosmos será então acessível através da sub-rede configurada.

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

## <a name="migrating-from-an-ip-firewall-rule-to-a-virtual-network-acl"></a><a id="migrate-from-firewall-to-vnet"></a>Migrar de uma regra de firewall IP para uma rede virtual ACL

Para migrar uma conta Azure Cosmos DB desde a utilização de regras de firewall IP para utilizar pontos finais de serviço de rede virtual, utilize os seguintes passos.

Depois de uma conta Azure Cosmos DB ser configurada para um ponto final de serviço para uma subnet, os pedidos dessa subnet são enviados para o Azure Cosmos DB com informações de rede virtual e fonte de sub-rede em vez de um endereço IP público de origem. Estes pedidos deixarão de corresponder a um filtro IP configurado na conta Azure Cosmos DB, razão pela qual são necessários os seguintes passos para evitar o tempo de inatividade.

Antes de prosseguir, ative o ponto final do serviço Azure Cosmos DB na rede virtual e subnet utilizando o passo acima indicado em "Habilitar o ponto final do serviço para uma subrede existente de uma rede virtual".

1. Obtenha informações de rede virtual e subnet:

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

1. Prepare um novo objeto de regra da Rede Virtual para a conta Azure Cosmos DB:

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Atualize a conta Azure Cosmos DB para permitir o acesso final do serviço a partir da subnet:

   ```powershell
   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Repita os passos anteriores para todas as contas Da Azure Cosmos DB acedidas a partir da subnet.

1. Remova a regra de firewall IP para a subnet das regras de Firewall da conta Azure Cosmos DB.

## <a name="next-steps"></a>Passos seguintes

* Para configurar uma firewall para Azure Cosmos DB, consulte o artigo de suporte da [Firewall.](firewall-support.md)
