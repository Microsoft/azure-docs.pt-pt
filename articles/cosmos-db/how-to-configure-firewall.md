---
title: Configurar um firewall IP para sua conta de Azure Cosmos DB
description: Saiba como configurar as políticas de controle de acesso IP para o suporte de firewall em contas de banco de dados Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/25/2019
ms.author: mjbrown
ms.openlocfilehash: 0b8ad6c5addbff293e9f7e9b8af6ed34d4dd274b
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494882"
---
# <a name="configure-ip-firewall-in-azure-cosmos-db"></a>Configurar o firewall IP no Azure Cosmos DB

Você pode proteger os dados armazenados em sua conta do Azure Cosmos DB usando firewalls de IP. O Azure Cosmos DB dá suporte a controles de acesso baseados em IP para suporte de firewall de entrada. Você pode definir um firewall IP na conta de Azure Cosmos DB usando uma das seguintes maneiras:

* Da portal do Azure
* Declarativamente usando um modelo de Azure Resource Manager
* Programaticamente por meio do CLI do Azure ou Azure PowerShell atualizando a propriedade **ipRangeFilter**

## <a id="configure-ip-policy"></a>Configurar um firewall IP usando o portal do Azure

Para definir a política de controle de acesso IP no portal do Azure, acesse a página da conta do Azure Cosmos DB e selecione **Firewall e redes virtuais** no menu de navegação. Altere o valor **de permitir acesso de** para **redes selecionadas**e, em seguida, selecione **salvar**. 

![Captura de tela mostrando como abrir a página firewall no portal do Azure](./media/how-to-configure-firewall/azure-portal-firewall.png)

Quando o controle de acesso IP é ativado, o portal do Azure fornece a capacidade de especificar endereços IP, intervalos de endereços IP e comutadores. Os comutadores permitem o acesso a outros serviços do Azure e ao portal do Azure. As seções a seguir fornecem detalhes sobre essas opções.

> [!NOTE]
> Depois de habilitar uma política de controle de acesso IP para sua conta de Azure Cosmos DB, todas as solicitações para sua conta de Azure Cosmos DB de computadores fora da lista de endereços IP permitidos são rejeitadas. A navegação pelos recursos de Azure Cosmos DB do portal também é bloqueada para garantir a integridade do controle de acesso.

### <a name="allow-requests-from-the-azure-portal"></a>Permitir solicitações do portal do Azure

Quando você habilita uma política de controle de acesso IP programaticamente, precisa adicionar o endereço IP para o portal do Azure à propriedade **ipRangeFilter** para manter o acesso. Os endereços IP do portal são:

|Região|Endereço IP|
|------|----------|
|Alemanha|51.4.229.218|
|China|139.217.8.252|
|US Gov|52.244.48.71|
|Todas as outras regiões|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|

Você pode habilitar o acesso ao portal do Azure selecionando a opção **permitir acesso de portal do Azure** , conforme mostrado na seguinte captura de tela: 

![Captura de tela mostrando como habilitar o acesso a portal do Azure](./media/how-to-configure-firewall/enable-azure-portal.png)

### <a name="allow-requests-from-global-azure-datacenters-or-other-sources-within-azure"></a>Permitir solicitações de data centers globais do Azure ou outras fontes no Azure

Se você acessar sua conta de Azure Cosmos DB de serviços que não fornecem um IP estático (por exemplo, Azure Stream Analytics e Azure Functions), ainda poderá usar o firewall de IP para limitar o acesso. Para permitir o acesso à conta de Azure Cosmos DB de tais serviços, adicione o endereço IP 0.0.0.0 à lista de endereços IP permitidos. O endereço 0.0.0.0 restringe as solicitações para sua conta de Azure Cosmos DB do intervalo de IP do datacenter do Azure. Essa configuração não permite o acesso de nenhum outro intervalo de IP à sua conta de Azure Cosmos DB.

> [!NOTE]
> Esta opção configura o firewall para permitir todas as solicitações do Azure, incluindo solicitações das assinaturas de outros clientes implantados no Azure. A lista de IPs permitidas por essa opção é ampla, portanto, ela limita a eficácia de uma política de firewall. Use esta opção somente se suas solicitações não se originam de IPs estáticos ou sub-redes em redes virtuais. A escolha dessa opção permite automaticamente o acesso do portal do Azure porque o portal do Azure é implantado no Azure.

