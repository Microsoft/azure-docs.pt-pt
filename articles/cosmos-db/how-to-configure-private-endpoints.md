---
title: Configurar o link privado do Azure para uma conta do Azure Cosmos
description: Saiba como configurar o link privado do Azure para acessar uma conta do Azure Cosmos usando um endereço IP privado em uma rede virtual.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: thweiss
ms.openlocfilehash: fde8829da3e523ced44143db0dee6b93cf9152bd
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74147755"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account-preview"></a>Configurar o link privado do Azure para uma conta do Azure Cosmos (versão prévia)

Usando o link privado do Azure, você pode se conectar a uma conta do Azure Cosmos por meio de um ponto de extremidade privado. O ponto de extremidade privado é um conjunto de endereços IP privados em uma sub-rede dentro de sua rede virtual. Você pode então limitar o acesso a uma conta do Azure Cosmos sobre endereços IP privados. Quando o link privado é combinado com políticas NSG restritas, ele ajuda a reduzir o risco de vazamento de dados. Para saber mais sobre pontos de extremidade privados, confira o artigo [link privado do Azure](../private-link/private-link-overview.md) .

O link privado permite que os usuários acessem uma conta do Azure Cosmos de dentro da rede virtual ou de qualquer rede virtual emparelhada. Os recursos mapeados para o link privado também podem ser acessados localmente no emparelhamento privado por meio de VPN ou do Azure ExpressRoute. 

Você pode se conectar a uma conta do Azure Cosmos configurada com o link privado usando o método de aprovação automática ou manual. Para saber mais, confira a seção [fluxo de trabalho de aprovação](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow) da documentação do link privado. 

Este artigo descreve as etapas para criar um ponto de extremidade privado. Ele pressupõe que você esteja usando o método de aprovação automática.

## <a name="create-a-private-endpoint-by-using-the-azure-portal"></a>Criar um ponto de extremidade privado usando o portal do Azure

Use as etapas a seguir para criar um ponto de extremidade privado para uma conta existente do Azure Cosmos usando o portal do Azure:

1. No painel **todos os recursos** , escolha uma conta do Azure Cosmos.

1. Selecione **conexões de ponto de extremidade privado** na lista de configurações e, em seguida, selecione **ponto de extremidade privado**:

   ![Seleções para criar um ponto de extremidade privado no portal do Azure](./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png)

1. No painel **criar um ponto de extremidade privado (visualização) – noções básicas** , insira ou selecione os seguintes detalhes:

    | Definição | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione um grupo de recursos.|
    | **Detalhes da instância** |  |
    | Nome | Insira qualquer nome para seu ponto de extremidade privado. Se esse nome for obtido, crie um exclusivo. |
    |Região| Selecione a região onde você deseja implantar o link privado. Crie o ponto de extremidade privado no mesmo local em que sua rede virtual existe.|
    |||
1. Selecione **Avançar: recurso**.
1. Em **criar um ponto de extremidade privado-recurso**, insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    |Método de ligação  | Selecione **conectar a um recurso do Azure em meu diretório**. <br/><br/> Em seguida, você pode escolher um de seus recursos para configurar o link privado. Ou você pode se conectar ao recurso de outra pessoa usando uma ID de recurso ou alias que eles compartilharam com você.|
    | Subscrição| Selecione a sua subscrição. |
    | Tipo de recurso | Selecione **Microsoft. AzureCosmosDB/databaseAccounts**. |
    | Recurso |Selecione sua conta do Azure Cosmos. |
    |Sub-recurso de destino |Selecione o tipo de API Azure Cosmos DB que você deseja mapear. O padrão é apenas uma opção para as APIs SQL, MongoDB e Cassandra. Para as APIs de tabela e Gremlin, você também pode escolher **SQL** porque essas APIs são interoperáveis com a API do SQL. |
    |||

1. Selecione **Avançar: configuração**.
1. Em **criar um ponto de extremidade privado (visualização)-configuração**, insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    |**Redes**| |
    | Rede virtual| Selecione sua rede virtual. |
    | Subrede | Selecione sua sub-rede. |
    |**Integração do DNS privado**||
    |Integrar com a zona DNS privada |Selecione **Sim**. <br><br/> Para conectar-se de forma privada com seu ponto de extremidade particular, você precisa de um registro DNS. Recomendamos que você integre seu ponto de extremidade privado a uma zona DNS privada. Você também pode usar seus próprios servidores DNS ou criar registros DNS usando os arquivos de host em suas máquinas virtuais. |
    |Zona de DNS privado |Selecione **privatelink.Documents.Azure.com**. <br><br/> A zona DNS privada é determinada automaticamente. Você não pode alterá-lo usando o portal do Azure.|
    |||

