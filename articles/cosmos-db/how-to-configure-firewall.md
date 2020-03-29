---
title: Configure uma firewall IP para a sua conta Azure Cosmos DB
description: Saiba como configurar as políticas de controlo de acesso IP para suporte a firewall nas contas do Azure Cosmos.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: 1c24782285ac9b06d5499351eebe1693ade07297
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78162949"
---
# <a name="configure-ip-firewall-in-azure-cosmos-db"></a>Configure firewall IP em Azure Cosmos DB

Pode proteger os dados armazenados na sua conta Azure Cosmos DB utilizando firewalls IP. A Azure Cosmos DB suporta controlos de acesso baseados em IP para suporte de firewall de entrada. Pode configurar uma firewall IP na conta Azure Cosmos DB utilizando uma das seguintes formas:

* No portal do Azure
* De forma declarativa com um modelo do Azure Resource Manager
* Programáticamente através do Azure CLI ou Azure PowerShell atualizando a propriedade **ipRangeFilter**

## <a name="configure-an-ip-firewall-by-using-the-azure-portal"></a><a id="configure-ip-policy"></a>Configure uma firewall IP utilizando o portal Azure

Para definir a política de controlo de acesso IP no portal Azure, aceda à página de conta Do Azure Cosmos DB e selecione **Firewall e redes virtuais** no menu de navegação. Altere o **acesso permitir do** valor para as redes **Selecionadas**e, em seguida, selecione **Guardar**.

![Screenshot mostrando como abrir a página Firewall no portal Azure](./media/how-to-configure-firewall/azure-portal-firewall.png)

Quando o controlo de acesso IP é ligado, o portal Azure fornece a capacidade de especificar endereços IP, intervalos de endereçoip e interruptores. Os interruptores permitem o acesso a outros serviços Azure e ao portal Azure. As seguintes secções dão detalhes sobre estes interruptores.

> [!NOTE]
> Depois de ativar uma política de controlo de acesso IP para a sua conta Azure Cosmos DB, todos os pedidos para a sua conta Azure Cosmos DB de máquinas fora da lista permitida de intervalos de endereços IP são rejeitados. A navegação nos recursos do Azure Cosmos DB a partir do portal também está bloqueada para garantir a integridade do controlo de acesso.

### <a name="allow-requests-from-the-azure-portal"></a>Permitir pedidos do portal Azure

Quando ativa uma política de controlo de acesso IP programáticamente, é necessário adicionar o endereço IP do portal Azure à propriedade **ipRangeFilter** para manter o acesso. Os endereços IP do portal são:

|Região|Endereço IP|
|------|----------|
|Alemanha|51.4.229.218|
|China|139.217.8.252|
|US Gov|52.244.48.71|
|Todas as outras regiões|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|

Pode ativar pedidos de acesso ao portal Azure selecionando a opção Permitir o acesso a partir da opção **portal Azure,** como mostra a seguinte imagem:

![Screenshot mostrando como ativar o acesso ao portal Azure](./media/how-to-configure-firewall/enable-azure-portal.png)

### <a name="allow-requests-from-global-azure-datacenters-or-other-sources-within-azure"></a>Permitir pedidos de datacenters globais do Azure ou de outras origens no Azure

Se aceder à sua conta Azure Cosmos DB a partir de serviços que não fornecem um IP estático (por exemplo, Azure Stream Analytics e Funções Azure), ainda pode utilizar a firewall IP para limitar o acesso. Pode ativar o acesso a partir de outras fontes dentro do Azure selecionando as ligações Accept a partir da opção Decentros de **Dados Azure,** como mostra a seguinte imagem:

![Screenshot mostrando como abrir a página Firewall no portal Azure](./media/how-to-configure-firewall/enable-azure-services.png)

Quando ativa esta opção, `0.0.0.0` o endereço IP é adicionado à lista de endereços IP permitidos. O `0.0.0.0` endereço IP restringe os pedidos à sua conta Azure Cosmos DB da gama IP do Centro de Dados Azure. Esta definição não permite o acesso de nenhum outro intervalo de IP à conta do Azure Cosmos DB.

> [!NOTE]
> Esta opção configura a firewall para permitir todos os pedidos do Azure, incluindo pedidos das subscrições de outros clientes implantados no Azure. A lista de iPs permitida por esta opção é ampla, pelo que limita a eficácia de uma política de firewall. Utilize esta opção apenas se os seus pedidos não forem originados de IPs estáticos ou subredes em redes virtuais. A escolha desta opção permite automaticamente o acesso do portal Azure porque o portal Azure está implantado no Azure.

