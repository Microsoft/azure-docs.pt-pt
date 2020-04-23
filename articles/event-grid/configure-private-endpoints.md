---
title: Configure pontos finais privados para tópicos ou domínios da Grelha de Eventos Do Azure
description: Este artigo descreve como configurar pontos finais privados para tópicos ou domínio sintetizar a Grelha de Eventos.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 04/22/2020
ms.author: spelluru
ms.openlocfilehash: 97f08bf0f89fdb65f0ffef7d18557f210e45a8d3
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82101013"
---
# <a name="configure-private-endpoints-for-azure-event-grid-topics-or-domains-preview"></a>Configure pontos finais privados para tópicos ou domínios da Grelha de Eventos Do Azure (Pré-visualização)
Você pode usar [pontos finais privados](../private-link/private-endpoint-overview.md) para permitir a entrada de eventos diretamente da sua rede virtual para os seus tópicos e domínios de forma segura através de um [link privado](../private-link/private-link-overview.md) sem passar pela internet pública. O ponto final privado utiliza um endereço IP do espaço de endereço VNet para o seu tópico ou domínio. Para obter mais informações conceptuais, consulte a [segurança da Rede.](network-security.md)

Este artigo descreve como configurar pontos finais privados para tópicos ou domínios.

> [!IMPORTANT]
> A funcionalidade de pontos finais privados está disponível para tópicos e domínios apenas em nível premium. Para atualizar de nível básico para nível premium, consulte o artigo do nível de [preços da Atualização.](update-tier.md) 

## <a name="use-azure-portal"></a>Utilizar o portal do Azure 
Esta secção mostra-lhe como usar o portal Azure para criar um ponto final privado para um tópico ou domínio.

> [!NOTE]
> Os passos mostrados nesta secção são principalmente para tópicos. Pode utilizar passos semelhantes para criar pontos finais privados para **domínios.** 

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue para o seu tópico ou domínio.
2. Mude para o separador **De Rede** da sua página de tópicos. Selecione **+ ponto final privado** na barra de ferramentas.

    ![Adicionar ponto final privado](./media/configure-private-endpoints/add-button.png)
2. Uma página do **Básico,** siga estes passos: 
    1. Selecione uma **subscrição Azure** na qual pretende criar o ponto final privado. 
    2. Selecione um **grupo de recursos Azure** para o ponto final privado. 
    3. Insira um **nome** para o ponto final. 
    4. Selecione a **região** para o ponto final. O seu ponto final privado deve estar na mesma região que a sua rede virtual, mas pode numa região diferente do recurso de ligação privada (neste exemplo, um tópico de grelha de eventos). 
    5. Em seguida, selecione **Seguinte: O** botão de >de recursos na parte inferior da página. 

      ![Ponto final privado - página básica](./media/configure-private-endpoints/basics-page.png)
3. Na página **Recurso,** siga estes passos: 
    1. Para obter o método de ligação, se selecionar **Ligar a um recurso Azure no meu diretório,** siga estes passos. Este exemplo mostra como se conectar a um recurso Azure no seu diretório. 
        1. Selecione a **subscrição Azure** na qual o seu **tópico/domínio** existe. 
        1. Para **o tipo de recurso**, selecione **Microsoft.EventGrid/topics** ou **Microsoft.EventGrid/domínios** para o **tipo de Recurso**.
        2. Para **Recurso,** selecione um tópico/domínio da lista de lançamentos. 
        3. Confirme que o **subrecurso Target** está definido para **tópico** ou **domínio** (com base no tipo de recurso selecionado).    
        4. Selecione **Seguinte: Configuração >** botão na parte inferior da página. 

            ![Ponto final privado - página de recursos](./media/configure-private-endpoints/resource-page.png)
    2. Se selecionar **Ligar a um recurso utilizando um ID de recurso ou um pseudónimo,** siga estes passos:
        1. Insira a identificação do recurso. Por exemplo: `/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>`.  
        2. Para **Recurso,** insira **tópico** ou **domínio.** 
        3. (opcional) Adicione uma mensagem de pedido. 
        4. Selecione **Seguinte: Configuração >** botão na parte inferior da página. 

            ![Ponto final privado - página de recursos](./media/configure-private-endpoints/connect-azure-resource-id.png)