Você pode habilitar o acesso ao portal do Azure selecionando a opção **aceitar conexões de dentro de data centers públicos do Azure** , conforme mostrado na seguinte captura de tela: 

![Captura de tela mostrando como abrir a página firewall no portal do Azure](./media/how-to-configure-firewall/enable-azure-services.png)

### <a name="requests-from-your-current-ip"></a>Solicitações de seu IP atual

Para simplificar o desenvolvimento, o portal do Azure ajuda a identificar e adicionar o IP do computador cliente à lista de permissões. Os aplicativos que executam seu computador podem acessar sua conta de Azure Cosmos DB. 

O portal detecta automaticamente o endereço IP do cliente. Pode ser o endereço IP do cliente do seu computador ou o endereço IP do seu gateway de rede. Certifique-se de remover esse endereço IP antes de fazer suas cargas de trabalho para produção. 

Para adicionar o IP atual à lista de IPs, selecione **Adicionar meu IP atual**. Em seguida, selecione **Guardar**.

![Captura de tela mostrando como definir as configurações de firewall para o IP atual](./media/how-to-configure-firewall/enable-current-ip.png)

### <a name="requests-from-cloud-services"></a>Solicitações de serviços de nuvem

No Azure, os serviços de nuvem são uma maneira comum de hospedar a lógica de serviço de camada intermediária usando Azure Cosmos DB. Para habilitar o acesso à sua conta do Azure Cosmos DB de um serviço de nuvem, você deve adicionar o endereço IP público do serviço de nuvem à lista de permissões de endereços IP associados à sua conta do Azure Cosmos DB Configurando [a política de controle de acesso de IP](#configure-ip-policy). Isso garante que todas as instâncias de função dos serviços de nuvem tenham acesso à sua conta do Azure Cosmos DB. 

Você pode recuperar endereços IP para seus serviços de nuvem no portal do Azure, conforme mostrado na seguinte captura de tela:

![Captura de tela mostrando o endereço IP público para um serviço de nuvem exibido no portal do Azure](./media/how-to-configure-firewall/public-ip-addresses.png)

Quando você expandir seu serviço de nuvem adicionando instâncias de função, essas novas instâncias terão acesso automaticamente à conta de Azure Cosmos DB, pois fazem parte do mesmo serviço de nuvem.

### <a name="requests-from-virtual-machines"></a>Solicitações de máquinas virtuais

Você também pode usar [máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/) ou [conjuntos de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) para hospedar serviços de camada intermediária usando Azure Cosmos DB. Para configurar sua conta de Cosmos DB de forma que ela permita o acesso de máquinas virtuais, você deve configurar o endereço IP público da máquina virtual e/ou o conjunto de dimensionamento de máquinas virtuais como um dos endereços IP permitidos para sua conta de Azure Cosmos DB Configurando [ a política de controle de acesso de IP](#configure-ip-policy). 

Você pode recuperar endereços IP para máquinas virtuais no portal do Azure, conforme mostrado na seguinte captura de tela:

![Captura de tela mostrando um endereço IP público para uma máquina virtual exibida no portal do Azure](./media/how-to-configure-firewall/public-ip-addresses-dns.png)

Quando você adiciona instâncias de máquina virtual ao grupo, elas recebem automaticamente o acesso à sua conta de Azure Cosmos DB.

### <a name="requests-from-the-internet"></a>Solicitações da Internet

Quando você acessa sua conta do Azure Cosmos DB de um computador na Internet, o endereço IP do cliente ou o intervalo de endereços IP do computador deve ser adicionado à lista de endereços IP permitidos para sua conta.

## <a id="configure-ip-firewall-arm"></a>Configurar um firewall de IP usando um modelo do Resource Manager

Para configurar o controle de acesso à sua conta do Azure Cosmos DB, verifique se o modelo do Resource Manager especifica o atributo **ipRangeFilter** com uma lista de intervalos de IP permitidos. Se estiver configurando o firewall IP para uma conta do cosmos `locations` já implantada, verifique se a matriz corresponde ao que está implantado atualmente. Não é possível modificar simultaneamente `locations` a matriz e outras propriedades. Para obter mais informações e exemplos de modelos do ARM para Azure Cosmos DB consulte [Azure Resource Manager modelos para Azure Cosmos DB](resource-manager-samples.md)

```json
{
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "name": "[variables('accountName')]",
  "apiVersion": "2016-03-31",
  "location": "[parameters('location')]",
  "kind": "GlobalDocumentDB",
  "properties": {
    "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
    "locations": "[variables('locations')]",
    "databaseAccountOfferType": "Standard",
    "enableAutomaticFailover": "[parameters('automaticFailover')]",
    "enableMultipleWriteLocations": "[parameters('multipleWriteLocations')]",
    "ipRangeFilter":"183.240.196.255,104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
  }
}
```

## <a id="configure-ip-firewall-cli"></a>Configurar uma política de controle de acesso IP usando o CLI do Azure

O comando a seguir mostra como criar uma conta de Azure Cosmos DB com o controle de acesso IP: 

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

Para atualizar as configurações de firewall para uma conta existente, execute o seguinte comando:

```azurecli-interactive
az cosmosdb update \
      --name $name \
      --resource-group $resourceGroupName \
      --ip-range-filter "183.240.196.255,104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
```

## <a id="configure-ip-firewall-ps"></a>Configurar uma política de controle de acesso IP usando o PowerShell

O script a seguir mostra como criar uma conta de Azure Cosmos DB com o controle de acesso IP:

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

## <a id="troubleshoot-ip-firewall"></a>Solucionar problemas com uma política de controle de acesso IP

Você pode solucionar problemas com uma política de controle de acesso IP usando as seguintes opções: 

### <a name="azure-portal"></a>Portal do Azure 
Ao habilitar uma política de controle de acesso IP para sua conta de Azure Cosmos DB, você bloqueia todas as solicitações para sua conta de computadores fora da lista de endereços IP permitidos. Para habilitar operações do plano de dados do portal, como a navegação de contêineres e a consulta de documentos, você precisa permitir acesso explicitamente portal do Azure usando o painel **Firewall** no Portal.

### <a name="sdks"></a>SDKs 
Quando você acessa Azure Cosmos DB recursos usando SDKs de computadores que não estão na lista de permissões, uma resposta genérica **403 Proibido** é retornada sem detalhes adicionais. Verifique a lista de IP permitida para sua conta e certifique-se de que a configuração de política correta seja aplicada à sua conta de Azure Cosmos DB. 

### <a name="source-ips-in-blocked-requests"></a>IPs de origem em solicitações bloqueadas
Habilite o log de diagnóstico em sua conta de Azure Cosmos DB. Esses logs mostram cada solicitação e resposta. As mensagens relacionadas ao firewall são registradas com um código de retorno 403. Ao filtrar essas mensagens, você pode ver os IPs de origem para as solicitações bloqueadas. Consulte [Azure Cosmos DB log de diagnóstico](logging.md).

### <a name="requests-from-a-subnet-with-a-service-endpoint-for-azure-cosmos-db-enabled"></a>Solicitações de uma sub-rede com um ponto de extremidade de serviço para Azure Cosmos DB habilitadas
As solicitações de uma sub-rede em uma rede virtual que tem um ponto de extremidade de serviço para Azure Cosmos DB habilitadas enviam a rede virtual e a identidade de sub-rede para Azure Cosmos DB contas. Essas solicitações não têm o IP público da origem, portanto, os filtros IP as rejeitam. Para permitir o acesso de sub-redes específicas em redes virtuais, adicione uma lista de controle de acesso conforme descrito em [como configurar a rede virtual e o acesso baseado em sub-rede para sua conta de Azure Cosmos DB](how-to-configure-vnet-service-endpoint.md). Pode levar até 15 minutos para que as regras de firewall sejam aplicadas.


## <a name="next-steps"></a>Passos Seguintes

Para configurar um ponto de extremidade de serviço de rede virtual para sua conta de Azure Cosmos DB, consulte os seguintes artigos:

* [Controle de acesso de rede virtual e sub-rede para sua conta de Azure Cosmos DB](vnet-service-endpoint.md)
* [Configurar a rede virtual e o acesso baseado em sub-rede para sua conta de Azure Cosmos DB](how-to-configure-vnet-service-endpoint.md)

