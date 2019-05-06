---
title: Configurar uma firewall IP para a sua conta do Azure Cosmos DB
description: Saiba como configurar políticas de controlo de acesso IP para o suporte de firewall em contas de base de dados do Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: cdf2da745cc418190f6546fffc03e2ac2c330e0e
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65068722"
---
# <a name="configure-ip-firewall-in-azure-cosmos-db"></a>Configurar a firewall do IP no Azure Cosmos DB

Pode proteger os dados armazenados na sua conta do Azure Cosmos DB ao utilizar IP firewalls. O Azure Cosmos DB suporta controlos de acesso baseado em IP para o suporte de firewall de entrada. Pode definir uma firewall do IP da conta do Azure Cosmos DB ao utilizar uma das seguintes formas:

* No portal do Azure
* Forma declarativa utilizando um modelo Azure Resource Manager
* Por meio de programação através do CLI do Azure ou do Azure PowerShell através da atualização do **ipRangeFilter** propriedade

## <a id="configure-ip-policy"></a> Configurar uma firewall IP utilizando o portal do Azure

Para definir a política de controlo de acesso IP no portal do Azure, vá para a página de conta do Azure Cosmos DB e selecione **firewalls e redes virtuais** no menu de navegação. Alteração da **autorizar o acesso** valor a **redes selecionadas**e, em seguida, selecione **guardar**. 

![Captura de ecrã que mostra como abrir a página de Firewall no portal do Azure](./media/how-to-configure-firewall/azure-portal-firewall.png)

Quando o controlo de acesso IP estiver ativado, o portal do Azure fornece a capacidade de especificar endereços IP, intervalos de endereços IP e comutadores. Comutadores de ativar o acesso a outros serviços do Azure e o portal do Azure. As secções seguintes fornecem detalhes sobre essas opções.

> [!NOTE]
> Depois de ativar uma política de controlo de acesso IP para a sua conta do Azure Cosmos DB, todos os pedidos à sua conta do Azure Cosmos DB a partir de máquinas fora da lista de intervalos de endereços IP permitidos são rejeitados. Os recursos do Azure Cosmos DB no portal de navegação também está bloqueada para assegurar a integridade de controlo de acesso.

### <a name="allow-requests-from-the-azure-portal"></a>Permitir pedidos a partir do portal do Azure

Quando ativa a uma política de controlo de acesso IP por meio de programação, precisa adicionar o endereço IP para o portal do Azure para o **ipRangeFilter** propriedade para manter o acesso. Os endereços IP portais são:

|Região|Endereço IP|
|------|----------|
|Alemanha|51.4.229.218|
|China|139.217.8.252|
|Governo dos EUA|52.244.48.71|
|Todas as outras regiões|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|

Pode ativar o acesso ao portal do Azure ao selecionar o **permitir o acesso a partir do portal do Azure** opção, conforme mostrado na captura de ecrã seguinte: 

![Captura de ecrã que mostra como ativar o acesso ao portal do Azure](./media/how-to-configure-firewall/enable-azure-portal.png)

### <a name="allow-requests-from-global-azure-datacenters-or-other-sources-within-azure"></a>Permitir pedidos a partir de datacenters globais do Azure ou de outras origens no Azure

Se aceder sua conta do Azure Cosmos DB a partir de serviços que não fornecem um IP estático (por exemplo, Azure Stream Analytics e as funções do Azure), pode usar o firewall do IP para limitar o acesso. Para permitir o acesso à conta do Azure Cosmos DB de tais serviços, adicione o endereço IP 0.0.0.0 à lista de endereços IP permitidos. O 0.0.0.0 endereço restringe a pedidos à sua conta do Azure Cosmos DB a partir do intervalo IP do datacenter do Azure. Esta definição não permite o acesso para quaisquer outros intervalos IP para a sua conta do Azure Cosmos DB.

> [!NOTE]
> Esta opção configura a firewall para permitir que todos os pedidos do Azure, incluindo pedidos de subscrições de outros clientes implementados no Azure. A lista de IPs permitidos por esta opção é grande, pelo que limita a eficácia de uma política de firewall. Utilize esta opção apenas se os pedidos não provêm de IPs estáticos ou de sub-redes em redes virtuais. Escolher esta opção automaticamente permite o acesso do portal do Azure, uma vez que o portal do Azure é implementado no Azure.

