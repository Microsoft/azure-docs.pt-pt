---
title: Configurar a rede virtual e o acesso com base na sub-rede para a sua conta do Azure Cosmos DB
description: Este documento descreve os passos necessários para configurar um ponto de extremidade do serviço de rede virtual para o Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: 03ec8858ee4b9323e6f7964ba02f8bb38f32d6a4
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56819008"
---
# <a name="configure-access-from-virtual-networks-vnet"></a>Configurar o acesso a partir de redes virtuais (VNet)

Pode configurar contas do Azure Cosmos DB para permitir o acesso a partir de uma sub-rede específica de uma rede virtual do Azure. Para limitar o acesso a uma conta do Azure Cosmos DB com ligações a partir de uma sub-rede numa rede virtual:
 
1. Ative a sub-rede enviar a sub-rede e a identidade de rede virtual para o Azure Cosmos DB. Pode conseguir isto ao permitir que um ponto de extremidade de serviço para o Azure Cosmos DB na sub-rede específica.

1. Adicione uma regra na conta do Azure Cosmos DB para especificar a sub-rede como uma origem a partir do qual a conta pode ser acessada.

> [!NOTE]
> Quando um ponto final de serviço para a sua conta do Azure Cosmos DB está ativado numa sub-rede, muda a origem do tráfego que atinge o Azure Cosmos DB a partir de um IP público a uma rede virtual e sub-rede. A mudança de tráfego aplica-se para qualquer conta do Azure Cosmos DB, que é acessada a partir desta sub-rede. Se as contas do Azure Cosmos DB tem um firewall baseado em IP para permitir que esta sub-rede, pedidos a partir da sub-rede habilitados para serviços já não correspondem a regras de firewall do IP e estão a ser rejeitadas. 
>
> Para obter mais informações, veja os passos descritos no [migrar a partir de uma regra de firewall do IP para uma lista de controlo de acesso de rede virtual](#migrate-from-firewall-to-vnet) seção deste artigo. 

As secções seguintes descrevem como configurar um ponto de extremidade de serviço de rede virtual para uma conta do Azure Cosmos DB.

## <a id="configure-using-portal"></a>Configurar um ponto de extremidade de serviço com o portal do Azure

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Configurar um ponto de extremidade de serviço para uma rede virtual do Azure existente e a sub-rede

1. Partir do **todos os recursos** painel, localize a conta do Azure Cosmos DB que pretende proteger.

1. Selecione **Firewalls e redes virtuais** no menu de definições e optar por permitir acesso a partir de **redes selecionadas**.

1. Para conceder acesso à sub-rede da rede virtual existente, em **redes virtuais**, selecione **adicionar rede virtual do Azure existente**.

1. Selecione o **subscrição** partir da qual pretende adicionar uma rede virtual do Azure. Selecione o Azure **redes virtuais** e **sub-redes** que deseja fornecer acesso à sua conta do Azure Cosmos DB. Em seguida, selecione **ativar** para ativar redes selecionadas com pontos finais de serviço para "Microsoft.AzureCosmosDB". Quando terminar, selecione **adicionar**. 

   ![Selecione a rede virtual e sub-rede](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)


1. Depois da conta do Azure Cosmos DB está ativada para acesso a partir de uma rede virtual, será permitido o tráfego apenas nesta escolhido sub-rede. A rede virtual e a sub-rede que adicionou devem aparecer como mostrado na captura de ecrã seguinte:

   ![rede virtual e sub-rede configurada com êxito](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> Para ativar pontos finais de serviço de rede virtual, tem as seguintes permissões de subscrição:
  * Subscrição com a rede virtual: Contribuinte de rede
  * Subscrição com a conta do Azure Cosmos DB: Contribuinte de conta do DocumentDB
  * Se a sua rede virtual e a conta do Azure Cosmos DB estiverem em diferentes subscrições, certifique-se de que a subscrição que tem a rede virtual também tem `Microsoft.DocumentDB` fornecedor de recursos registado. Para registar um fornecedor de recursos, consulte [fornecedores de recursos do Azure e tipos de](../azure-resource-manager/resource-manager-supported-services.md) artigo. 

Aqui estão as instruções para registar a subscrição no fornecedor de recursos.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Configurar um ponto de extremidade de serviço para uma nova rede virtual do Azure e uma sub-rede

1. Partir do **todos os recursos** painel, localize a conta do Azure Cosmos DB que pretende proteger.  

1. Selecione **Firewalls e redes virtuais do Azure** no menu de definições e optar por permitir acesso a partir de **redes selecionadas**.  

1. Para conceder acesso a uma nova rede virtual do Azure, em **redes virtuais**, selecione **Adicionar nova rede virtual**.  

1. Forneça os detalhes necessários para criar uma nova rede virtual e, em seguida, selecione **criar**. A sub-rede será criada com um ponto de extremidade de serviço para "Microsoft.AzureCosmosDB" ativada.

   ![Selecione uma rede virtual e uma sub-rede para uma nova rede virtual](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

Se a sua conta do Azure Cosmos DB é utilizada por outros serviços do Azure, como o Azure Search, ou que é acessada a partir de análise de Stream ou Power BI, permitir o acesso, selecionando **aceitar ligações dos datacenters do Azure público**.

Para garantir que tem acesso a métricas do Azure Cosmos DB no portal, tem de ativar **permitir o acesso a partir do portal do Azure** opções. Para saber mais sobre estas opções, consulte a [configurar uma firewall do IP](how-to-configure-firewall.md) artigo. Depois de ativar o acesso, selecione **guardar** para guardar as definições.

## <a id="remove-vnet-or-subnet"></a>Remover uma rede virtual ou sub-rede 

1. Partir do **todos os recursos** painel, a conta de localizar o Azure Cosmos DB para o qual é atribuída a pontos finais de serviço.  

2. Selecione **Firewalls e redes virtuais** no menu definições.  

3. Para remover uma rede virtual ou a regra de sub-rede, selecione **...**  ao lado de rede virtual ou sub-rede e selecione **remover**.

   ![Remover uma rede virtual](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

4.  Selecione **guardar** para aplicar as alterações.

## <a id="configure-using-powershell"></a>Configurar um ponto de extremidade de serviço com o Azure PowerShell

> [!NOTE]
> Quando estiver a utilizar o PowerShell ou a CLI do Azure, certifique-se de que especifica a lista completa de filtros IP e as ACLs de rede virtual nos parâmetros, não apenas aqueles que precisam de ser adicionados.

Utilize os seguintes passos para configurar um ponto final de serviço a uma conta do Azure Cosmos DB com o Azure PowerShell:  

1. Instale [do Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) e [iniciar sessão](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  

1. Ative o ponto de final de serviço para uma sub-rede existente de uma rede virtual.  

   ```powershell
   $rgname = "<Resource group name>"
   $vnName = "<Virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzureRmVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzureRmVirtualNetworkSubnetConfig `
    -Name $sname  `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzureRmVirtualNetwork
   ```

1. Obtenha informações de rede virtual.

   ```powershell
   $vnProp = Get-AzureRmVirtualNetwork `
     -Name $vnName `
     -ResourceGroupName $rgName
   ```

1. Obter propriedades da conta do Azure Cosmos DB, execute o seguinte cmdlet:  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. Inicialize as variáveis para utilização posterior. Configure todas as variáveis da definição de conta existente.

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

1. Atualize propriedades da conta do Azure Cosmos DB com a nova configuração ao executar os seguintes cmdlets: 

   ```powershell
   $cosmosDBProperties = @{
      databaseAccountOfferType      = $cosmosDBConfiguration.Properties.databaseAccountOfferType;
      consistencyPolicy             = $cosmosDBConfiguration.Properties.consistencyPolicy;
      ipRangeFilter                 = $cosmosDBConfiguration.Properties.ipRangeFilter;
      locations                     = $locations;
      virtualNetworkRules           = $virtualNetworkRules;
      isVirtualNetworkFilterEnabled = $True;
   }

   Set-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName `
     -Properties $CosmosDBProperties
   ```

1. Execute o seguinte comando para verificar que a sua conta do Azure Cosmos DB é atualizada com o ponto de final de serviço de rede virtual que configurou no passo anterior:

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a id="configure-using-cli"></a>Configurar um ponto de extremidade de serviço com a CLI do Azure 

1. Ative o ponto de final de serviço para uma sub-rede numa rede virtual.

1. Atualize a conta existente do Azure Cosmos DB com listas de controlo de acesso (ACLs) de sub-rede.

   ```azurecli-interactive

   name="<Azure Cosmos DB account name>"
   resourceGroupName="<Resource group name>"

   az cosmosdb update \
    --name $name \
    --resource-group $resourceGroupName \
    --enable-virtual-network true \
    --virtual-network-rules "/subscriptions/testsub/resourceGroups/testRG/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/frontend"
   ```

1. Crie uma nova conta do Azure Cosmos DB com ACLs de sub-rede.

   ```azurecli-interactive
   az cosmosdb create \
    --name $name \
    --kind GlobalDocumentDB \
    --resource-group $resourceGroupName \
    --max-interval 10 \
    --max-staleness-prefix 200 \
    --enable-virtual-network true \
    --virtual-network-rules "/subscriptions/testsub/resourceGroups/testRG/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/default"
   ```

## <a id="migrate-from-firewall-to-vnet"></a>Migrar a partir de uma regra de firewall do IP para uma ACL de rede virtual 

Utilize os seguintes passos apenas para contas do Azure Cosmos DB com regras de firewall IP existente, que permitem uma sub-rede, quando pretender utilizar a rede virtual e ACLs baseadas em sub-rede em vez de uma regra de firewall do IP.

Depois de um ponto final de serviço para uma conta do Azure Cosmos DB está ativado para uma sub-rede, as solicitações são enviadas com uma origem que contém informações de rede e sub-rede virtual em vez de um IP público. Estes pedidos não correspondem um filtro IP. Este parâmetro de origem ocorre para todas as contas do Azure Cosmos DB acedidas a partir da sub-rede com um ponto final de serviço ativado. Para impedir que o tempo de inatividade, utilize os seguintes passos:

1. Obter propriedades da conta do Azure Cosmos DB, execute o seguinte cmdlet:

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. Inicialize as variáveis para utilizá-los mais tarde. Configure todas as variáveis da definição de conta existente. Adicionar a rede virtual ACL a todas as contas do Azure Cosmos DB que está sendo acessadas a partir da sub-rede com `ignoreMissingVNetServiceEndpoint` sinalizador.

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

1. Atualize propriedades da conta do Azure Cosmos DB com a nova configuração ao executar os seguintes cmdlets:

   ```powershell
   $cosmosDBProperties = @{
      databaseAccountOfferType      = $cosmosDBConfiguration.Properties.databaseAccountOfferType;
      consistencyPolicy             = $cosmosDBConfiguration.Properties.consistencyPolicy;
      ipRangeFilter                 = $cosmosDBConfiguration.Properties.ipRangeFilter;
      locations                     = $locations;
      virtualNetworkRules           = $virtualNetworkRules;
      isVirtualNetworkFilterEnabled = $True;
   }

   Set-AzureRmResource `
      -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
      -ApiVersion $apiVersion `
      -ResourceGroupName $rgName `
      -Name $acctName `
      -Properties $CosmosDBProperties
   ```

1. Repita os passos 1 a 3 para todas as contas do Azure Cosmos DB que acedem a partir da sub-rede.

1.  Aguarde 15 minutos e, em seguida, atualize a sub-rede para ativar o ponto final de serviço.

1.  Ative o ponto de final de serviço para uma sub-rede existente de uma rede virtual.

    ```powershell
    $rgname= "<Resource group name>"
    $vnName = "<virtual network name>"
    $sname = "<Subnet name>"
    $subnetPrefix = "<Subnet address range>"

    Get-AzureRmVirtualNetwork `
       -ResourceGroupName $rgname `
       -Name $vnName | Set-AzureRmVirtualNetworkSubnetConfig `
       -Name $sname `
       -AddressPrefix $subnetPrefix `
       -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzureRmVirtualNetwork
    ```

1. Remova a regra de firewall do IP da sub-rede.

## <a name="next-steps"></a>Passos Seguintes

* Para configurar uma firewall para o Azure Cosmos DB, consulte a [suporte de Firewall](firewall-support.md) artigo.
