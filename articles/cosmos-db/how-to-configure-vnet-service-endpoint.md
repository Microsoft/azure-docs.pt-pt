---
title: Configurar o acesso à rede virtual para uma conta Azure Cosmos
description: Este documento descreve os passos necessários para configurar um ponto final de serviço de rede virtual para a Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1d63d21f4c49e3c7aef035208477ac9fc79f2e51
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94637188"
---
# <a name="configure-access-to-azure-cosmos-db-from-virtual-networks-vnet"></a>Configure o acesso ao Azure Cosmos DB a partir de redes virtuais (VNet)
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Pode configurar a conta Azure Cosmos para permitir o acesso apenas a partir de uma sub-rede específica de rede virtual (VNet). Ao permitir o [acesso ao](../virtual-network/virtual-network-service-endpoints-overview.md) Azure Cosmos DB na sub-rede dentro de uma rede virtual, o tráfego dessa sub-rede é enviado para a Azure Cosmos DB com a identidade da sub-rede e da Rede Virtual. Uma vez ativado o ponto final do serviço Azure Cosmos DB, pode limitar o acesso à sub-rede adicionando-o à sua conta Azure Cosmos.

Por padrão, uma conta Azure Cosmos está acessível a partir de qualquer fonte se o pedido for acompanhado de um token de autorização válido. Quando adicionar uma ou mais sub-redes dentro de VNets, apenas os pedidos originários dessas sub-redes terão uma resposta válida. Os pedidos originários de qualquer outra fonte receberão uma resposta 403 (Proibida). 

Pode configurar as contas do Azure Cosmos DB para permitirem o acesso apenas a uma sub-rede específica de uma rede virtual do Azure. Para limitar o acesso a uma conta DB da Azure Cosmos com ligações a partir de uma sub-rede numa rede virtual:

1. Ative a sub-rede para enviar a sub-rede e identidade de rede virtual para Azure Cosmos DB. Pode conseguir isso ativando um ponto final de serviço para a Azure Cosmos DB na sub-rede específica.

1. Adicione uma regra na conta DB da Azure Cosmos para especificar a sub-rede como uma fonte a partir da qual a conta pode ser acedida.

