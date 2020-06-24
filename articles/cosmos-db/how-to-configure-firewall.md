---
title: Configure uma firewall IP para a sua conta DB Azure Cosmos
description: Saiba como configurar políticas de controlo de acesso IP para suporte a firewall nas contas do Azure Cosmos.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: b1f1ac9e7c5d44fd5cf6e7d692a71f5cf34b48df
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85261686"
---
# <a name="configure-ip-firewall-in-azure-cosmos-db"></a>Configure firewall IP em Azure Cosmos DB

Pode proteger os dados armazenados na sua conta DB Azure Cosmos utilizando firewalls IP. O Azure Cosmos DB suporta controlos de acesso baseados em IP para suporte a firewall de entrada. Pode configurar uma firewall IP na conta DB do Azure Cosmos utilizando uma das seguintes formas:

* No portal do Azure
* De forma declarativa com um modelo do Azure Resource Manager
* Programáticamente através do Azure CLI ou Azure PowerShell atualizando a propriedade **ipRangeFilter**

## <a name="configure-an-ip-firewall-by-using-the-azure-portal"></a><a id="configure-ip-policy"></a>Configure uma firewall IP utilizando o portal Azure

Para definir a política de controlo de acesso IP no portal Azure, vá à página de conta DB do Azure Cosmos e selecione **Firewall e redes virtuais** no menu de navegação. Altere o **acesso do Valor** para as redes **Selecionadas**e, em seguida, selecione **Guardar**.

:::image type="content" source="./media/how-to-configure-firewall/azure-portal-firewall.png" alt-text="Screenshot mostrando como abrir a página firewall no portal Azure":::

Quando o controlo de acesso IP é ligado, o portal Azure fornece a capacidade de especificar endereços IP, intervalos de endereços IP e comutadores. Os switches permitem o acesso a outros serviços Azure e ao portal Azure. As seguintes secções dão detalhes sobre estes interruptores.

> [!NOTE]
> Depois de ativar uma política de controlo de acesso IP para a sua conta DB Azure Cosmos, todos os pedidos para a sua conta DB Azure Cosmos de máquinas fora da lista permitida de intervalos de endereços IP são rejeitados. A navegação dos recursos DB do Azure Cosmos a partir do portal também está bloqueada para garantir a integridade do controlo de acessos.

### <a name="allow-requests-from-the-azure-portal"></a>Permitir pedidos a partir do portal Azure

Quando ativa uma política de controlo de acesso IP programáticamente, precisa de adicionar o endereço IP do portal Azure à propriedade **ipRangeFilter** para manter o acesso. Os endereços IP do portal são:

|Região|Endereço IP|
|------|----------|
|Alemanha|51.4.229.218|
|China|139.217.8.252|
|US Gov|52.244.48.71|
|Todas as outras regiões|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|

Pode ativar pedidos para aceder ao portal Azure selecionando a opção **Do portal Azure,** como mostra a seguinte imagem:

:::image type="content" source="./media/how-to-configure-firewall/enable-azure-portal.png" alt-text="Screenshot mostrando como ativar o acesso ao portal Azure":::

### <a name="allow-requests-from-global-azure-datacenters-or-other-sources-within-azure"></a>Permitir pedidos de datacenters globais do Azure ou de outras origens no Azure

Se aceder à sua conta DB Azure Cosmos a partir de serviços que não fornecem um IP estático (por exemplo, Azure Stream Analytics e Azure Functions), ainda pode utilizar a firewall IP para limitar o acesso. Pode ativar o acesso a partir de outras fontes dentro do Azure selecionando as ligações Aceitar a partir da opção **Azure datacenters,** como mostra a seguinte imagem:

:::image type="content" source="./media/how-to-configure-firewall/enable-azure-services.png" alt-text="Screenshot mostrando como abrir a página firewall no portal Azure":::

