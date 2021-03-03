---
title: Configure Azure Private Link para uma conta Azure Cosmos
description: Saiba como configurar o Azure Private Link para aceder a uma conta Azure Cosmos utilizando um endereço IP privado numa rede virtual.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/02/2021
ms.author: thweiss
ms.custom: devx-track-azurecli
ms.openlocfilehash: d21943c90e1f77bd4a43cdfd27b183df018f6cc7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101690673"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account"></a>Configure Azure Private Link para uma conta Azure Cosmos
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Ao utilizar o Azure Private Link, pode ligar-se a uma conta Azure Cosmos através de um ponto final privado. O ponto final privado é um conjunto de endereços IP privados numa sub-rede dentro da sua rede virtual. Em seguida, pode limitar o acesso a uma conta Azure Cosmos em endereços IP privados. Quando o Private Link é combinado com políticas restritas de NSG, ajuda a reduzir o risco de exfiltração de dados. Para saber mais sobre os pontos finais privados, consulte o artigo [Azure Private Link.](../private-link/private-link-overview.md)

> [!NOTE]
> O Private Link não impede que os seus pontos finais do Azure Cosmos sejam resolvidos pelo DNS público. A filtragem dos pedidos de entrada ocorre ao nível da aplicação, não ao nível do transporte ou da rede.

O Private Link permite que os utilizadores acedam a uma conta Azure Cosmos a partir da rede virtual ou de qualquer rede virtual. Os recursos mapeados para private link também são acessíveis no local através de peering privado através de VPN ou Azure ExpressRoute.

Pode ligar-se a uma conta Azure Cosmos configurada com Private Link utilizando o método de aprovação automática ou manual. Para saber mais, consulte a secção de fluxo de trabalho de [aprovação](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow) da documentação Private Link.

Este artigo descreve como configurar pontos finais privados para a loja transacional Azure Cosmos DB. Assume que está a usar o método de aprovação automática. Se estiver a utilizar a loja analítica, consulte [os pontos finais privados para o artigo da loja analítica.](analytical-store-private-endpoints.md)

## <a name="create-a-private-endpoint-by-using-the-azure-portal"></a>Criar um ponto final privado utilizando o portal Azure

Utilize os seguintes passos para criar um ponto final privado para uma conta Azure Cosmos existente utilizando o portal Azure:

1. Do painel **all resources,** escolha uma conta Azure Cosmos.

1. Selecione **As Ligações de Ponto Final Privado da** lista de definições e, em seguida, selecione Private **endpoint**:

   :::image type="content" source="./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png" alt-text="Seleções para criar um ponto final privado no portal Azure":::

1. No **Ponto final privado - Painel básico,** introduza ou selecione os seguintes detalhes:

    | Definição | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione um grupo de recursos.|
    | **Detalhes da instância** |  |
    | Name | Insira qualquer nome para o seu ponto final privado. Se este nome for tomado, crie um único. |
    |Region| Selecione a região onde pretende implantar Private Link. Crie o ponto final privado no mesmo local onde existe a sua rede virtual.|
    |||
1. Selecione **Seguinte: Recurso**.
1. Em **Criar um ponto final privado - Recurso,** insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    |Método de ligação  | Selecione **Ligar a um recurso Azure no meu diretório**. <br/><br/> Em seguida, pode escolher um dos seus recursos para configurar o Private Link. Ou pode ligar-se ao recurso de outra pessoa usando um ID de recurso ou um pseudónimo que partilharam consigo.|
    | Subscrição| Selecione a sua subscrição. |
    | Tipo de recurso | Selecione **Microsoft.AzureCosmosDB/databaseSa contas**. |
    | Recurso |Selecione a sua conta Azure Cosmos. |
    |Recurso secundário de destino |Selecione o tipo Azure Cosmos DB API que deseja mapear. Isto é uma padrão para apenas uma escolha para as APIs SQL, MongoDB e Cassandra. Para as APIs gremlin e de tabela, você também pode escolher **Sql** porque estes APIs são interoperáveis com o SQL API. |
    |||

