---
title: Configure Link Privado Azure para uma conta Azure Cosmos
description: Saiba como configurar o Azure Private Link para aceder a uma conta Azure Cosmos utilizando um endereço IP privado numa rede virtual.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: thweiss
ms.openlocfilehash: 4b49d2aa61587d0156755bdd5c47b3eeb90090a5
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270694"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account"></a>Configure Link Privado Azure para uma conta Azure Cosmos

Ao utilizar o Azure Private Link, pode ligar-se a uma conta Azure Cosmos através de um ponto final privado. O ponto final privado é um conjunto de endereços IP privados numa subnet dentro da sua rede virtual. Em seguida, pode limitar o acesso a uma conta Azure Cosmos em endereços IP privados. Quando o Private Link é combinado com políticas restritas de NSG, ajuda a reduzir o risco de exfiltração de dados. Para saber mais sobre pontos finais privados, consulte o artigo [Azure Private Link.](../private-link/private-link-overview.md)

O Private Link permite que os utilizadores acedam a uma conta Azure Cosmos a partir da rede virtual ou de qualquer rede virtual. Os recursos mapeados para private link também são acessíveis no local através de peering privado através de VPN ou Azure ExpressRoute. 

Pode ligar-se a uma conta Azure Cosmos configurada com private link utilizando o método de aprovação automática ou manual. Para saber mais, consulte a secção de fluxo de trabalho de [aprovação](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow) da documentação private link. 

Este artigo descreve os passos para criar um ponto final privado. Assume que está a usar o método de aprovação automática.

> [!NOTE]
> O suporte de ponto final privado está atualmente disponível apenas para o modo de ligação gateway. Para o modo direto, está disponível como uma função de pré-visualização.

## <a name="create-a-private-endpoint-by-using-the-azure-portal"></a>Criar um ponto final privado utilizando o portal Azure

Utilize os seguintes passos para criar um ponto final privado para uma conta Azure Cosmos existente utilizando o portal Azure:

1. A partir do painel **de todos os recursos,** escolha uma conta Azure Cosmos.

1. Selecione **Ligações de ponto final privados** da lista de definições e, em seguida, selecione **ponto final privado:**

   ![Seleções para criar um ponto final privado no portal Azure](./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png)

1. No **Create a private endpoint - Basics** pane, insira ou selecione os seguintes detalhes:

    | Definição | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione um grupo de recursos.|
    | **Detalhes da instância** |  |
    | Nome | Insira qualquer nome para o seu ponto final privado. Se este nome for tomado, crie um único. |
    |Região| Selecione a região onde pretende implementar private link. Crie o ponto final privado no mesmo local onde existe a sua rede virtual.|
    |||
1. Selecione **Seguinte: Recurso**.
1. Em **Criar um ponto final privado - Recurso,** insira ou selecione esta informação:

    | Definição | Valor |
    | ------- | ----- |
    |Método de ligação  | Selecione **Ligar a um recurso Azure no meu diretório**. <br/><br/> Em seguida, pode escolher um dos seus recursos para criar private link. Ou pode ligar-se ao recurso de outra pessoa usando uma identificação de recursos ou pseudónimo que eles partilharam consigo.|
    | Subscrição| Selecione a sua subscrição. |
    | Tipo de recurso | Selecione **Microsoft.AzureCosmosDB/databaseAccounts**. |
    | Recurso |Selecione a sua conta Azure Cosmos. |
    |Sub-recurso-alvo |Selecione o tipo API Do Azure Cosmos DB que pretende mapear. Isto falha apenas uma escolha para as APIs SQL, MongoDB e Cassandra. Para as APIs Gremlin e Table, também pode escolher **Sql** porque estas APIs são interoperáveis com a API SQL. |
    |||

1. Selecione **Seguinte: Configuração**.
1. Em **Criar um ponto final privado - Configuração,** insira ou selecione esta informação:

    | Definição | Valor |
    | ------- | ----- |
    |**Redes**| |
    | Rede virtual| Selecione a sua rede virtual. |
    | Subrede | Selecione a sua sub-rede. |
    |**Integração Privada de DNS**||
    |Integrar com zona privada de DNS |Selecione **Sim**. <br><br/> Para se ligar em privado com o seu ponto final privado, precisa de um registo DNS. Recomendamos que integre o seu ponto final privado com uma zona privada de DNS. Também pode utilizar os seus próprios servidores DNS ou criar registos DNS utilizando os ficheiros de anfitriões nas suas máquinas virtuais. |
    |Zona Privada de DNS |Selecione **privatelink.documents.azure.com**. <br><br/> A zona privada de DNS é determinada automaticamente. Não se pode mudá-lo usando o portal Azure.|
    |||