4. Na página **de Configuração,** selecione a subnet numa rede virtual para onde pretende implementar o ponto final privado. 
    1. Selecione uma **rede virtual**. Apenas redes virtuais na subscrição e localização atualmente selecionadas estão listadas na lista de drop-down. 
    2. Selecione uma **sub-rede** na rede virtual selecionada. 
    3. Selecione **Seguinte: Etiquetas >** botão na parte inferior da página. 

    ![Ponto final privado - página de configuração](./media/configure-private-endpoints/configuration-page.png)
5. Na página **Tags,** crie quaisquer tags (nomes e valores) que queira associar ao recurso de ponto final privado. Em seguida, selecione **Review + crie** o botão na parte inferior da página. 
6. No **Review + criar,** reveja todas as definições e selecione **Criar** para criar o ponto final privado. 

    ![Endpoint privado - rever & criar página](./media/configure-private-endpoints/review-create-page.png)
    

### <a name="manage-private-link-connection"></a>Gerir a ligação de ligação privada

Quando se cria um ponto final privado, a ligação tem de ser aprovada. Se o recurso para o qual está a criar um ponto final privado estiver no seu diretório, pode aprovar o pedido de ligação desde que tenha permissões suficientes. Se estiver a ligar-se a um recurso Azure noutro diretório, tem de esperar que o proprietário desse recurso aprove o seu pedido de ligação.

Existem quatro estados de provisionamento:

| Ação de serviço | Estado de ponto final privado do consumidor de serviço | Descrição |
|--|--|--|
| Nenhuma | Pendente | A ligação é criada manualmente e está pendente de aprovação do proprietário de recursos de Link privado. |
| Aprovar | Aprovado | A ligação foi aprovada automaticamente ou manualmente e está pronta a ser utilizada. |
| Rejeitar | Rejected | A ligação foi rejeitada pelo proprietário de recursos de ligação privada. |
| Remover | Desligado | A ligação foi removida pelo proprietário do recurso de ligação privada, o ponto final privado torna-se informativo e deve ser eliminado para limpeza. |
 
###  <a name="how-to-manage-a-private-endpoint-connection"></a>Como gerir uma ligação de ponto final privado
As seguintes secções mostram-lhe como aprovar ou rejeitar uma ligação de ponto final privado. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Na barra de pesquisa, digite tópicos de Grelha de **Eventos** ou **domínios da Grelha de Eventos**.
1. Selecione o **tópico** ou **domínio** que pretende gerir.
1. Selecione o separador **Networking.**
1. Se houver alguma ligação pendente, verá uma ligação listada com **pendente** no estado de provisionamento. 

### <a name="to-approve-a-private-endpoint"></a>Para aprovar um ponto final privado
Pode aprovar um ponto final privado que esteja no estado pendente. Para aprovar, siga estes passos: 

> [!NOTE]
> Os passos mostrados nesta secção são principalmente para tópicos. Pode utilizar passos semelhantes para aprovar pontos finais privados para **domínios**. 

1. Selecione o **ponto final privado** que pretende aprovar e selecione **Aprovar** na barra de ferramentas.

    ![Ponto final privado - estado pendente](./media/configure-private-endpoints/pending.png)
1. Na caixa de diálogo de **ligação Approve,** introduza um comentário (opcional) e selecione **Sim**. 

    ![Ponto final privado - aprove](./media/configure-private-endpoints/approve.png)
1. Confirme que vê o estado do ponto final como **Aprovado**. 

    ![Ponto final privado - estado aprovado](./media/configure-private-endpoints/approved-status.png)

### <a name="to-reject-a-private-endpoint"></a>Rejeitar um ponto final privado
Pode rejeitar um ponto final privado que esteja no estado pendente ou no estado aprovado. Para rejeitar, siga estes passos: 

> [!NOTE]
> Os passos mostrados nesta secção são para tópicos. Pode utilizar passos semelhantes para rejeitar pontos finais privados para **domínios**. 

1. Selecione o **ponto final privado** que deseja rejeitar e selecione **Rejeitar** na barra de ferramentas.

    ![Ponto final privado - rejeitar](./media/configure-private-endpoints/reject-button.png)