1. Selecione **Seguinte: Configuração**.
1. Em **Criar um ponto final privado - Configuração,** insira ou selecione esta informação:

    | Definição | Valor |
    | ------- | ----- |
    |**Rede**| |
    | Rede virtual| Selecione a sua rede virtual. |
    | Sub-rede | Selecione a sua sub-rede. |
    |**Integração privada de DNS**||
    |Integrar com zona DNS privada |Selecione **Yes** (Sim). <br><br/> Para se ligar em privado com o seu ponto final privado, precisa de um registo DNS. Recomendamos que integre o seu ponto de terminação privado com uma zona privada de DNS. Também pode utilizar os seus próprios servidores DNS ou criar registos DNS utilizando os ficheiros anfitriões nas suas máquinas virtuais. |
    |Zona DNS Privada |Selecione **privatelink.documents.azure.com**. <br><br/> A zona privada de DNS é determinada automaticamente. Não pode mudá-lo usando o portal Azure.|
    |||

1. Selecione **Rever + criar**. Na página **'Rever + criar',** o Azure valida a sua configuração.
1. Quando vir a mensagem **A validação passou**, selecione **Criar**.

Quando tiver aprovado o Private Link para uma conta Azure Cosmos, no portal Azure, a opção **Todas as redes** no Painel de Firewall e redes **virtuais** não está disponível.

A tabela seguinte mostra o mapeamento entre diferentes tipos de API de conta Azure Cosmos, sub-recursos suportados e os nomes correspondentes da zona privada. Também pode aceder às contas da API gremlin e da tabela através da API SQL, pelo que existem duas entradas para estas APIs.

|Tipo AZure Cosmos conta API  |Sub-recursos suportados (ou IDs de grupo) |Nome da zona privada  |
|---------|---------|---------|
|SQL    |   SQL      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Rio Mongo   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  SQL       |  privatelink.documents.azure.com    |
|Tabela    |    Tabela     |   privatelink.table.cosmos.azure.com    |
|Tabela     |   SQL      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>Buscar os endereços IP privados

Depois de ser previsto o ponto final privado, pode consultar os endereços IP. Para ver os endereços IP a partir do portal Azure:

1. Selecione **Todos os recursos**.
1. Procure o ponto final privado que criou anteriormente. Neste caso, é **cdbPrivateEndpoint3**.
1. Selecione o **separador 'Vista Geral'** para ver as definições de DNS e endereços IP.

:::image type="content" source="./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png" alt-text="Endereços IP privados no portal Azure":::

Vários endereços IP são criados por ponto final privado:

* Um para o ponto final global (região-agnóstico) da conta Azure Cosmos
* Um para cada região onde a conta Azure Cosmos é implantada

## <a name="create-a-private-endpoint-by-using-azure-powershell"></a>Criar um ponto final privado utilizando a Azure PowerShell

Execute o seguinte script PowerShell para criar um ponto final privado chamado "MyPrivateEndpoint" para uma conta Azure Cosmos existente. Substitua os valores variáveis pelos detalhes do seu ambiente.

```azurepowershell-interactive
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"

# API type of the Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "MyPrivateEndpoint"
# Location where the private endpoint can be created. The private endpoint should be created in the same location where your subnet or the virtual network exists
$Location = "westcentralus"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnectionPS" -PrivateLinkServiceId $cosmosDbResourceId -GroupId $CosmosDbApiType
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $ResourceGroupName -Name $VNetName  
 
$subnet = $virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq $SubnetName}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $ResourceGroupName -Name $PrivateEndpointName -Location "westcentralus" -Subnet  $subnet -PrivateLinkServiceConnection $privateEndpointConnection
```

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Integre o ponto final privado com uma zona privada de DNS

Depois de criar o ponto final privado, pode integrá-lo com uma zona privada de DNS utilizando o seguinte script PowerShell:

```azurepowershell-interactive
Import-Module Az.PrivateDns
$zoneName = "privatelink.documents.azure.com"
$zone = New-AzPrivateDnsZone -ResourceGroupName $ResourceGroupName `
  -Name $zoneName