Quando ativa esta opção, o endereço IP `0.0.0.0` é adicionado à lista de endereços IP permitidos. O `0.0.0.0` endereço IP restringe os pedidos à sua conta DB Azure Cosmos a partir da gama IP do datacenter Azure. Esta definição não permite o acesso de nenhum outro intervalo de IP à conta do Azure Cosmos DB.

> [!NOTE]
> Esta opção configura a firewall para permitir todos os pedidos da Azure, incluindo pedidos das subscrições de outros clientes implantados no Azure. A lista de IPs permitida por esta opção é ampla, pelo que limita a eficácia de uma política de firewall. Utilize esta opção apenas se os seus pedidos não agem de IPs estáticos ou sub-redes em redes virtuais. A escolha desta opção permite automaticamente o acesso a partir do portal Azure porque o portal Azure está implantado no Azure.

### <a name="requests-from-your-current-ip"></a>Pedidos do seu IP atual

Para simplificar o desenvolvimento, o portal Azure ajuda-o a identificar e adicionar o IP da sua máquina cliente à lista permitida. As aplicações que executam a sua máquina podem então aceder à sua conta DB Azure Cosmos.

O portal deteta automaticamente o endereço IP do cliente. Pode ser o endereço IP do cliente da sua máquina, ou o endereço IP do seu portal de rede. Certifique-se de remover este endereço IP antes de levar as suas cargas de trabalho para a produção.

Para adicionar o seu IP atual à lista de IPs, **selecione Adicione o meu IP atual**. Em seguida, selecione **Guardar**.

:::image type="content" source="./media/how-to-configure-firewall/enable-current-ip.png" alt-text="Screenshot mostrando como configurar as definições de firewall para o IP atual":::

### <a name="requests-from-cloud-services"></a>Pedidos de serviços na nuvem