> [!NOTE]
> Quando um ponto final de serviço para a sua conta DB Azure Cosmos é ativado numa sub-rede, a fonte do tráfego que atinge a Azure Cosmos DB muda de um IP público para uma rede virtual e sub-rede. A comutação de tráfego aplica-se a qualquer conta DB Azure Cosmos que tenha acesso a partir desta sub-rede. Se as suas contas DB da Azure Cosmos tiverem uma firewall baseada em IP para permitir esta sub-rede, os pedidos da sub-rede ativada pelo serviço já não correspondem às regras de firewall IP, e são rejeitados.
>
> Para saber mais, consulte os passos descritos na [migração de uma regra de firewall IP para uma](#migrate-from-firewall-to-vnet) secção de lista de controlo de acesso à rede virtual deste artigo.

As secções seguintes descrevem como configurar um ponto final de serviço de rede virtual para uma conta DB Azure Cosmos.

## <a name="configure-a-service-endpoint-by-using-the-azure-portal"></a><a id="configure-using-portal"></a>Configure um ponto final de serviço utilizando o portal Azure

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Configurar um ponto final de serviço para uma rede virtual Azure existente e sub-rede

1. A partir da lâmina **de todos os recursos,** encontre a conta DB Azure Cosmos que você quer proteger.

1. Selecione **Firewalls e redes virtuais** a partir do menu de definições e opte por permitir o acesso a partir de **redes selecionadas**.

1. Para conceder acesso à sub-rede virtual existente, em **redes Virtuais,** selecione **Adicionar a rede virtual Azure existente**.

1. Selecione a **Subscrição** a partir da qual pretende adicionar uma rede virtual Azure. Selecione as **redes Eretivas** Azure Virtual e **Subnets** que pretende fornecer acesso à sua conta DB Azure Cosmos. Em seguida, **selecione Ativar** para ativar redes selecionadas com pontos finais de serviço para "Microsoft.AzureCosmosDB". Quando estiver completo, **selecione Add**.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png" alt-text="Selecione rede virtual e sub-rede":::

1. Depois de a conta DB Azure Cosmos ter sido ativada para acesso a partir de uma rede virtual, permitirá o tráfego a partir apenas desta sub-rede escolhida. A rede virtual e a sub-rede que adicionou devem aparecer como mostrado na seguinte imagem:

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png" alt-text="Rede virtual e sub-rede configuradas com sucesso":::

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

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png" alt-text="Selecione uma rede virtual e sub-rede para uma nova rede virtual":::

Se a sua conta DB Azure Cosmos for utilizada por outros serviços Azure, como a Azure Cognitive Search, ou tiver acesso a partir de análises de stream ou Power BI, permite o acesso selecionando **ligações aceitar ligações dentro dos centros de dados globais do Azure.**

Para garantir que tem acesso às métricas DB do Azure Cosmos a partir do portal, precisa de ativar o acesso a partir das opções **do portal Azure.** Para saber mais sobre estas opções, consulte o artigo configurar um artigo [de firewall IP.](how-to-configure-firewall.md) Depois de ativar o acesso, **selecione Guardar** para guardar as definições.

## <a name="remove-a-virtual-network-or-subnet"></a><a id="remove-vnet-or-subnet"></a>Remover uma rede virtual ou sub-rede

1. A partir da lâmina **de todos os recursos,** encontre a conta DB Azure Cosmos para a qual atribuiu pontos finais de serviço.  

1. Selecione **Firewalls e redes virtuais** a partir do menu de definições.  

1. Para remover uma regra de rede virtual ou sub-rede, selecione... ao lado da rede virtual ou sub-rede, e selecione **Remover**. 

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png" alt-text="Remover uma rede virtual":::

1. Selecione **Guardar** para aplicar as suas alterações.

## <a name="configure-a-service-endpoint-by-using-azure-powershell"></a><a id="configure-using-powershell"></a>Configure um ponto final de serviço utilizando a Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Utilize os seguintes passos para configurar um ponto final de serviço numa conta DB da Azure Cosmos utilizando a Azure PowerShell:  

1. Instale [a Azure PowerShell](/powershell/azure/install-Az-ps) e [inscreva-se em](/powershell/azure/authenticate-azureps).  

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

   > [!NOTE]
   > Quando estiver a utilizar o PowerShell ou o Azure CLI, certifique-se de especificar a lista completa de filtros IP e ACLs de rede virtual em parâmetros, e não apenas os que precisam de ser adicionados.

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

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

Aqui estão algumas perguntas frequentes sobre a configuração do acesso a partir de redes virtuais:

### <a name="are-notebooks-and-mongocassandra-shell-currently-compatible-with-virtual-network-enabled-accounts"></a>Os Cadernos e a Mongo/Cassandra Shell são atualmente compatíveis com contas de Rede Virtual ativadas?

De momento, as integrações da [concha de Mongo](https://devblogs.microsoft.com/cosmosdb/preview-native-mongo-shell/) e [da Cassandra](https://devblogs.microsoft.com/cosmosdb/announcing-native-cassandra-shell-preview/) no Cosmos DB Data Explorer, e o [serviço Jupyter Notebooks,](./cosmosdb-jupyter-notebooks.md)não são suportados com acesso VNET. Isto encontra-se atualmente em desenvolvimento ativo.

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>Posso especificar tanto o ponto final do serviço de rede virtual como a política de controlo de acesso IP numa conta Azure Cosmos? 

Pode ativar tanto o ponto final do serviço de rede virtual como uma política de controlo de acesso IP (também conhecida como firewall) na sua conta Azure Cosmos. Estas duas características são complementares e coletivamente garantem o isolamento e a segurança da sua conta Azure Cosmos. A utilização de firewall IP garante que os IPs estáticos podem aceder à sua conta. 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>Como limito o acesso à sub-rede dentro de uma rede virtual? 

Há dois passos necessários para limitar o acesso à conta Azure Cosmos a partir de uma sub-rede. Em primeiro lugar, permite que o tráfego da sub-rede leve a sua sub-rede e identidade de rede virtual para a Azure Cosmos DB. Isto é feito permitindo o ponto final de serviço para Azure Cosmos DB na sub-rede. Em seguida, é adicionar uma regra na conta Azure Cosmos especificando esta sub-rede como uma fonte a partir da qual a conta pode ser acedida.

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>Os ACLs de rede virtual e o IP Firewall rejeitarão pedidos ou ligações? 

Quando são adicionadas regras de firewall IP ou de acesso à rede virtual, apenas os pedidos de fontes permitidas obtêm respostas válidas. Outros pedidos são rejeitados com um 403 (Proibido). É importante distinguir a firewall da conta Azure Cosmos de uma firewall de nível de ligação. A fonte ainda pode ligar-se ao serviço e as próprias ligações não são rejeitadas.

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>Os meus pedidos começaram a ser bloqueados quando ativei o ponto final do serviço para a Azure Cosmos DB na sub-rede. O que aconteceu?

Uma vez que o ponto final de serviço para Azure Cosmos DB é ativado numa sub-rede, a fonte do tráfego que alcança a conta muda de IP público para rede virtual e sub-rede. Se a sua conta Azure Cosmos tiver apenas firewall baseada em IP, o tráfego da sub-rede ativada pelo serviço deixaria de corresponder às regras de firewall IP e, portanto, seria rejeitado. Revindo os passos para migrar perfeitamente de firewall baseada em IP para o controlo de acesso baseado em rede virtual.

### <a name="are-additional-azure-rbac-permissions-needed-for-azure-cosmos-accounts-with-vnet-service-endpoints"></a>São necessárias permissões adicionais do Azure RBAC para as contas da Azure Cosmos com pontos finais de serviço VNET?

Depois de adicionar os pontos finais do serviço VNet a uma conta Azure Cosmos, para efetivar quaisquer alterações nas definições da conta, precisa de ter acesso à `Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action` ação para todos os VNETs configurados na sua conta Azure Cosmos. Esta permissão é necessária porque o processo de autorização valida o acesso a recursos (como base de dados e recursos de rede virtual) antes de avaliar quaisquer propriedades.
 
A autorização valida a permissão para a ação de recursos VNet mesmo que o utilizador não especifique os ACLs VNET utilizando o Azure CLI. Atualmente, o avião de controlo da conta Azure Cosmos suporta a definição do estado completo da conta Azure Cosmos. Um dos parâmetros para as chamadas do avião de controlo `virtualNetworkRules` é. Se este parâmetro não for especificado, o CLI do Azure faz uma chamada de base de dados para recuperar o `virtualNetworkRules` valor e utiliza este valor na chamada de atualização.

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>As redes virtuais espreitadas também têm acesso à conta Azure Cosmos? 
Apenas a rede virtual e as suas sub-redes adicionadas à conta Azure Cosmos têm acesso. Os seus VNets espreitados não podem aceder à conta até que as sub-redes dentro das redes virtuais sejam adicionadas à conta.

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>Qual é o número máximo de sub-redes autorizados a aceder a uma única conta Cosmos? 
Atualmente, você pode ter no máximo 256 sub-redes permitidas para uma conta Azure Cosmos.

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>Posso permitir o acesso a partir da VPN e da Rota Expressa? 
Para aceder à conta Azure Cosmos sobre a rota Express a partir das instalações, você precisaria de ativar o observamento da Microsoft. Assim que colocar regras de firewall IP ou acesso à rede virtual, pode adicionar os endereços IP públicos utilizados para a Microsoft a espreitar na sua conta IP da Azure Cosmos para permitir o acesso dos serviços de instalações à conta Azure Cosmos. 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>Preciso de atualizar as regras dos Grupos de Segurança da Rede (NSG)? 
As regras NSG são usadas para limitar a conectividade de e para uma sub-rede com rede virtual. Quando adicionas o ponto final de serviço para a Azure Cosmos DB à sub-rede, não há necessidade de abrir a conectividade de saída em NSG para a sua conta Azure Cosmos. 

### <a name="are-service-endpoints-available-for-all-vnets"></a>Os pontos finais de serviço estão disponíveis para todos os VNets?
Não, apenas as redes virtuais do Azure Resource Manager podem ter o ponto final de serviço ativado. As redes virtuais clássicas não suportam pontos finais de serviço.

### <a name="can-i-accept-connections-from-within-public-azure-datacenters-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>Posso "Aceitar ligações de dentro dos datacenters públicos da Azure" quando o acesso ao ponto final do serviço estiver ativado para a Azure Cosmos DB?  
Isto só é necessário quando pretende que a sua conta DB Azure Cosmos seja acedida por outros serviços de primeira festa da Azure, como a fábrica de Dados Azure, a Azure Cognitive Search ou qualquer serviço que seja implantado na região de Azure.

## <a name="next-steps"></a>Passos seguintes

* Para configurar uma firewall para Azure Cosmos DB, consulte o artigo de suporte da [Firewall.](how-to-configure-firewall.md)