$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $ResourceGroupName `
  -ZoneName $zoneName `
  -Name "myzonelink" `
  -VirtualNetworkId $virtualNetwork.Id  
 
$pe = Get-AzPrivateEndpoint -Name $PrivateEndpointName `
  -ResourceGroupName $ResourceGroupName

$networkInterface = Get-AzResource -ResourceId $pe.NetworkInterfaces[0].Id `
  -ApiVersion "2019-04-01"
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
$recordName = $fqdn.split('.',2)[0] 
$dnsZone = $fqdn.split('.',2)[1] 
New-AzPrivateDnsRecordSet -Name $recordName `
  -RecordType A -ZoneName $zoneName  `
  -ResourceGroupName $ResourceGroupName -Ttl 600 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig `
  -IPv4Address $ipconfig.properties.privateIPAddress)  
}
}
```

### <a name="fetch-the-private-ip-addresses"></a>Buscar os endereços IP privados

Depois de ser apresentado o ponto final privado, pode consultar os endereços IP e o mapeamento FQDN utilizando o seguinte script PowerShell:

```azurepowershell-interactive
$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-endpoint-by-using-azure-cli"></a>Criar um ponto final privado utilizando o Azure CLI

Execute o seguinte script Azure CLI para criar um ponto final privado chamado "myPrivateEndpoint" para uma conta Azure Cosmos existente. Substitua os valores variáveis pelos detalhes do seu ambiente.

```azurecli-interactive
# Resource group where the Azure Cosmos account and virtual network resources are located
ResourceGroupName="myResourceGroup"

# Subscription ID where the Azure Cosmos account and virtual network resources are located
SubscriptionId="<your Azure subscription ID>"

# Name of the existing Azure Cosmos account
CosmosDbAccountName="mycosmosaccount"

# API type of your Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
CosmosDbApiType="Sql"

# Name of the virtual network to create
VNetName="myVnet"

# Name of the subnet to create
SubnetName="mySubnet"

# Name of the private endpoint to create
PrivateEndpointName="myPrivateEndpoint"

# Name of the private endpoint connection to create
PrivateConnectionName="myConnection"

az network vnet create \
 --name $VNetName \
 --resource-group $ResourceGroupName \
 --subnet-name $SubnetName

az network vnet subnet update \
 --name $SubnetName \
 --resource-group $ResourceGroupName \
 --vnet-name $VNetName \
 --disable-private-endpoint-network-policies true

az network private-endpoint create \
    --name $PrivateEndpointName \
    --resource-group $ResourceGroupName \
    --vnet-name $VNetName  \
    --subnet $SubnetName \
    --private-connection-resource-id "/subscriptions/$SubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.DocumentDB/databaseAccounts/$CosmosDbAccountName" \
    --group-ids $CosmosDbApiType \
    --connection-name $PrivateConnectionName
```

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Integre o ponto final privado com uma zona privada de DNS

Depois de criar o ponto final privado, pode integrá-lo com uma zona privada de DNS utilizando o seguinte script Azure CLI:

```azurecli-interactive
zoneName="privatelink.documents.azure.com"

az network private-dns zone create --resource-group $ResourceGroupName \
   --name  $zoneName

az network private-dns link vnet create --resource-group $ResourceGroupName \
   --zone-name  $zoneName\
   --name myzonelink \
   --virtual-network $VNetName \
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name $PrivateEndpointName --resource-group $ResourceGroupName --query 'networkInterfaces[0].id' -o tsv)
 
# Copy the content for privateIPAddress and FQDN matching the Azure Cosmos account 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
 
#Create DNS records 
az network private-dns record-set a create --name recordSet1 --zone-name privatelink.documents.azure.com --resource-group $ResourceGroupName
az network private-dns record-set a add-record --record-set-name recordSet2 --zone-name privatelink.documents.azure.com --resource-group $ResourceGroupName -a <Private IP Address>
```

## <a name="create-a-private-endpoint-by-using-a-resource-manager-template"></a>Crie um ponto final privado usando um modelo de Gestor de Recursos

Pode configurar o Private Link criando um ponto final privado numa sub-rede de rede virtual. Consegue-o utilizando um modelo de Gestor de Recursos Azure.