1. Na caixa de diálogo de **ligação Rejeitar,** introduza um comentário (opcional) e selecione **Sim**. 

    ![Ponto final privado - rejeitar](./media/configure-private-endpoints/reject.png)
1. Confirme que vê o estado do ponto final como **Rejeitado**. 

    ![Ponto final privado - estado rejeitado](./media/configure-private-endpoints/rejected-status.png)

    > [!NOTE]
    > Não se pode aprovar um ponto final privado no portal Azure uma vez rejeitado. 


## <a name="use-azure-cli"></a>Utilizar a CLI do Azure
Para criar um ponto final privado, utilize o método de criação de pontos [de criação da rede Az,](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) tal como mostrado no seguinte exemplo:

```azurecli-interactive
az network private-endpoint create \
    --resource-group <RESOURECE GROUP NAME> \
    --name <PRIVATE ENDPOINT NAME> \
    --vnet-name <VIRTUAL NETWORK NAME> \
    --subnet <SUBNET NAME> \
    --private-connection-resource-id "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME> \
    --connection-name <PRIVATE LINK SERVICE CONNECTION NAME> \
    --location <LOCATION> \
    --group-ids topic
```

Para descrições dos parâmetros utilizados no exemplo, consulte a documentação para criar um ponto de ligação privado da [rede Az.](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) Alguns pontos a notar neste exemplo são: 

- Para `private-connection-resource-id`especificar a identificação do recurso do **tópico** ou **domínio**. O exemplo anterior usa o tipo: tópico.
- para, `group-ids` `topic` especificar `domain`ou . No exemplo anterior, `topic` é utilizado. 

Para eliminar um ponto final privado, utilize o método [de eliminação de pontos finais privados da rede Az,](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-delete) tal como mostrado no seguinte exemplo:

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> Os passos mostrados nesta secção são para tópicos. Pode utilizar passos semelhantes para criar pontos finais privados para **domínios.** 



### <a name="prerequisites"></a>Pré-requisitos
Atualize a extensão da Grelha de Eventos Azure para o CLI executando o seguinte comando: 

```azurecli-interactive
az extension update -n eventgrid
```

Se a extensão não estiver instalada, execute o seguinte comando para instalá-la: 

```azurecli-interactive
az extension add -n eventgrid
```

### <a name="create-a-private-endpoint"></a>Criar um ponto final privado
Para criar um ponto final privado, utilize o método de criação de pontos [de criação da rede Az,](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) tal como mostrado no seguinte exemplo:

```azurecli-interactive
az network private-endpoint create \
    --resource-group <RESOURECE GROUP NAME> \
    --name <PRIVATE ENDPOINT NAME> \
    --vnet-name <VIRTUAL NETWORK NAME> \
    --subnet <SUBNET NAME> \
    --private-connection-resource-id "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME> \
    --connection-name <PRIVATE LINK SERVICE CONNECTION NAME> \
    --location <LOCATION> \
    --group-ids topic
```

Para descrições dos parâmetros utilizados no exemplo, consulte a documentação para criar um ponto de ligação privado da [rede Az.](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) Alguns pontos a notar neste exemplo são: 

- Para `private-connection-resource-id`especificar a identificação do recurso do **tópico** ou **domínio**. O exemplo anterior usa o tipo: tópico.
- para, `group-ids` `topic` especificar `domain`ou . No exemplo anterior, `topic` é utilizado. 

Para eliminar um ponto final privado, utilize o método [de eliminação de pontos finais privados da rede Az,](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-delete) tal como mostrado no seguinte exemplo:

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> Os passos mostrados nesta secção são para tópicos. Pode utilizar passos semelhantes para criar pontos finais privados para **domínios.** 

#### <a name="sample-script"></a>Script de exemplo
Aqui está um script de amostra que cria os seguintes recursos Azure:

- Grupo de recursos
- Rede virtual
- Subnet na rede virtual
- Tópico da Grelha de Eventos Azure (nível premium)
- Ponto final privado para o tema

