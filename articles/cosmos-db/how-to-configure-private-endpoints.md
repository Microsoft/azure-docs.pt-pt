---
title: Configurar o link privado do Azure para uma conta do Azure Cosmos
description: Saiba como configurar o link privado do Azure para acessar uma conta do Azure Cosmos usando um endereço IP privado em uma rede virtual.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: thweiss
ms.openlocfilehash: 34b54459629560ba80e6a38d10edbab32ea44778
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820159"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account-preview"></a>Configurar o link privado do Azure para uma conta do Azure Cosmos (versão prévia)

Usando o link privado do Azure, você pode se conectar a uma conta do Azure Cosmos por meio de um ponto de extremidade privado. O ponto de extremidade privado é um conjunto de endereços IP privados em uma sub-rede dentro de sua rede virtual. Usando o link privado, você pode limitar o acesso a uma determinada conta do Azure Cosmos sobre endereços IP privados. Quando combinado com políticas NSG restritas, o link privado ajuda a reduzir o risco de vazamento de dados. Para saber mais sobre pontos de extremidade privados, consulte o artigo [link privado do Azure](../private-link/private-link-overview.md) .

Além disso, o link privado permite que uma conta do Azure Cosmos seja acessível de dentro da rede virtual ou de qualquer rede virtual emparelhada. Os recursos mapeados para o link privado também podem ser acessados do local sobre o emparelhamento privado por meio de VPN ou ExpressRoute. 

Você pode se conectar a uma conta do Azure Cosmos configurada com o link privado usando os métodos de aprovação "automático" ou "manual". Para saber mais, confira a seção [fluxo de trabalho de aprovação](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow) da documentação do link privado. Este artigo descreve as etapas para criar um link privado supondo que você esteja usando o método de aprovação automática.

## <a name="create-a-private-link-using-the-azure-portal"></a>Criar um link privado usando o portal do Azure

Use as etapas a seguir para criar um link privado para uma conta existente do Azure Cosmos usando portal do Azure:

1. No painel **todos os recursos** , localize a conta de Azure Cosmos DB que você deseja proteger.

1. Selecione **conexão de ponto de extremidade privado** no menu configurações e selecione opção de **ponto de extremidade particular** :

   ![Criar um ponto de extremidade privado usando portal do Azure](./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png)

1. Em **criar um ponto de extremidade privado (visualização) – noções básicas**, painel, insira ou selecione os seguintes detalhes:

    | Definição | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione um grupo de recursos.|
    | **Detalhes da instância** |  |
    | Nome | Insira qualquer nome para seu ponto de extremidade particular; Se esse nome for obtido, crie um exclusivo. |
    |Região| Selecione a região onde você deseja implantar o link privado. O ponto de extremidade privado deve ser criado no mesmo local em que a rede virtual existe.|
    |||
1. Selecione **Avançar: recurso**.
1. Em **criar um ponto de extremidade privado-recurso**, insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    |Método de ligação  | Selecione conectar a um recurso do Azure em meu diretório. <br/><br/> Essa opção permite que você escolha um de seus recursos para configurar um link privado ou se conectar ao recurso de outra pessoa com uma ID de recurso ou alias que eles compartilharam com você.|
    | Subscrição| Selecione a sua subscrição. |
    | Tipo de recurso | Selecione **Microsoft. AzureCosmosDB/databaseAccounts**. |
    | Recurso |Selecione sua conta do Azure Cosmos |
    |Sub-recurso de destino |Selecione o tipo de API Cosmos DB que você deseja mapear. O padrão é apenas uma opção para as APIs SQL, MongoDB e Cassandra. Para as APIs Gremlin e Table, você também pode escolher *SQL* , pois essas APIs são interoperáveis com a API do SQL. |
    |||

1. Selecione **Avançar: configuração**.
1. Em **criar um ponto de extremidade privado (visualização)-configuração**, insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    |**Redes**| |
    | Rede virtual| Selecione sua rede virtual. |
    | Subrede | Selecione sua sub-rede. |
    |**Integração do DNS privado**||
    |Integrar com a zona DNS privada |Selecione **Sim**. <br><br/> Para conectar-se de forma privada com seu ponto de extremidade particular, você precisa de um registro DNS. É recomendável que você integre seu ponto de extremidade privado a uma zona DNS privada. Você também pode utilizar seus próprios servidores DNS ou criar registros DNS usando os arquivos de host em suas máquinas virtuais. |
    |Zona de DNS privado |Selecionar *privatelink.Documents.Azure.com* <br><br/> A zona de DNS privado é determinada automaticamente e não pode ser alterada no momento usando o portal do Azure.|
    |||