Utilize o seguinte código para criar um modelo de Gestor de Recursos chamado "PrivateEndpoint_template.jsligado". Este modelo cria um ponto final privado para uma conta API API AZure Cosmos SQL existente numa rede virtual existente.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "[resourceGroup().location]",
          "metadata": {
            "description": "Location for all resources."
          }
        },
        "privateEndpointName": {
            "type": "string"
        },
        "resourceId": {
            "type": "string"
        },
        "groupId": {
            "type": "string"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('privateEndpointName')]",
            "type": "Microsoft.Network/privateEndpoints",
            "apiVersion": "2019-04-01",
            "location": "[parameters('location')]",
            "properties": {
                "subnet": {
                    "id": "[parameters('subnetId')]"
                },
                "privateLinkServiceConnections": [
                    {
                        "name": "MyConnection",
                        "properties": {
                            "privateLinkServiceId": "[parameters('resourceId')]",
                            "groupIds": [ "[parameters('groupId')]" ],
                            "requestMessage": ""
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {
        "privateEndpointNetworkInterface": {
          "type": "string",
          "value": "[reference(concat('Microsoft.Network/privateEndpoints/', parameters('privateEndpointName'))).networkInterfaces[0].id]"
        }
    }
}
```

**Defina o ficheiro de parâmetros para o modelo**

Crie um ficheiro de parâmetros para o modelo, e nomeie-o como "PrivateEndpoint_parameters.jsligado". Adicione o seguinte código ao ficheiro parâmetros:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateEndpointName": {
            "value": ""
        },
        "resourceId": {
            "value": ""
        },
        "groupId": {
            "value": ""
        },
        "subnetId": {
            "value": ""
        }
    }
}
```

**Implemente o modelo utilizando um script PowerShell**

Crie um script PowerShell utilizando o seguinte código. Antes de executar o script, substitua o ID de subscrição, o nome do grupo de recursos e outros valores variáveis com os detalhes para o seu ambiente.

```azurepowershell-interactive
### This script creates a private endpoint for an existing Azure Cosmos account in an existing virtual network

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Sign in to your Azure account and select the target subscription.
Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet.
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private endpoint.
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName

$deploymentOutput
```

No script PowerShell, a `GroupId` variável pode conter apenas um valor. Este valor é o tipo API da conta. Os valores permitidos são: `Sql` , , , e `MongoDB` `Cassandra` `Gremlin` `Table` . Alguns tipos de conta Azure Cosmos são acessíveis através de várias APIs. Por exemplo:

* Uma conta API gremlin pode ser acedida a partir de contas de API gremlin e SQL.
* Uma conta API de tabela pode ser acedida a partir de contas API de Tabela e SQL.

Para essas contas, deve criar um ponto final privado para cada tipo de API. O tipo API correspondente é especificado na `GroupId` matriz.

Depois de o modelo ser implementado com sucesso, pode ver uma saída semelhante à que a imagem seguinte mostra. O `provisioningState` valor é se os `Succeeded` pontos finais privados forem configurado corretamente.

:::image type="content" source="./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png" alt-text="Saída de implementação para o modelo de Gestor de Recursos":::

Após a implementação do modelo, os endereços IP privados são reservados dentro da sub-rede. A regra de firewall da conta Azure Cosmos está configurada para aceitar ligações apenas a partir do ponto final privado.

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Integre o ponto final privado com uma Zona Privada de DNS

Utilize o seguinte código para criar um modelo de Gestor de Recursos chamado "PrivateZone_template.jsligado". Este modelo cria uma zona de DNS privada para uma conta API API Azure Cosmos SQL existente numa rede virtual existente.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "type": "string"
        },
        "VNetId": {
            "type": "string"
        }        
    },
    "resources": [
        {
            "name": "[parameters('privateZoneName')]",
            "type": "Microsoft.Network/privateDnsZones",
            "apiVersion": "2018-09-01",
            "location": "global",
            "properties": {                
            }
        },
        {
            "type": "Microsoft.Network/privateDnsZones/virtualNetworkLinks",
            "apiVersion": "2018-09-01",
            "name": "[concat(parameters('privateZoneName'), '/myvnetlink')]",
            "location": "global",
            "dependsOn": [
                "[resourceId('Microsoft.Network/privateDnsZones', parameters('privateZoneName'))]"
            ],
            "properties": {
                "registrationEnabled": false,
                "virtualNetwork": {
                    "id": "[parameters('VNetId')]"
                }
            }
        }        
    ]
}
```

Utilize o seguinte código para criar um modelo de Gestor de Recursos chamado "PrivateZoneRecords_template.jsligado".

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DNSRecordName": {
            "type": "string"
        },
        "IPAddress": {
            "type":"string"
        }        
    },
    "resources": [
         {
            "type": "Microsoft.Network/privateDnsZones/A",
            "apiVersion": "2018-09-01",
            "name": "[parameters('DNSRecordName')]",
            "properties": {
                "ttl": 300,
                "aRecords": [
                    {
                        "ipv4Address": "[parameters('IPAddress')]"
                    }
                ]
            }
        }    
    ]
}
```