1. Selecione **Rever + criar**. Na página **Review + criar,** o Azure valida a sua configuração.
1. Quando vir a **mensagem de validação passada,** selecione **Criar**.

Quando tiver aprovado o Private Link para uma conta Azure Cosmos, no portal Azure, a opção **Todas as redes** no Firewall e o painel de redes **virtuais** não está disponível.

A tabela seguinte mostra o mapeamento entre diferentes tipos de API da conta Azure Cosmos, subrecursos suportados e os nomes de zona privada correspondentes. Também pode aceder às contas Gremlin e Table API através da API SQL, pelo que existem duas entradas para estas APIs.

|Conta Azure Cosmos Tipo API  |Sub-recursos suportados (ou IDs de grupo) |Nome de zona privada  |
|---------|---------|---------|
|SQL    |   SQL      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Mongo   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  SQL       |  privatelink.documents.azure.com    |
|Tabela    |    Tabela     |   privatelink.table.cosmos.azure.com    |
|Tabela     |   SQL      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>Buscar os endereços IP privados

Depois do ponto final privado ser provisionado, pode consultar os endereços IP. Para ver os endereços IP do portal Azure:

1. Selecione **Todos os recursos**.
1. Procure o ponto final privado que criou anteriormente. Neste caso, é **cdbPrivateEndpoint3**.
1. Selecione o separador **'Visão Geral'** para ver as definições de DNS e os endereços IP.

![Endereços IP privados no portal Azure](./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png)

Vários endereços IP são criados por ponto final privado:

* Um para o ponto final global (região-agnóstico) da conta Azure Cosmos
* Um para cada região onde a conta Azure Cosmos é implantada

## <a name="create-a-private-endpoint-by-using-azure-powershell"></a>Criar um ponto final privado usando o Azure PowerShell

Execute o seguinte script PowerShell para criar um ponto final privado chamado "MyPrivateEndpoint" para uma conta Azure Cosmos existente. Substitua os valores variáveis pelos detalhes para o seu ambiente.

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

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Integrar o ponto final privado com uma zona privada de DNS

Depois de criar o ponto final privado, pode integrá-lo com uma zona Privada de DNS utilizando o seguinte script PowerShell:

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

Após o fornecimento do ponto final privado, pode consultar os endereços IP e o mapeamento FQDN utilizando o seguinte script PowerShell:

```azurepowershell-interactive
$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-endpoint-by-using-azure-cli"></a>Criar um ponto final privado utilizando o Azure CLI

Execute o seguinte script Azure CLI para criar um ponto final privado chamado "myPrivateEndpoint" para uma conta Azure Cosmos existente. Substitua os valores variáveis pelos detalhes para o seu ambiente.

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

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Integrar o ponto final privado com uma zona privada de DNS

Depois de criar o ponto final privado, pode integrá-lo com uma zona Privada de DNS utilizando o seguinte script Azure CLI:

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

## <a name="create-a-private-endpoint-by-using-a-resource-manager-template"></a>Criar um ponto final privado usando um modelo de Gestor de Recursos

Pode configurar o Private Link criando um ponto final privado numa subnet de rede virtual. Obtém-se isso utilizando um modelo de Gestor de Recursos Azure.

Utilize o seguinte código para criar um modelo de Gestor de Recursos chamado "PrivateEndpoint_template.json". Este modelo cria um ponto final privado para uma conta API Azure Cosmos SQL existente numa rede virtual existente.

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

Crie um ficheiro de parâmetros para o modelo e nomeie-o "PrivateEndpoint_parameters.json". Adicione o seguinte código ao ficheiro de parâmetros:

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

**Desdobrar o modelo usando um script PowerShell**

Crie um script PowerShell utilizando o seguinte código. Antes de executar o script, substitua o ID de subscrição, o nome do grupo de recursos e outros valores variáveis pelos detalhes para o seu ambiente.

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

No script PowerShell, `GroupId` a variável pode conter apenas um valor. Este valor é o tipo API da conta. Os valores `Sql` `MongoDB`permitidos são: , , `Cassandra` `Gremlin`e `Table`. Alguns tipos de conta Azure Cosmos são acessíveis através de múltiplas APIs. Por exemplo:

* Uma conta API Gremlin pode ser acedida a partir de contas API Gremlin e SQL.
* Uma conta API tabela pode ser acedida a partir de contas De Mesa e SQL API.

Para essas contas, deve criar um ponto final privado para cada tipo de API. O tipo API correspondente é `GroupId` especificado na matriz.

Depois do modelo ser implementado com sucesso, pode ver uma saída semelhante à que a imagem a seguir mostra. O `provisioningState` valor `Succeeded` é se os pontos finais privados forem corretamente configurados.

![Saída de implementação para o modelo de Gestor de Recursos](./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png)

Após a implementação do modelo, os endereços IP privados são reservados dentro da sub-rede. A regra da firewall da conta Azure Cosmos está configurada para aceitar ligações apenas a partir do ponto final privado.

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Integrar o ponto final privado com uma Zona Privada de DNS

Utilize o seguinte código para criar um modelo de Gestor de Recursos chamado "PrivateZone_template.json". Este modelo cria uma zona privada de DNS para uma conta API Azure Cosmos SQL existente numa rede virtual existente.

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

Utilize o seguinte código para criar um modelo de Gestor de Recursos chamado "PrivateZoneRecords_template.json".

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

Crie o seguinte ficheiro de dois parâmetros para o modelo. Crie o "PrivateZone_parameters.json". com o seguinte código:

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

**Desdobrar o modelo usando um script PowerShell**

Crie um script PowerShell utilizando o seguinte código. Antes de executar o script, substitua o ID de subscrição, o nome do grupo de recursos e outros valores variáveis pelos detalhes para o seu ambiente.

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

Deve usar uma zona privada de DNS dentro da subnet onde criou o ponto final privado. Configure os pontos finais de modo a que cada endereço IP privado seja mapeado para uma entrada dNS. (Consulte `fqdns` a propriedade na resposta mostrada anteriormente.)

Quando estiver a criar o ponto final privado, pode integrá-lo com uma zona privada de DNS em Azure. Se optar por utilizar uma zona DNS personalizada, tem de configurá-la para adicionar registos DNS para todos os endereços IP privados reservados para o ponto final privado.

## <a name="private-link-combined-with-firewall-rules"></a>Link Privado combinado com regras de firewall

As seguintes situações e resultados são possíveis quando utiliza o Private Link em combinação com as regras de firewall:

* Se não configurar quaisquer regras de firewall, então, por padrão, todo o tráfego pode aceder a uma conta Azure Cosmos.

* Se configurar o tráfego público ou um ponto final de serviço e criar pontos finais privados, então diferentes tipos de tráfego de entrada são autorizados pelo tipo correspondente de regra de firewall.

* Se não configurar qualquer ponto final de tráfego público ou de serviço e criar pontos finais privados, então a conta Azure Cosmos só é acessível através dos pontos finais privados. Se não configurar o tráfego público ou um ponto final de serviço, depois de todos os pontos finais privados aprovados serem rejeitados ou eliminados, a conta está aberta a toda a rede.

## <a name="blocking-public-network-access-during-account-creation"></a>Bloquear o acesso à rede pública durante a criação de contas

Tal como descrito na secção anterior, e a menos que tenham sido definidas regras específicas de firewall, adicionar um ponto final privado torna a sua conta Azure Cosmos acessível apenas através de pontos finais privados. Isto significa que a conta Azure Cosmos poderia ser alcançada a partir do tráfego público após a sua criação e antes de um ponto final privado ser adicionado. Para garantir que o acesso à rede pública é desativado `publicNetworkAccess` mesmo `Disabled` antes da criação de pontos finais privados, pode definir a bandeira durante a criação de contas. Consulte este modelo de Gestor de [Recursos Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-private-endpoint/) para um exemplo que mostra como usar esta bandeira.

## <a name="update-a-private-endpoint-when-you-add-or-remove-a-region"></a>Atualize um ponto final privado quando adicionar ou remover uma região

Adicionar ou remover regiões a uma conta Azure Cosmos requer que adicione ou remova as entradas de DNS para essa conta. Depois de as regiões terem sido adicionadas ou removidas, pode atualizar a zona privada de DNS da subrede para refletir as entradas de DNS adicionadas ou removidas e os respetivos endereços IP privados correspondentes.

Por exemplo, imagine que implementa uma conta Azure Cosmos em três regiões: "Oeste dos EUA", "EUA Central" e "Europa Ocidental". Quando cria um ponto final privado para a sua conta, quatro IPs privados são reservados na subnet. Há um IP para cada uma das três regiões, e há um IP para o ponto final global/regional-agnóstico.

Mais tarde, poderá adicionar uma nova região (por exemplo, "Leste dos EUA") à conta Azure Cosmos. Depois de adicionar a nova região, precisa adicionar um registo DNS correspondente à sua zona privada de DNS ou ao seu DNS personalizado.

Pode utilizar os mesmos passos quando remover uma região. Depois de remover a região, tem de remover o registo DNS correspondente da sua zona Privada DNS ou do seu DNS personalizado.

## <a name="current-limitations"></a>Limitações atuais

As seguintes limitações aplicam-se quando se está a usar o Private Link com uma conta Azure Cosmos:

* Quando estiver a utilizar o Private Link com uma conta Azure Cosmos utilizando uma ligação de modo direto, só pode utilizar o protocolo TCP. O protocolo HTTP ainda não é suportado.

* O suporte de ponto final privado está atualmente disponível apenas para o modo de ligação gateway. Para o modo direto, está disponível como uma função de pré-visualização.

* Quando está a utilizar a API do Azure Cosmos DB para contas MongoDB, um ponto final privado é suportado para contas apenas `*.mongo.cosmos.azure.com`na versão do servidor 3.6 (isto é, contas usando o ponto final no formato). O Private Link não é suportado para contas na versão 3.2 do servidor `*.documents.azure.com`(isto é, contas que utilizam o ponto final no formato). Para utilizar o Private Link, deve migrar contas antigas para a nova versão.

* Quando você está usando a API do Azure Cosmos DB para contas MongoDB que têm Private Link, você não pode usar ferramentas como Robo 3T, Studio 3T e Mongoose. O ponto final só pode ter `appName=<account name>` suporte de ligação privada se o parâmetro for especificado. Um exemplo é `replicaSet=globaldb&appName=mydbaccountname`. Como estas ferramentas não passam o nome da aplicação na cadeia de ligação ao serviço, não pode utilizar o Private Link. Mas ainda pode aceder a estas contas utilizando controladores SDK com a versão 3.6.

* Não pode mover ou eliminar uma rede virtual se contiver Private Link.

* Não se pode apagar uma conta Azure Cosmos se estiver ligada a um ponto final privado.

* Não se pode falhar uma conta azure cosmos para uma região que não está mapeada para todos os pontos finais privados anexados à conta.

* Um administrador de rede deve ser concedido pelo menos a permissão "*/PrivateEndpointConnectionsApproval" no âmbito da conta Azure Cosmos para criar pontos finais privados automaticamente aprovados.

### <a name="limitations-to-private-dns-zone-integration"></a>Limitações à integração privada da zona DNS

Os registos DNS na zona privada de DNS não são removidos automaticamente quando elimina um ponto final privado ou se remove uma região da conta Azure Cosmos. Deve remover manualmente os registos DNS antes:

* Adicionando um novo ponto final privado ligado a esta zona privada de DNS.
* Adicionar uma nova região a qualquer conta de base de dados que tenha pontos finais privados ligados a esta zona privada de DNS.

Se não limpar os registos do DNS, podem acontecer problemas inesperados de aviões de dados. Estas questões incluem a interrupção de dados para as regiões adicionadas após a remoção do ponto final privado ou a remoção da região.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as funcionalidades de segurança da Azure Cosmos DB, consulte os seguintes artigos:

* Para configurar uma firewall para Azure Cosmos DB, consulte o [suporte firewall](firewall-support.md).

* Para aprender a configurar um ponto final de serviço de rede virtual para a sua conta Azure Cosmos, consulte o [acesso configure a partir de redes virtuais](how-to-configure-vnet-service-endpoint.md).

* Para saber mais sobre o Private Link, consulte a documentação [do Link Privado Azure.](../private-link/private-link-overview.md)