1. Selecione **Rever + criar**. Na página **revisar + criar** , o Azure valida sua configuração.
1. Quando você vir a mensagem **validação aprovada** , selecione **criar**.

Quando você tiver aprovado o link privado para uma conta do Azure Cosmos, na portal do Azure, a opção **todas as redes** no painel **Firewall e redes virtuais** estará indisponível.

A tabela a seguir mostra o mapeamento entre diferentes tipos de API de conta do Azure Cosmos, os sub-recursos com suporte e os nomes de zona privada correspondentes. Você também pode acessar as contas Gremlin e API de Tabela por meio da API do SQL, portanto, há duas entradas para essas APIs.

|Tipo de API de conta do Azure Cosmos  |Sub-recursos com suporte (ou IDs de grupo) |Nome da zona privada  |
|---------|---------|---------|
|SQL    |   SQL      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Mongo   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  SQL       |  privatelink.documents.azure.com    |
|Tabela    |    Tabela     |   privatelink.table.cosmos.azure.com    |
|Tabela     |   SQL      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>Buscar os endereços IP privados

Depois que o ponto de extremidade privado for provisionado, você poderá consultar os endereços IP. Para exibir os endereços IP do portal do Azure:

1. Selecione **Todos os recursos**.
1. Pesquise o ponto de extremidade privado que você criou anteriormente. Nesse caso, é **cdbPrivateEndpoint3**.
1. Selecione a guia **visão geral** para ver as configurações de DNS e os endereços IP.

![Endereços IP privados no portal do Azure](./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png)

Vários endereços IP são criados por ponto de extremidade privado:

* Um para o ponto de extremidade global (independente de região) da conta do Azure Cosmos
* Um para cada região em que a conta do Azure cosmos é implantada

## <a name="create-a-private-endpoint-by-using-azure-powershell"></a>Criar um ponto de extremidade privado usando Azure PowerShell

Execute o seguinte script do PowerShell para criar um ponto de extremidade privado chamado "MyPrivateEndpoint" para uma conta existente do Azure Cosmos. Substitua os valores de variáveis pelos detalhes do seu ambiente.

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

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Integrar o ponto de extremidade privado a uma zona DNS privada

Depois de criar o ponto de extremidade privado, você pode integrá-lo a uma zona DNS privada usando o seguinte script do PowerShell:

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

Depois que o ponto de extremidade privado for provisionado, você poderá consultar os endereços IP e o mapeamento de FQDN usando o seguinte script do PowerShell:

```azurepowershell-interactive

$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-endpoint-by-using-a-resource-manager-template"></a>Criar um ponto de extremidade privado usando um modelo do Resource Manager

Você pode configurar o link privado criando um ponto de extremidade privado em uma sub-rede de rede virtual. Você consegue fazer isso usando um modelo de Azure Resource Manager.

Use o código a seguir para criar um modelo do Resource Manager chamado "PrivateEndpoint_template. JSON". Este modelo cria um ponto de extremidade privado para uma conta existente da API do SQL do Azure Cosmos em uma rede virtual existente.

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

**Definir o arquivo de parâmetros para o modelo**

Crie um arquivo de parâmetros para o modelo e nomeie-o como "PrivateEndpoint_parameters. JSON". Adicione o seguinte código ao arquivo de parâmetros:

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

**Implantar o modelo usando um script do PowerShell**

Crie um script do PowerShell usando o código a seguir. Antes de executar o script, substitua a ID da assinatura, o nome do grupo de recursos e outros valores de variáveis pelos detalhes do seu ambiente.

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

No script do PowerShell, a variável `GroupId` pode conter apenas um valor. Esse valor é o tipo de API da conta. Os valores permitidos são: `Sql`, `MongoDB`, `Cassandra`, `Gremlin`e `Table`. Alguns tipos de conta do Azure Cosmos podem ser acessados por meio de várias APIs. Por exemplo:

* Uma conta de API do Gremlin pode ser acessada de contas do Gremlin e da API do SQL.
* Uma conta de API de Tabela pode ser acessada de contas da API do SQL e da tabela.

Para essas contas, você deve criar um ponto de extremidade privado para cada tipo de API. O tipo de API correspondente é especificado na matriz de `GroupId`.

Depois que o modelo for implantado com êxito, você poderá ver uma saída semelhante à mostrada na imagem a seguir. O valor de `provisioningState` será `Succeeded` se os pontos de extremidade privados estiverem configurados corretamente.

![Saída de implantação para o modelo do Resource Manager](./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png)

Depois que o modelo é implantado, os endereços IP privados são reservados na sub-rede. A regra de firewall da conta do Azure cosmos é configurada para aceitar conexões somente do ponto de extremidade privado.

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Integrar o ponto de extremidade privado a uma zona de DNS privado

Use o código a seguir para criar um modelo do Resource Manager chamado "PrivateZone_template. JSON". Este modelo cria uma zona DNS privada para uma conta existente da API do SQL do Azure Cosmos em uma rede virtual existente.

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

Use o código a seguir para criar um modelo do Resource Manager chamado "PrivateZoneRecords_template. JSON".

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

**Definir o arquivo de parâmetros para o modelo**

Crie os dois arquivos de parâmetros a seguir para o modelo. Crie o "PrivateZone_parameters. JSON". com o código a seguir:

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

Crie o "PrivateZoneRecords_parameters. JSON". com o código a seguir:

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

**Implantar o modelo usando um script do PowerShell**

Crie um script do PowerShell usando o código a seguir. Antes de executar o script, substitua a ID da assinatura, o nome do grupo de recursos e outros valores de variáveis pelos detalhes do seu ambiente.

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

Você deve usar uma zona DNS privada na sub-rede em que você criou o ponto de extremidade privado. Configure os pontos de extremidade para que cada endereço IP privado seja mapeado para uma entrada DNS. (Consulte a propriedade `fqdns` na resposta mostrada anteriormente.)

Ao criar o ponto de extremidade privado, você pode integrá-lo a uma zona DNS privada no Azure. Se você optar por usar uma zona DNS personalizada, precisará configurá-la para adicionar registros DNS para todos os endereços IP privados reservados para o ponto de extremidade privado.

## <a name="private-link-combined-with-firewall-rules"></a>Link privado combinado com regras de firewall

As seguintes situações e resultados são possíveis quando você usa o link privado em combinação com regras de firewall:

* Se você não configurar nenhuma regra de firewall, por padrão, todo o tráfego poderá acessar uma conta do Azure Cosmos.

* Se você configurar o tráfego público ou um ponto de extremidade de serviço e criar pontos de extremidades privados, diferentes tipos de tráfego de entrada serão autorizados pelo tipo de regra de firewall correspondente.

* Se você não configurar nenhum tráfego público ou ponto de extremidade de serviço e criar pontos de extremidades privados, a conta do Azure Cosmos será acessível somente por meio dos pontos de extremidade privados. Se você não configurar o tráfego público ou um ponto de extremidade de serviço, depois que todos os pontos de extremidades particulares aprovados forem rejeitados ou excluídos, a conta será aberta para toda a rede.

## <a name="update-a-private-endpoint-when-you-add-or-remove-a-region"></a>Atualizar um ponto de extremidade privado ao adicionar ou remover uma região

Adicionar ou remover regiões para uma conta do Azure Cosmos exige que você adicione ou remova entradas DNS para essa conta. Atualize essas alterações adequadamente no ponto de extremidade privado usando as seguintes etapas:

1. Quando o administrador de Azure Cosmos DB adiciona ou remove regiões, o administrador de rede Obtém uma notificação sobre as alterações pendentes. Para o ponto de extremidade privado mapeado para uma conta do Azure Cosmos, o valor da propriedade `ActionsRequired` muda de `None` para `Recreate`. Em seguida, o administrador de rede atualiza o ponto de extremidade privado emitindo uma solicitação PUT com o mesmo conteúdo do Gerenciador de recursos que foi usado para criá-lo.

1. Depois que o ponto de extremidade privado for atualizado, você poderá atualizar a zona DNS privada da sub-rede para refletir as entradas DNS adicionadas ou removidas e seus endereços IP privados correspondentes.

Por exemplo, imagine que você implante uma conta do Azure Cosmos em três regiões: "oeste dos EUA", "EUA Central" e "Europa Ocidental". Quando você cria um ponto de extremidade privado para sua conta, quatro IPs privados são reservados na sub-rede. Há um IP para cada uma das três regiões, e há um IP para o ponto de extremidade global/independente de região.

Posteriormente, você pode adicionar uma nova região (por exemplo, "leste dos EUA") à conta do Azure Cosmos. Por padrão, a nova região não pode ser acessada do ponto de extremidade privado existente. O administrador da conta do Azure Cosmos deve atualizar a conexão de ponto de extremidade privada antes de acessá-la da nova região. 

Quando você executa o comando ` Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>`, a saída do comando contém o parâmetro `actionsRequired`. Esse parâmetro é definido como `Recreate`. Esse valor indica que o ponto de extremidade privado deve ser atualizado. Em seguida, o administrador da conta do Azure Cosmos executa o comando `Set-AzPrivateEndpoint` para disparar a atualização do ponto de extremidade privado.

```powershell
$pe = Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>