> [!NOTE]
> Os passos mostrados nesta secção são para tópicos. Pode usar passos semelhantes para criar pontos finais privados para domínios.

```azurecli-interactive
subscriptionID="<AZURE SUBSCRIPTION ID>"
resourceGroupName="<RESOURCE GROUP NAME>"
location="<LOCATION>"
vNetName="<VIRTUAL NETWORK NAME>"
subNetName="<SUBNET NAME>"
topicName = "<TOPIC NAME>"
connectionName="<ENDPOINT CONNECTION NAME>"
endpointName=<ENDPOINT NAME>

# resource ID of the topic. replace <SUBSCRIPTION ID>, <RESOURCE GROUP NAME>, and <TOPIC NAME>
topicResourceID="/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME>"

# select subscription
az account set --subscription $subscriptionID

# create resource group
az group create --name $resourceGroupName --location $location

# create vnet 
az network vnet create \
    --resource-group $resourceGroupName \
    --name $vNetName \
    --address-prefix 10.0.0.0/16

# create subnet
az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --address-prefixes 10.0.0.0/24

# disable private endpoint network policies for the subnet
az network vnet subnet update \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --disable-private-endpoint-network-policies true

# create event grid topic. update <LOCATION>
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --sku "Premium" 

# verify that the topic was created.
az eventgrid topic show \
    --resource-group $resourceGroupName \
    --name $topicName

# create private endpoint for the topic you created
az network private-endpoint create 
    --resource-group $resourceGroupName \
    --name $endpointName \
    --vnet-name $vNetName \
    --subnet $subNetName \
    --private-connection-resource-id $topicResourceID \
    --connection-name $connectionName \
    --location $location \
    --group-ids topic

# get topic 
az eventgrid topic show \
    --resource-group $resourceGroupName \
    --name $topicName

```

### <a name="approve-a-private-endpoint"></a>Aprovar um ponto final privado
O seguinte corte CLI mostra-lhe como aprovar uma ligação de ponto final privado. 

```azurecli-interactive
az eventgrid topic private-endpoint-connection approve \
    --resource-group $resourceGroupName \
    --topic-name $topicName \
    --name  $endpointName \
    --description "connection approved"
```


### <a name="reject-a-private-endpoint"></a>Rejeitar um ponto final privado
A amostra seguinte, o corte CLI mostra-lhe como rejeitar uma ligação de ponto final privado. 

```azurecli-interactive
az eventgrid topic private-endpoint-connection reject \
    --resource-group $resourceGroupName \
    --topic-name $topicName \
    --name $endpointName \
    --description "Connection rejected"
```

### <a name="disable-public-network-access"></a>Desativar o acesso à rede pública
Por predefinição, o acesso à rede pública está ativado para um tópico ou domínio da Grelha de Eventos. Para permitir o acesso apenas através de pontos finais privados, desative o acesso da rede pública executando o seguinte comando:  

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access disabled
```


## <a name="use-powershell"></a>Utilizar o PowerShell
Esta secção mostra-lhe como criar um ponto final privado para um tópico ou domínio usando powerShell. 

### <a name="prerequisite"></a>Pré-requisito
Siga as instruções de [Como: Utilize o portal para criar uma aplicação e um diretor](../active-directory/develop/howto-create-service-principal-portal.md) de serviço da Azure AD que possam aceder a recursos para criar uma aplicação de Diretório Ativo Azure e observar os valores para o Id de **Diretório (inquilino),** **ID de aplicação (cliente)** e segredo de **aplicação (cliente).** 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Prepare token e cabeçalhos para chamadas REST API 
Executar os seguintes comandos pré-requisitos para obter um símbolo de autenticação para usar com chamadas e autorização da API REST e outras informações de cabeçalho. 

```azurepowershell-interactive
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get authentication token
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token  `
    -Body $body  `
    -ContentType 'application/x-www-form-urlencoded' 

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
```

### <a name="create-a-subnet-with-endpoint-network-policies-disabled"></a>Criar uma subrede com políticas de rede de pontofinal desativadas

```azurepowershell-interactive