1. Selecione **Rever + criar**. Você é levado para a página **revisar + criar** , na qual o Azure valida sua configuração.
1. Quando você vir a mensagem **validação aprovada** , selecione **criar**.

Quando você tiver aprovado links privados para uma conta do Azure Cosmos, na portal do Azure a opção **todas as redes** no painel **Firewall e redes virtuais** ficará esmaecida.

A tabela a seguir mostra o mapeamento entre diferentes tipos de API de conta do Azure Cosmos, os sub-recursos com suporte e os nomes de zona privada correspondentes. As contas Gremlin e API de Tabela podem ser acessadas por meio da API do SQL e, portanto, há duas entradas para essas APIs:

|Tipo de API de conta do Azure Cosmos  |Subrecursos com suporte (ou groupIds) |Nome da zona privada  |
|---------|---------|---------|
|SQL    |   SQL      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Mongo   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  SQL       |  privatelink.documents.azure.com    |
|Tabela    |    Tabela     |   privatelink.table.cosmos.azure.com    |
|Tabela     |   SQL      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>Buscar os endereços IP privados

Depois que o ponto de extremidade privado for provisionado, você poderá consultar os endereços IP. Para exibir os endereços IP de portal do Azure. Selecione **todos os recursos**, procure o ponto de extremidade privado que você criou anteriormente, neste caso, é "dbPrivateEndpoint3" e selecione a guia Visão geral para ver as configurações de DNS e os endereços IP:

![Endereços IP privados no portal do Azure](./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png)

Vários endereços IP são criados por ponto de extremidade privado:

* Uma para o ponto de extremidade global (independente de região) da conta do Azure Cosmos.
* Uma para cada região em que a conta do Azure cosmos é implantada.

## <a name="create-a-private-link-using-azure-powershell"></a>Criar um link privado usando Azure PowerShell

Execute o seguinte script PowerSehll para criar um link privado chamado "MyPrivateEndpoint" para uma conta existente do Azure Cosmos. Certifique-se de substituir os valores de variáveis pelos detalhes específicos do seu ambiente.

```azurepowershell-interactive
Fill in these details, make sure to replace the variable values with the details specific to your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Cosmos DB and VNet resources live
$ResourceGroupName = "myResourceGroup"
# Name of the Cosmos DB account
$CosmosDbAccountName = "mycosmosaccount"

# API type of the Cosmos DB account: Sql or MongoDB or Cassandra or Gremlin or Table
$CosmosDbApiType = "Sql"
# Name of the existing VNet
$VNetName = "myVnet"
# Name of the target subnet in the VNet
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

### <a name="integrate-the-private-endpoint-with-private-dns-zone"></a>Integrar o ponto de extremidade privado com a zona DNS privada

Depois de criar o ponto de extremidade privado, você pode integrá-lo a uma zona DNS privada usando o seguinte script PowerSehll:

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

Depois que o ponto de extremidade privado for provisionado, você poderá consultar os endereços IP e o mapeamento de FQDNS usando o seguinte script do PowerShell:

```azurepowershell-interactive

$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-link-using-a-resource-manager-template"></a>Criar um link privado usando um modelo do Resource Manager

Você pode configurar o link privado criando um ponto de extremidade privado em uma sub-rede de rede virtual. Isso é feito usando um modelo de Azure Resource Manager. Crie um modelo do Resource Manager chamado "PrivateEndpoint_template. JSON" com o código a seguir. Este modelo cria um ponto de extremidade privado para uma conta existente da API do SQL do Azure Cosmos em uma rede virtual existente:

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

### <a name="define-the-template-parameters-file"></a>Definir o arquivo de parâmetros de modelo

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

### <a name="deploy-the-template-by-using-a-powershell-script"></a>Implantar o modelo usando um script do PowerShell

Em seguida, crie um script do PowerShell com o código a seguir. Antes de executar o script, certifique-se de substituir a ID da assinatura, o nome do grupo de recursos e outros valores de variáveis pelos detalhes específicos do seu ambiente:

```azurepowershell-interactive
### This script creates a private endpoint for an existing Cosmos DB account in an existing VNet

## Step 1: Fill in these details, make sure to replace the variable values with the details specific to your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Cosmos DB and VNet resources live
$ResourceGroupName = "myResourceGroup"
# Name of the Cosmos DB account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Cosmos DB account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing VNet
$VNetName = "myVnet"
# Name of the target subnet in the VNet
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

No script do PowerShell, a variável "GroupId" só pode conter um valor, que é o tipo de API da conta. Os valores permitidos são: SQL, MongoDB, Cassandra, Gremlin e Table. Alguns tipos de conta do Azure Cosmos podem ser acessados por meio de várias APIs. Por exemplo:

* Uma conta de API do Gremlin pode ser acessada de contas do Gremlin e da API do SQL.
* Uma conta de API de Tabela pode ser acessada de contas da API do SQL e da tabela.

Para essas contas, você deve criar um ponto de extremidade privado para cada tipo de API, com o tipo de API correspondente especificado na matriz "GroupId".

Depois que o modelo for implantado com êxito, você poderá ver uma saída semelhante à mostrada na imagem a seguir. O valor de provisioningState será "Succeeded" se os pontos de extremidade privados estiverem configurados corretamente.

![Saída de implantação do modelo do Resource Manager](./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png)

Depois que o modelo é implantado, os endereços IP privados são reservados na sub-rede. A regra de firewall da conta do Azure cosmos é configurada para aceitar conexões somente do ponto de extremidade privado.

## <a name="configure-custom-dns"></a>Configurar DNS personalizado

Você deve usar um DNS privado na sub-rede em que o ponto de extremidade privado foi criado. E configure os pontos de extremidade para que cada um dos endereços IP privados seja mapeado para uma entrada DNS (consulte a propriedade "FQDNs" na resposta mostrada acima).

Ao criar o ponto de extremidade privado, você pode integrá-lo a uma zona DNS privada no Azure. Se você optar por não integrar seu ponto de extremidade privado com a zona DNS privada no Azure e, em vez disso, usar um DNS personalizado, será necessário configurar o DNS para adicionar registros DNS para todos os endereços IP privados reservados para o ponto de extremidade privado.

## <a name="firewall-configuration-with-private-link"></a>Configuração de firewall com link privado

A seguir estão situações e resultados diferentes quando você usa o link privado em combinação com regras de firewall:

* Se não houver nenhuma regra de firewall configurada, por padrão, uma conta do Azure cosmos poderá ser acessada por todo o tráfego.

* Se o tráfego público ou o ponto de extremidade de serviço estiver configurado e os pontos de extremidades privados forem criados, os diferentes tipos de tráfego de entrada serão autorizados pelo tipo de regra de firewall correspondente.

* Se nenhum tráfego público ou ponto de extremidade de serviço estiver configurado e pontos de extremidades privados forem criados, a conta do Azure Cosmos só poderá ser acessada por meio dos pontos de extremidade privados. Se nenhum tráfego público ou ponto de extremidade de serviço for configurado, depois que todos os pontos de extremidades particulares aprovados forem rejeitados ou excluídos, a conta será aberta para toda a rede.

## <a name="update-private-endpoint-when-you-add-or-remove-a-region"></a>Atualizar ponto de extremidade privado ao adicionar ou remover uma região

Adicionar ou remover regiões para uma conta do Azure Cosmos exige que você adicione ou remova entradas DNS para essa conta. Essas alterações devem ser atualizadas de acordo com o ponto de extremidade privado. No momento, você deve fazer essa alteração manualmente usando as seguintes etapas:

1. O administrador de Azure Cosmos DB adiciona ou remove regiões. Em seguida, os administradores de rede são notificados sobre as alterações pendentes. O ponto de extremidade privado mapeado para uma conta do Azure Cosmos vê suas propriedades "ActionsRequired" alteradas de "nenhum" para "recriar". Em seguida, o administrador de rede atualiza o ponto de extremidade privado emitindo uma solicitação PUT com a mesma carga do Gerenciador de recursos usada para criá-lo.

1. Após essa operação, o DNS privado da sub-rede também precisa ser atualizado para refletir as entradas DNS adicionadas ou removidas e seus endereços IP privados correspondentes.

Por exemplo, se você implantar uma conta do Azure Cosmos em 3 regiões: "oeste dos EUA", "EUA Central" e "Europa Ocidental". Quando você cria um ponto de extremidade privado para sua conta, 4 IPs privados são reservados na sub-rede. Uma para cada região, que conta para um total de 3 e uma para o ponto de extremidade global/independente de região.

Mais tarde, se você adicionar uma nova região, por exemplo, "leste dos EUA" à conta do Azure Cosmos. Por padrão, a nova região não pode ser acessada do ponto de extremidade privado existente. O administrador da conta do Azure Cosmos deve atualizar a conexão de ponto de extremidade particular antes de acessá-la na nova região. 

Quando você executa o comando ` Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>`, a saída do comando contém o parâmetro `actionsRequired`, que é definido como "recriar". Esse valor indica que o ponto de extremidade privado deve ser atualizado. Em seguida, o administrador da conta do Azure Cosmos executa o comando `Set-AzPrivateEndpoint` para disparar a atualização do ponto de extremidade privado.

```powershell
$pe = Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>