Set-AzPrivateEndpoint -PrivateEndpoint $pe
```

Um novo IP privado é reservado automaticamente na sub-rede sob esse ponto de extremidade privado. O valor de `actionsRequired` se torna `None`. Se você não tiver nenhuma integração de zona DNS privada (em outras palavras, se estiver usando uma zona DNS privada personalizada), você precisará configurar sua zona DNS privada para adicionar um novo registro DNS para o IP privado que corresponde à nova região.

Você pode usar as mesmas etapas ao remover uma região. O IP privado da região removida é recuperado automaticamente e o sinalizador de `actionsRequired` se torna `None`. Se você não tiver nenhuma integração de zona DNS privada, deverá configurar sua zona DNS privada para remover o registro DNS para a região removida.

Os registros DNS na zona DNS privada não são removidos automaticamente quando um ponto de extremidade privado é excluído ou uma região da conta do Azure cosmos é removida. Você deve remover manualmente os registros DNS.

## <a name="current-limitations"></a>Limitações atuais

As seguintes limitações se aplicam quando você está usando o link privado com uma conta do Azure Cosmos:

* O suporte de link privado para contas do Azure Cosmos e redes virtuais está disponível somente em regiões específicas. Para obter uma lista de regiões com suporte, consulte a seção [regiões disponíveis](../private-link/private-link-overview.md#availability) do artigo de link privado. 

  > [!NOTE]
  > Para criar um ponto de extremidade privado, verifique se a rede virtual e a conta do Azure Cosmos estão em regiões com suporte.

* Quando você estiver usando um link privado com uma conta do Azure Cosmos usando uma conexão de modo direto, poderá usar apenas o protocolo TCP. O protocolo HTTP ainda não tem suporte.

* Quando você estiver usando a API do Azure Cosmos DB para contas do MongoDB, um ponto de extremidade privado terá suporte para contas somente no servidor versão 3,6 (ou seja, contas que usam o ponto de extremidade no formato `*.mongo.cosmos.azure.com`). O link privado não tem suporte para contas no servidor versão 3,2 (ou seja, contas que usam o ponto de extremidade no formato `*.documents.azure.com`). Para usar o link privado, você deve migrar contas antigas para a nova versão.

* Quando você estiver usando a API do Azure Cosmos DB para contas do MongoDB que têm o link privado, não poderá usar ferramentas como Robo 3T, Studio 3T e Mongoose. O ponto de extremidade poderá ter suporte a vínculo privado somente se o parâmetro `appName=<account name>` for especificado. Um exemplo é `replicaSet=globaldb&appName=mydbaccountname`. Como essas ferramentas não passam o nome do aplicativo na cadeia de conexão para o serviço, você não pode usar o link privado. Mas você ainda pode acessar essas contas usando drivers do SDK com a versão 3,6.

* Você não poderá mover ou excluir uma rede virtual se ela contiver um link privado.

* Você não poderá excluir uma conta do Azure cosmos se ela estiver anexada a um ponto de extremidade privado.

* Não é possível fazer failover de uma conta do Azure Cosmos para uma região que não está mapeada para todos os pontos de extremidade privados anexados à conta.

* Um administrador de rede deve receber pelo menos a permissão "*/PrivateEndpointConnectionsApproval" no escopo da conta do Azure Cosmos para criar pontos de extremidade privados aprovados automaticamente.

### <a name="limitations-to-private-dns-zone-integration"></a>Limitações para a integração de zona DNS privada

Os registros DNS na zona DNS privada não são removidos automaticamente quando você exclui um ponto de extremidade privado ou remove uma região da conta do Azure Cosmos. Você deve remover manualmente os registros DNS antes:

* Adicionando um novo ponto de extremidade privado vinculado a esta zona DNS privada.
* Adicionar uma nova região a qualquer conta de banco de dados que tenha pontos de extremidade privados vinculados a essa zona DNS privada.

Se você não limpar os registros DNS, poderão ocorrer problemas inesperados no plano de dados. Esses problemas incluem a interrupção de dados para regiões adicionadas após remoção de ponto de extremidade particular ou remoção de região.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre Azure Cosmos DB recursos de segurança, consulte os seguintes artigos:

* Para configurar um firewall para Azure Cosmos DB, consulte [suporte a firewall](firewall-support.md).

* Para saber como configurar um ponto de extremidade de serviço de rede virtual para sua conta do Azure Cosmos, consulte [Configurar o acesso de redes virtuais](how-to-configure-vnet-service-endpoint.md).

* Para saber mais sobre o link privado, consulte a documentação do [link privado do Azure](../private-link/private-link-overview.md) .