**Defina o ficheiro de parâmetros para o modelo**

Crie o seguinte arquivo de dois parâmetros para o modelo. Crie o "PrivateZone_parameters.json". com o seguinte código:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "value": ""
        },
        "VNetId": {
            "value": ""
        }
    }
}
```

Crie o "PrivateZoneRecords_parameters.json". com o seguinte código:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DNSRecordName": {
            "value": ""
        },
        "IPAddress": {
            "type":"object"
        }
    }
}
```

**Implemente o modelo utilizando um script PowerShell**

Crie um script PowerShell utilizando o seguinte código. Antes de executar o script, substitua o ID de subscrição, o nome do grupo de recursos e outros valores variáveis com os detalhes para o seu ambiente.

```azurepowershell-interactive
### This script:
### - creates a private zone
### - creates a private endpoint for an existing Cosmos DB account in an existing VNet
### - maps the private endpoint to the private zone

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private zone to create
$PrivateZoneName = "myPrivateZone.documents.azure.com"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateZoneTemplateFilePath = "PrivateZone_template.json"
$PrivateZoneParametersFilePath = "PrivateZone_parameters.json"
$PrivateZoneRecordsTemplateFilePath = "PrivateZoneRecords_template.json"
$PrivateZoneRecordsParametersFilePath = "PrivateZoneRecords_parameters.json"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Login your Azure account and select the target subscription
Login-AzAccount 
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private zone
New-AzResourceGroupDeployment -Name "PrivateZoneDeployment" `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $PrivateZoneTemplateFilePath `
    -TemplateParameterFile $PrivateZoneParametersFilePath `
    -PrivateZoneName $PrivateZoneName `
    -VNetId $VNetResourceId

## Step 5: Create the private endpoint
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName
$deploymentOutput

## Step 6: Map the private endpoint to the private zone
$networkInterface = Get-AzResource -ResourceId $deploymentOutput.Outputs.privateEndpointNetworkInterface.Value -ApiVersion "2019-04-01"
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) {
    foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) {
        $recordName = $fqdn.split('.',2)[0]
        $dnsZone = $fqdn.split('.',2)[1]
        Write-Output "Deploying PrivateEndpoint DNS Record $($PrivateZoneName)/$($recordName) Template on $($resourceGroupName)"
        New-AzResourceGroupDeployment -Name "PrivateEndpointDNSDeployment" `
            -ResourceGroupName $ResourceGroupName `
            -TemplateFile $PrivateZoneRecordsTemplateFilePath `
            -TemplateParameterFile $PrivateZoneRecordsParametersFilePath `
            -DNSRecordName "$($PrivateZoneName)/$($RecordName)" `
            -IPAddress $ipconfig.properties.privateIPAddress
    }
}
```

## <a name="configure-custom-dns"></a>Configurar DNS personalizado

Deve utilizar uma zona de DNS privada dentro da sub-rede onde criou o ponto final privado. Configure os pontos finais de modo a que cada endereço IP privado seja mapeado para uma entrada DE DNS. (Ver o `fqdns` imóvel na resposta mostrada anteriormente.)

Quando estiver a criar o ponto final privado, pode integrá-lo com uma zona privada de DNS em Azure. Se optar por utilizar uma zona DE DNS personalizada, tem de as configurar para adicionar registos DNS para todos os endereços IP privados reservados para o ponto final privado.

> [!IMPORTANT]
> É a resolução do DNS dos seus pedidos que determina se estes pedidos passam por cima dos seus pontos finais privados, ou se seguem o caminho público padrão. Certifique-se de que o seu DNS local faz referência correta ao IP privado mapeado pelo seu ponto final privado.

## <a name="private-link-combined-with-firewall-rules"></a>Ligação privada combinada com regras de firewall

As seguintes situações e resultados são possíveis quando utiliza o Private Link em combinação com as regras de firewall:

* Se não configurar quaisquer regras de firewall, então, por padrão, todo o tráfego pode aceder a uma conta Azure Cosmos.

* Se configurar o tráfego público ou um ponto final de serviço e criar pontos finais privados, então diferentes tipos de tráfego de entrada são autorizados pela regra de firewall correspondente. Se um ponto final privado estiver configurado numa sub-rede onde o ponto final de serviço também está configurado:
  * o tráfego para a conta de base de dados mapeada pelo ponto final privado é encaminhado através de ponto final privado,
  * o tráfego para outras contas de base de dados da sub-rede é encaminhado através do ponto final de serviço.

* Se não configurar qualquer ponto final de tráfego público ou serviço e criar pontos finais privados, então a conta Azure Cosmos só está acessível através dos pontos finais privados. Se não configurar o tráfego público ou um ponto final de serviço, depois de todos os pontos finais privados aprovados serem rejeitados ou eliminados, a conta está aberta a toda a rede, a menos que o PublicNetworkAccess esteja definido para Desativado (ver secção abaixo).

## <a name="blocking-public-network-access-during-account-creation"></a>Bloquear o acesso à rede pública durante a criação de conta

Como descrito na secção anterior, e a menos que tenham sido definidas regras específicas de firewall, adicionar um ponto final privado torna a sua conta Azure Cosmos acessível apenas através de pontos finais privados. Isto significa que a conta Azure Cosmos poderia ser alcançada a partir do tráfego público após a sua criação e antes que um ponto final privado seja adicionado. Para garantir que o acesso à rede pública seja desativado mesmo antes da criação de pontos finais privados, pode definir a bandeira durante a criação de `publicNetworkAccess` `Disabled` conta. Note que esta bandeira tem precedência sobre qualquer regra de IP ou rede virtual; todo o tráfego de rede pública e virtual é bloqueado quando a bandeira está definida para `Disabled` , mesmo que a origem IP ou rede virtual seja permitida na configuração de firewall.

Consulte [este modelo de Gestor de Recursos Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-private-endpoint/) para um exemplo que mostra como usar esta bandeira.

## <a name="adding-private-endpoints-to-an-existing-cosmos-account-with-no-downtime"></a>Adicionar pontos finais privados a uma conta cosmos existente sem tempo de inatividade

Por predefinição, a adição de um ponto final privado a uma conta existente resulta num curto período de paragem de aproximadamente 5 minutos. Siga as instruções abaixo para evitar este tempo de paragem:

1. Adicione regras de IP ou rede virtual à sua configuração de firewall para permitir explicitamente as ligações do seu cliente.
1. Aguarde 10 minutos para se certificar de que a atualização de configuração é aplicada.
1. Configure o seu novo ponto final privado.
1. Retire as regras de firewall definidas no passo 1.

## <a name="port-range-when-using-direct-mode"></a>Gama de portas ao utilizar o modo direto

Quando estiver a utilizar o Private Link com uma conta Azure Cosmos através de uma ligação de modo direto, tem de garantir que toda a gama de portas TCP (0 - 65535) está aberta.

## <a name="update-a-private-endpoint-when-you-add-or-remove-a-region"></a>Atualizar um ponto final privado quando adicionar ou remover uma região

A menos que esteja a usar um grupo privado de zona de DNS, adicionar ou remover regiões a uma conta Azure Cosmos requer que adicione ou remova as entradas de DNS para essa conta. Depois de as regiões terem sido adicionadas ou removidas, pode atualizar a zona privada de DNS da sub-rede para refletir as entradas DNS adicionadas ou removidas e os respetivos endereços IP privados.

Por exemplo, imagine que implanta uma conta Azure Cosmos em três regiões: "EUA Ocidentais", "EUA Centrais" e "Europa Ocidental". Quando cria um ponto final privado para a sua conta, quatro IPs privados são reservados na sub-rede. Há um IP para cada uma das três regiões, e há um IP para o ponto final global/regional-agnóstico.

Mais tarde, poderá adicionar uma nova região (por exemplo, "East US") à conta de Azure Cosmos. Depois de adicionar a nova região, precisa de adicionar um registo DNS correspondente à sua zona privada de DNS ou ao seu DNS personalizado.

Pode usar os mesmos passos quando remover uma região. Depois de remover a região, tem de remover o registo DNS correspondente da sua zona privada de DNS ou do seu DNS personalizado.

## <a name="current-limitations"></a>Limitações atuais

Aplicam-se as seguintes limitações quando utiliza o Private Link com uma conta Azure Cosmos:

* Não pode ter mais de 200 pontos finais privados numa única conta da Azure Cosmos.

* Quando estiver a utilizar o Link Privado com uma conta Azure Cosmos através de uma ligação de modo direto, pode utilizar apenas o protocolo TCP. O protocolo HTTP não está atualmente suportado.

* Quando está a utilizar a API da Azure Cosmos para contas MongoDB, um ponto final privado é suportado para contas apenas na versão 3.6 do servidor (isto é, contas que utilizam o ponto final no `*.mongo.cosmos.azure.com` formato). O Private Link não é suportado para contas na versão 3.2 do servidor (isto é, contas que utilizam o ponto final no `*.documents.azure.com` formato). Para utilizar o Private Link, deverá migrar contas antigas para a nova versão.

* Quando estiver a utilizar uma conta AZure Cosmos DB para a conta MongoDB que tenha uma Ligação Privada, as ferramentas/bibliotecas devem suportar a Identificação de Nome de Serviço (SNI) ou passar o `appName` parâmetro da cadeia de ligação para se ligar corretamente. Algumas ferramentas/bibliotecas mais antigas podem não ser compatíveis para utilizar a função De ligação privada.

* Um administrador de rede deve ser concedido pelo menos a `Microsoft.DocumentDB/databaseAccounts/PrivateEndpointConnectionsApproval/action` permissão no âmbito da conta Azure Cosmos para criar pontos finais privados automaticamente aprovados.

### <a name="limitations-to-private-dns-zone-integration"></a>Limitações à integração privada da zona de DNS

A menos que esteja a usar um grupo privado de zona de DNS, os registos DNS na zona privada do DNS não são removidos automaticamente quando elimina um ponto final privado ou remove uma região da conta Azure Cosmos. Deve remover manualmente os registos DNS antes:

* Adicionar um novo ponto final privado ligado a esta zona privada de DNS.
* Adicionar uma nova região a qualquer conta de base de dados que tenha pontos finais privados ligados a esta zona privada de DNS.

Se não limpar os registos do DNS, problemas inesperados de data plane podem acontecer. Estas questões incluem a interrupção de dados para as regiões adicionadas após a remoção do ponto final privado ou a remoção da região.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as funcionalidades de segurança da Azure Cosmos DB, consulte os seguintes artigos:

* Para configurar uma firewall para Azure Cosmos DB, consulte [o suporte do Firewall](how-to-configure-firewall.md).

* Para aprender a configurar um ponto final de serviço de rede virtual para a sua conta Azure Cosmos, consulte [o acesso configurar a partir de redes virtuais.](how-to-configure-vnet-service-endpoint.md)

* Para saber mais sobre o Private Link, consulte a documentação do [Azure Private Link.](../private-link/private-link-overview.md)