### <a name="requests-from-your-current-ip"></a>Pedidos do seu IP atual

Para simplificar o desenvolvimento, o portal Azure ajuda-o a identificar e adicionar o IP da sua máquina cliente à lista permitida. As aplicações que executam a sua máquina podem então aceder à sua conta Azure Cosmos DB.

O portal deteta automaticamente o endereço IP do cliente. Pode ser o endereço IP do cliente da sua máquina, ou o endereço IP da sua porta de entrada de rede. Certifique-se de remover este endereço IP antes de levar as suas cargas de trabalho à produção.

Para adicionar o seu IP atual à lista de IPs, selecione **Adicionar o meu IP atual**. Em seguida, selecione **Guardar**.

![Screenshot mostrando como configurar as definições de firewall para o IP atual](./media/how-to-configure-firewall/enable-current-ip.png)

### <a name="requests-from-cloud-services"></a>Pedidos de serviços na nuvem

Em Azure, os serviços na nuvem são uma forma comum de hospedar a lógica de serviço de nível médio usando o Azure Cosmos DB. Para permitir o acesso à sua conta Azure Cosmos DB a partir de um serviço na nuvem, deve adicionar o endereço IP público do serviço na nuvem à lista permitida de endereços IP associados à sua conta Azure Cosmos DB [configurando a política](#configure-ip-policy)de controlo de acesso IP. Isto garante que todos os exemplos de serviços na nuvem têm acesso à sua conta Azure Cosmos DB.

Pode recuperar endereços IP para os seus serviços na nuvem no portal Azure, como mostra a seguinte imagem:

![Screenshot mostrando o endereço IP público para um serviço na nuvem exibido no portal Azure](./media/how-to-configure-firewall/public-ip-addresses.png)

Ao escalar o seu serviço na nuvem adicionando exemplos de papéis, esses novos casos terão automaticamente acesso à conta Azure Cosmos DB porque fazem parte do mesmo serviço na nuvem.

### <a name="requests-from-virtual-machines"></a>Pedidos de máquinas virtuais

Também pode utilizar [máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/) ou [conjuntos de escala](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) de máquinas virtuais para hospedar serviços de nível médio utilizando o Azure Cosmos DB. Para configurar a sua conta Cosmos DB de modo a permitir o acesso a partir de máquinas virtuais, deve configurar o endereço IP público da máquina virtual e/ou da escala de máquina virtual definida como um dos endereços IP permitidos para a sua conta Azure Cosmos DB [configurando a política](#configure-ip-policy)de controlo de acesso IP .

Pode recuperar endereços IP para máquinas virtuais no portal Azure, como mostra a seguinte imagem:

![Screenshot mostrando um endereço IP público para uma máquina virtual exibida no portal Azure](./media/how-to-configure-firewall/public-ip-addresses-dns.png)

Quando adicionas casos de máquinas virtuais ao grupo, eles automaticamente recebem acesso à sua conta Azure Cosmos DB.

### <a name="requests-from-the-internet"></a>Pedidos da internet

Quando aceder à sua conta Azure Cosmos DB a partir de um computador na internet, o endereço IP do cliente ou o intervalo de endereçoIP da máquina devem ser adicionados à lista permitida de endereços IP para a sua conta.

## <a name="configure-an-ip-firewall-by-using-a-resource-manager-template"></a><a id="configure-ip-firewall-arm"></a>Configure uma firewall IP usando um modelo de Gestor de Recursos

Para configurar o controlo de acesso à sua conta Azure Cosmos DB, certifique-se de que o modelo do Gestor de Recursos especifica o atributo **ipRangeFilter** com uma lista de gamas IP permitidas. Se configurar o IP Firewall para uma conta `locations` Cosmos já implantada, certifique-se de que a matriz corresponde ao que está atualmente implantado. Não é possível modificar simultaneamente a `locations` matriz e outras propriedades. Para mais informações e amostras de modelos de Gestor de Recursos Azure para a Azure Cosmos DB ver, [modelos de Gestor de Recursos Azure para Azure Cosmos DB](resource-manager-samples.md)

```json
{
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "name": "[variables('accountName')]",
  "apiVersion": "2019-08-01",
  "location": "[parameters('location')]",
  "kind": "GlobalDocumentDB",
  "properties": {
    "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
    "locations": "[variables('locations')]",
    "databaseAccountOfferType": "Standard",
    "enableAutomaticFailover": "[parameters('automaticFailover')]",
    "ipRangeFilter":"40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
  }
}
```

## <a name="configure-an-ip-access-control-policy-by-using-the-azure-cli"></a><a id="configure-ip-firewall-cli"></a>Configure uma política de controlo de acesso IP utilizando o Azure CLI

O seguinte comando mostra como criar uma conta Azure Cosmos DB com controlo de acesso IP:

```azurecli-interactive
# Create a Cosmos DB account with default values and IP Firewall enabled
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
ipRangeFilter='192.168.221.17,183.240.196.255,40.76.54.131'

# Make sure there are no spaces in the comma-delimited list of IP addresses or CIDR ranges.
az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --locations regionName='East US 2' failoverPriority=1 isZoneRedundant=False \
    --ip-range-filter $ipRangeFilter
```

## <a name="configure-an-ip-access-control-policy-by-using-powershell"></a><a id="configure-ip-firewall-ps"></a>Configure uma política de controlo de acesso IP utilizando o PowerShell

O seguinte guião mostra como criar uma conta Azure Cosmos DB com controlo de acesso IP:

```azurepowershell-interactive
# Create a Cosmos DB account with default values and IP Firewall enabled
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$ipRangeFilter = "192.168.221.17,183.240.196.255,40.76.54.131"

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0; "isZoneRedundant"=False },
    @{ "locationName"="East US 2"; "failoverPriority"=1, "isZoneRedundant"=False }
)

# Make sure there are no spaces in the comma-delimited list of IP addresses or CIDR ranges.
$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "ipRangeFilter"=$ipRangeFilter
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a name="troubleshoot-issues-with-an-ip-access-control-policy"></a><a id="troubleshoot-ip-firewall"></a>Problemas de resolução de problemas com uma política de controlo de acesso ip

Pode resolver problemas com uma política de controlo de acesso IP utilizando as seguintes opções:

### <a name="azure-portal"></a>Portal do Azure

Ao ativar uma política de controlo de acesso IP para a sua conta Azure Cosmos DB, bloqueia todos os pedidos para a sua conta a partir de máquinas fora da lista permitida de intervalos de endereços IP. Para permitir operações de planode dados do portal, como contentores de navegação e documentos de consulta, é necessário permitir explicitamente o acesso do portal Azure utilizando o painel **firewall** no portal.

### <a name="sdks"></a>SDKs

Quando acede aos recursos DB da Azure Cosmos utilizando SDKs de máquinas que não estão na lista permitida, uma resposta genérica **403 Proibida** é devolvida sem detalhes adicionais. Verifique a lista ip permitida para a sua conta e certifique-se de que a configuração correta da política é aplicada na sua conta Azure Cosmos DB.

### <a name="source-ips-in-blocked-requests"></a>IPs de origem em pedidos bloqueados

Ative o registo de diagnóstico na sua conta Azure Cosmos DB. Estes registos mostram cada pedido e resposta. As mensagens relacionadas com firewall são registadas com um código de devolução 403. Filtrando estas mensagens, pode ver os IPs de origem para os pedidos bloqueados. Ver [Extração de diagnóstico de Azure Cosmos DB](logging.md).

### <a name="requests-from-a-subnet-with-a-service-endpoint-for-azure-cosmos-db-enabled"></a>Pedidos de uma subnet com um ponto final de serviço para o Azure Cosmos DB habilitado

Os pedidos de uma subnet numa rede virtual que tem um ponto final de serviço para o Azure Cosmos DB habilitado envia a rede virtual e a identidade de subnet para contas Da BD da Azure Cosmos. Estes pedidos não têm o IP público da fonte, por isso os filtros IP rejeitam-nos. Para permitir o acesso a partir de subredes específicas em redes virtuais, adicione uma lista de controlo de acesso como delineada em Como configurar a rede virtual e o acesso baseado em subnet para a [sua conta Azure Cosmos DB](how-to-configure-vnet-service-endpoint.md). Pode levar até 15 minutos para as regras de firewall se aplicarem.

## <a name="next-steps"></a>Passos seguintes

Para configurar um ponto final de serviço de rede virtual para a sua conta Azure Cosmos DB, consulte os seguintes artigos:

* [Controlo de acesso de rede virtual e subnet para a sua conta Azure Cosmos DB](vnet-service-endpoint.md)
* [Configure a rede virtual e o acesso baseado em subnet para a sua conta Azure Cosmos DB](how-to-configure-vnet-service-endpoint.md)