# create resource group
New-AzResourceGroup -ResourceGroupName <RESOURCE GROUP NAME>  -Location <LOCATION>

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName <RESOURCE GROUP NAME> `
                    -Location <LOCATION> `
                    -Name <VIRTUAL NETWORK NAME> `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name example-privatelinksubnet `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork
```

### <a name="create-an-event-grid-topic-with-a-private-endpoint"></a>Crie um tópico de grelha de evento com um ponto final privado

> [!NOTE]
> Os passos mostrados nesta secção são para tópicos. Pode utilizar passos semelhantes para criar pontos finais privados para **domínios.** 


```azurepowershell-interactive
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="premium"}; "properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json

# create topic
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview"  `
    -Headers $Headers  `
    -Body $body

# verify that the topic was created
$topic=Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview"   `
    -Headers $Headers  

# create private link service connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name "<PRIVATE LINK SERVICE CONNECTION NAME>" `
                                -PrivateLinkServiceId $topic.Id `
                                -GroupId "topic"

# get virtual network info 
$virtualNetwork = Get-AzVirtualNetwork  `
                    -ResourceGroupName  <RESOURCE GROUP NAME> `
                    -Name <VIRTUAL NETWORK NAME>

# get subnet info
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                             | Where-Object  {$_.Name -eq <SUBNET NAME>}  

# now, you are ready to create a private endpoint 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName <RESOURCE GROUP NAME>  `
                                        -Name <PRIVATE ENDPOINT NAME>   `
                                        -Location <LOCATION> `
                                        -Subnet  $subnet   `
                                        -PrivateLinkServiceConnection $privateEndpointConnection

# verify that the endpoint was created
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections?api-version=2020-04-01-preview"   `
    -Headers $Headers   `
    | ConvertTo-Json -Depth 5

```

Quando verificar que o ponto final foi criado, deve ver o resultado semelhante ao seguinte:

```json

{
  "value": [
    {
      "properties": {
        "privateEndpoint": {
          "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Network/privateEndpoints/<PRIVATE ENDPOINT NAME>"
        },
        "groupIds": [
          "topic"
        ],
        "privateLinkServiceConnectionState": {
          "status": "Approved",
          "description": "Auto-approved",
          "actionsRequired": "None"
        },
        "provisioningState": "Succeeded"
      },
      "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>",
      "name": "myConnection",
      "type": "Microsoft.EventGrid/topics/privateEndpointConnections"
    }
  ]
}
```

### <a name="approve-a-private-endpoint-connection"></a>Aprovar uma ligação de ponto final privado
A amostra seguinte O corte PowerShell mostra-lhe como aprovar um ponto final privado. 

> [!NOTE]
> Os passos mostrados nesta secção são para tópicos. Pode utilizar passos semelhantes para aprovar pontos finais privados para **domínios**. 

```azurepowershell-interactive
$approvedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="approved";"description"="connection approved";"actionsRequired"="none"}}} | ConvertTo-Json

# approve endpoint connection
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AzuRE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview"  `
    -Headers $Headers  `
    -Body $approvedBody

# confirm that the endpoint connection was approved
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview"  `
    -Headers $Headers

```

### <a name="reject-a-private-endpoint-connection"></a>Rejeitar uma ligação de ponto final privado
O exemplo que se segue mostra como rejeitar um ponto final privado usando o PowerShell. Pode obter o GUID para o ponto final privado a partir do resultado do comando GET anterior. 

> [!NOTE]
> Os passos mostrados nesta secção são para tópicos. Pode utilizar passos semelhantes para rejeitar pontos finais privados para **domínios**. 


```azurepowershell-interactive
$rejectedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="rejected";"description"="connection rejected";"actionsRequired"="none"}}} | ConvertTo-Json

# reject private endpoint
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview"  `
    -Headers $Headers  `
    -Body $rejectedBody

# confirm that endpoint was rejected
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview" ` 
    -Headers $Headers
```

Pode aprovar a ligação mesmo depois de rejeitada através da API. Se usar o portal Azure, não pode aprovar um ponto final que tenha sido rejeitado. 

## <a name="next-steps"></a>Passos seguintes
Para saber como configurar as definições de firewall IP, consulte a [firewall IP configure para tópicos ou domínios da Grelha de Eventos do Azure](configure-firewall.md).