Em Azure, os serviços na nuvem são uma forma comum de hospedar lógica de serviço de nível médio usando Azure Cosmos DB. Para permitir o acesso à sua conta DB Azure Cosmos a partir de um serviço de nuvem, deve adicionar o endereço IP público do serviço de nuvem à lista permitida de endereços IP associados à sua conta DB Azure [Cosmos, configurando a política](#configure-ip-policy)de controlo de acesso IP. Isto garante que todas as instâncias de função dos serviços em nuvem têm acesso à sua conta DB Azure Cosmos.

Pode recuperar endereços IP para os seus serviços em nuvem no portal Azure, como mostra a seguinte imagem:

:::image type="content" source="./media/how-to-configure-firewall/public-ip-addresses.png" alt-text="Screenshot mostrando o endereço IP público para um serviço de nuvem exibido no portal Azure":::

Quando escala o seu serviço na nuvem adicionando instâncias de função, essas novas instâncias terão automaticamente acesso à conta DB do Azure Cosmos porque fazem parte do mesmo serviço na nuvem.

### <a name="requests-from-virtual-machines"></a>Pedidos de máquinas virtuais

Também pode utilizar [máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/) ou [conjuntos de escala de máquinas virtuais](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) para hospedar serviços de nível médio utilizando o Azure Cosmos DB. Para configurar a sua conta Cosmos DB de modo a permitir o acesso a partir de máquinas virtuais, deve configurar o endereço IP público da máquina virtual e/ou da balança de máquinas virtual definida como um dos endereços IP permitidos para a sua conta DB Azure [Cosmos, configurando a política](#configure-ip-policy)de controlo de acesso IP .

Pode recuperar endereços IP para máquinas virtuais no portal Azure, como mostra a seguinte imagem:

:::image type="content" source="./media/how-to-configure-firewall/public-ip-addresses-dns.png" alt-text="Screenshot mostrando um endereço IP público para uma máquina virtual exibida no portal Azure":::

Quando adicionas casos de máquina virtual ao grupo, eles automaticamente recebem acesso à tua conta DB Azure Cosmos.

### <a name="requests-from-the-internet"></a>Pedidos da internet

Ao aceder à sua conta DB Azure Cosmos a partir de um computador na internet, o endereço IP do cliente ou o intervalo de endereços IP da máquina devem ser adicionados à lista permitida de endereços IP para a sua conta.

## <a name="configure-an-ip-firewall-by-using-a-resource-manager-template"></a><a id="configure-ip-firewall-arm"></a>Configure uma firewall IP usando um modelo de Gestor de Recursos

Para configurar o controlo de acesso à sua conta DB Azure Cosmos, certifique-se de que o modelo do Gestor de Recursos especifica o atributo **ipRangeFilter** com uma lista de intervalos IP permitidos. Se configurar o IP Firewall numa conta Cosmos já implantada, certifique-se de que o `locations` array corresponde ao que está atualmente implantado. Não é possível modificar simultaneamente a `locations` matriz e outras propriedades. Para mais informações e amostras de modelos do Azure Resource Manager para Azure Cosmos DB ver, [modelos de Gestor de Recursos Azure para Azure Cosmos DB](resource-manager-samples.md)

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

## <a name="configure-an-ip-access-control-policy-by-using-the-azure-cli"></a><a id="configure-ip-firewall-cli"></a>Configure uma política de controlo de acesso IP utilizando o CLI Azure

O seguinte comando mostra como criar uma conta DB Azure Cosmos com controlo de acesso IP:

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

O seguinte script mostra como criar uma conta DB Azure Cosmos com controlo de acesso IP:

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

Ao ativar uma política de controlo de acesso IP para a sua conta DB Azure Cosmos, bloqueia todos os pedidos na sua conta a partir de máquinas fora da lista permitida de intervalos de endereços IP. Para ativar operações de plano de dados do portal, como a navegação de contentores e documentos de consulta, é necessário permitir explicitamente o acesso ao portal Azure utilizando o painel **firewall** no portal.

### <a name="sdks"></a>SDKs

Quando acede aos recursos DB da Azure Cosmos utilizando SDKs de máquinas que não estão na lista permitida, uma resposta genérica **403 Proibida** é devolvida sem detalhes adicionais. Verifique a lista IP permitida para a sua conta e certifique-se de que a configuração correta da política é aplicada na sua conta DB Azure Cosmos.

### <a name="source-ips-in-blocked-requests"></a>IPs de origem em pedidos bloqueados

Ative o registo de diagnóstico na sua conta DB Azure Cosmos. Estes registos mostram cada pedido e resposta. As mensagens relacionadas com firewall são registadas com um código de devolução 403. Ao filtrar estas mensagens, pode ver os IPs de origem para os pedidos bloqueados. Ver [registo de diagnóstico Azure Cosmos DB](logging.md).

### <a name="requests-from-a-subnet-with-a-service-endpoint-for-azure-cosmos-db-enabled"></a>Pedidos de uma sub-rede com um ponto final de serviço para Azure Cosmos DB habilitado

Pedidos de uma sub-rede numa rede virtual que tem um ponto final de serviço para a Azure Cosmos DB habilitado envia a rede virtual e a identidade da sub-rede para as contas DB da Azure Cosmos. Estes pedidos não têm o IP público da fonte, por isso os filtros IP rejeitam-nos. Para permitir o acesso a partir de sub-redes específicas em redes virtuais, adicione uma lista de controlo de acesso como delineada em [Como configurar a rede virtual e o acesso subterrâneo à sua conta DB Azure Cosmos](how-to-configure-vnet-service-endpoint.md). Pode levar até 15 minutos para que as regras de firewall se apliquem.

## <a name="next-steps"></a>Passos seguintes

Para configurar um ponto final de serviço de rede virtual para a sua conta DB Azure Cosmos, consulte os seguintes artigos:

* [Rede virtual e controlo de acesso a sub-redes para a sua conta DB Azure Cosmos](vnet-service-endpoint.md)
* [Configure a rede virtual e o acesso subterrâneo à sua conta DB Azure Cosmos](how-to-configure-vnet-service-endpoint.md)