Pode ativar o acesso ao portal do Azure ao selecionar o **aceitar ligações dos datacenters do Azure público** opção, conforme mostrado na captura de ecrã seguinte: 

![Captura de ecrã que mostra como abrir a página de Firewall no portal do Azure](./media/how-to-configure-firewall/enable-azure-services.png)

### <a name="requests-from-your-current-ip"></a>Pedidos de seu IP atual

Para simplificar o desenvolvimento, o portal do Azure ajuda-o a identificar e adicione o IP do seu computador cliente à lista de permitidos. As aplicações em execução de seu computador, em seguida, podem aceder à sua conta de Azure Cosmos DB. 

O portal Deteta automaticamente o endereço IP do cliente. Poderá ser o endereço IP de cliente da sua máquina ou o endereço IP do gateway da rede. Certifique-se remover este endereço IP antes de executar as cargas de trabalho de produção. 

Para adicionar o seu IP atual para a lista de IPs, selecione **adicionar o meu IP atual**. Em seguida, selecione **Guardar**.

![Captura de ecrã que mostra um como configurar as definições de firewall para o IP atual](./media/how-to-configure-firewall/enable-current-ip.png)

### <a name="requests-from-cloud-services"></a>Pedidos de serviços cloud

No Azure, serviços cloud são uma maneira comum para hospedar a lógica do serviço de camada intermediária através da utilização do Azure Cosmos DB. Para ativar o acesso à sua conta do Azure Cosmos DB a partir de um serviço em nuvem, tem de adicionar o endereço IP público do serviço cloud à lista de permissões de endereços IP associados à sua conta do Azure Cosmos DB por [configurar a política de controlo de acesso IP](#configure-ip-policy). Isto garante que todas as instâncias de função dos serviços cloud têm acesso à sua conta do Azure Cosmos DB. 

Pode recuperar endereços IP para os seus serviços cloud no portal do Azure, conforme mostrado na captura de ecrã seguinte:

![Captura de ecrã que mostra o endereço IP público para um serviço cloud apresentado no portal do Azure](./media/how-to-configure-firewall/public-ip-addresses.png)

Ao aumentar horizontalmente o seu serviço cloud adicionando instâncias de função, essas novas instâncias têm automaticamente acesso à conta do Azure Cosmos DB, como fazem parte do mesmo serviço cloud.

### <a name="requests-from-virtual-machines"></a>Pedidos provenientes de máquinas virtuais

Também pode utilizar [máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/) ou [conjuntos de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) para serviços de camada intermediária do anfitrião ao utilizar o Azure Cosmos DB. Para configurar a sua conta do Cosmos DB, de modo que ele permite o acesso de máquinas virtuais, tem de configurar o endereço IP público da máquina virtual e/ou do dimensionamento de máquinas virtuais definido como um dos endereços IP permitidos para a sua conta do Azure Cosmos DB por [ configurar a política de controlo de acesso IP](#configure-ip-policy). 

Pode recuperar endereços IP para máquinas virtuais no portal do Azure, conforme mostrado na captura de ecrã seguinte:

![Captura de ecrã que mostra um endereço IP público para uma máquina virtual apresentado no portal do Azure](./media/how-to-configure-firewall/public-ip-addresses-dns.png)

Ao adicionar instâncias de máquina virtual ao grupo, estes recebem automaticamente acesso à sua conta do Azure Cosmos DB.

### <a name="requests-from-the-internet"></a>Pedidos a partir da internet

Quando acessa sua conta do Azure Cosmos DB a partir de um computador na internet, o endereço IP do cliente ou um intervalo de endereços IP da máquina tem de ser adicionado à lista de permissões de endereços IP para a sua conta.

## <a id="configure-ip-firewall-arm"></a>Configurar uma firewall IP utilizando um modelo do Resource Manager

Para configurar o controlo de acesso à sua conta do Azure Cosmos DB, certifique-se de que o modelo do Resource Manager Especifica os **ipRangeFilter** atributo com uma lista de intervalos IP permitidos. Por exemplo, adicione o seguinte código JSON ao seu modelo:

```json
   {
     "apiVersion": "2015-04-08",
     "type": "Microsoft.DocumentDB/databaseAccounts",
     "kind": "GlobalDocumentDB",
     "name": "[parameters('databaseAccountName')]",
     "location": "[resourceGroup().location]",
     "properties": {
       "databaseAccountOfferType": "Standard",
       "name": "[parameters('databaseAccountName')]",
       "ipRangeFilter":"183.240.196.255,104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
     }
   }
```

## <a id="configure-ip-firewall-cli"></a>Configurar uma política de controlo de acesso IP com a CLI do Azure

O comando seguinte mostra como criar uma conta do Azure Cosmos DB com o controlo de acesso IP: 

```azurecli-interactive

name="<Azure Cosmos DB account name>"
resourceGroupName="<Resource group name>"

az cosmosdb create \
  --name $name \
  --kind GlobalDocumentDB \
  --resource-group $resourceGroupName \
  --max-interval 10 \
  --max-staleness-prefix 200 \
  --ip-range-filter "183.240.196.255,104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
```

Para atualizar as definições de firewall para uma conta existente, execute o seguinte comando:

```azurecli-interactive
az cosmosdb update \
      --name $name \
      --resource-group $resourceGroupName \
      --ip-range-filter "183.240.196.255,104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
```

## <a id="configure-ip-firewall-ps"></a>Configurar uma política de controlo de acesso IP com o PowerShell

O script seguinte mostra como criar uma conta do Azure Cosmos DB com o controlo de acesso IP:

```azurepowershell-interactive

$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$locations = @(
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="East US"; "failoverPriority"=1 }
)

# Add local machine's IP address to firewall, InterfaceAlias is your Network Adapter's name
$ipRangeFilter = Get-NetIPConfiguration | Where-Object InterfaceAlias -eq "Ethernet 2" | Select-Object IPv4Address

$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy;
    "ipRangeFilter"=$ipRangeFilter
}

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a id="troubleshoot-ip-firewall"></a>Resolver problemas com uma política de controlo de acesso IP

Pode resolver problemas com uma política de controlo de acesso IP utilizando as seguintes opções: 

### <a name="azure-portal"></a>Portal do Azure 
Ao ativar uma política de controlo de acesso IP para a sua conta do Azure Cosmos DB, bloquear todos os pedidos à sua conta a partir de máquinas fora da lista de intervalos de endereços IP permitidos. Para ativar as operações do plano de dados portal, como navegação nos contentores e consulta de documentos, tem de permitir explicitamente o acesso ao portal do Azure utilizando o **Firewall** painel no portal.

### <a name="sdks"></a>SDKs 
Quando acede a recursos do Azure Cosmos DB através de SDKs de máquinas que não estejam na lista de permitidos, um genérico **403 Proibido** resposta é devolvida com sem detalhes adicionais. Verifique se a lista IP permitida para a sua conta e certifique-se de que a configuração da política correta é aplicada à sua conta do Azure Cosmos DB. 

### <a name="source-ips-in-blocked-requests"></a>IPs de origem em pedidos bloqueados
Ative o registo de diagnóstico na sua conta do Azure Cosmos DB. Estes registos mostram cada solicitação e resposta. As mensagens relacionadas com a firewall são registadas com um código de retorno 403. Ao filtrar essas mensagens, pode ver os IPs de origem para os pedidos bloqueados. Ver [registo de diagnósticos do Azure Cosmos DB](logging.md).

### <a name="requests-from-a-subnet-with-a-service-endpoint-for-azure-cosmos-db-enabled"></a>Pedidos a partir de uma sub-rede com um ponto final de serviço para o Azure Cosmos DB ativada
Pedidos a partir de uma sub-rede numa rede virtual que tenha um ponto de extremidade de serviço para o Azure Cosmos DB ativada envia a identidade de rede e sub-rede virtual para contas do Azure Cosmos DB. Estes pedidos não tem o IP público da origem, para que os filtros de IP rejeitá-las. Para permitir o acesso a partir de sub-redes específicas nas redes virtuais, adicione uma lista de controlo de acesso, conforme descrito na [como configurar a rede virtual e o acesso com base na sub-rede para a sua conta do Azure Cosmos DB](how-to-configure-vnet-service-endpoint.md). Pode demorar até 15 minutos para as regras de firewall aplicar.


## <a name="next-steps"></a>Passos Seguintes

Para configurar um ponto de extremidade de serviço de rede virtual para a sua conta do Azure Cosmos DB, veja os artigos seguintes:

* [Virtual o controlo de acesso de rede e sub-rede para a sua conta do Azure Cosmos DB](vnet-service-endpoint.md)
* [Configurar a rede virtual e o acesso com base na sub-rede para a sua conta do Azure Cosmos DB](how-to-configure-vnet-service-endpoint.md)