Set-AzPrivateEndpoint -PrivateEndpoint $pe
```

Um novo IP privado é reservado automaticamente na sub-rede sob esse ponto de extremidade privado, e o valor `actionsRequired` se torna `None`. Se você não tiver nenhuma integração de zona DNS privada (em outras palavras, se estiver usando um DNS privado personalizado), você precisará configurar seu DNS privado para adicionar um novo registro DNS para o IP privado correspondente à nova região.

Você pode usar as mesmas etapas ao remover uma região. O IP privado da região removida é recuperado automaticamente e o sinalizador de `actionsRequired` se torna `None`. Se você não tiver nenhuma integração de zona DNS privada, deverá configurar seu DNS privado para remover o registro DNS para a região removida.

Os registros DNS na zona DNS privada não são removidos automaticamente quando um ponto de extremidade privado é excluído ou uma região da conta do Azure cosmos é removida. Você deve remover manualmente os registros DNS.

## <a name="current-limitations"></a>Limitações atuais

As seguintes limitações se aplicam ao usar o link privado com uma conta do Azure Cosmos:

* O suporte ao link privado para contas do Azure Cosmos e VNETs está disponível somente em regiões específicas. Para obter uma lista de regiões com suporte, consulte a seção [regiões disponíveis](../private-link/private-link-overview.md#availability) do artigo de link privado. **Tanto a VNET quanto a conta do Azure Cosmos devem estar nas regiões com suporte para poder criar um ponto de extremidade privado**.

* Ao usar links privados com a conta do Azure Cosmos usando a conexão de modo direto, você só pode usar o protocolo TCP. O protocolo HTTP ainda não tem suporte

* Ao usar a API do Azure Cosmos DB para contas do MongoDB, o ponto de extremidade privado tem suporte para contas somente no servidor versão 3,6 (ou seja, contas que usam o ponto de extremidade no formato `*.mongo.cosmos.azure.com`). O link privado não tem suporte para contas no servidor versão 3,2 (ou seja, contas que usam o ponto de extremidade no formato `*.documents.azure.com`). Para usar o link privado, você deve migrar contas antigas para a nova versão.

* Ao usar a API do Azure Cosmos DB para contas do MongoDB que têm o link privado, você não pode usar ferramentas como Robo 3T, Studio 3T, Mongoose, etc. O ponto de extremidade poderá ter suporte a vínculo privado somente se o parâmetro appName =<account name> for especificado. Por exemplo: réplicaset = globaldb & appName = mydbaccountname. Como essas ferramentas não passam o nome do aplicativo na cadeia de conexão para o serviço para que você não possa usar o link privado. No entanto, você ainda pode acessar essas contas com drivers do SDK com a versão 3,6.

* Uma rede virtual não poderá ser movida ou excluída se contiver um link privado.

* Uma conta do Azure Cosmos não poderá ser excluída se estiver anexada a um ponto de extremidade privado.

* Não é possível fazer failover de uma conta do Azure Cosmos para uma região que não esteja mapeada para todos os pontos de extremidade privados anexados à conta. Para obter mais informações, consulte Adicionando ou removendo regiões na seção anterior.

* Um administrador de rede deve receber pelo menos a permissão "*/PrivateEndpointConnectionsApproval" no escopo da conta do Azure Cosmos por um administrador para criar pontos de extremidade privados aprovados automaticamente.

### <a name="private-dns-zone-integration-limitations"></a>Limitações de integração de zona de DNS privado

Os registros DNS na zona DNS privada não são removidos automaticamente quando um ponto de extremidade privado é excluído ou uma região da conta do Azure cosmos é removida. Você deve remover manualmente os registros DNS antes:

* Adicionando um novo ponto de extremidade privado vinculado a esta zona DNS privada.
* Adicionar uma nova região a qualquer conta de banco de dados que tenha pontos de extremidade privados vinculados a essa zona DNS privada.

Sem limpar os registros DNS, problemas de plano de dados inesperados, como interrupção de dados para regiões adicionadas após remoção de ponto de extremidade particular ou remoção de região podem ocorrer

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os outros Azure Cosmos DB recursos de segurança, consulte o seguinte artigo:

* Para configurar um firewall para Azure Cosmos DB, consulte [suporte a firewall](firewall-support.md).

* [Como configurar o ponto de extremidade de serviço de rede virtual para sua conta do Azure Cosmos.](how-to-configure-vnet-service-endpoint.md)

* Para saber mais sobre o link privado, consulte a documentação do [link privado do Azure](../private-link/private-link-overview.md) .
