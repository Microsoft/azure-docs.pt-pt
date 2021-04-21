---
title: Configurar pontos finais privados para tópicos ou domínios da Grelha de Eventos Azure
description: Este artigo descreve como configurar pontos finais privados para tópicos ou domínios da Grelha de Eventos Azure.
ms.topic: how-to
ms.date: 11/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 85546e99a8c431dc75b1af3d5044e06a18cf226d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770512"
---
# <a name="configure-private-endpoints-for-azure-event-grid-topics-or-domains"></a>Configurar pontos finais privados para tópicos ou domínios da Grelha de Eventos Azure
Você pode usar [pontos finais privados](../private-link/private-endpoint-overview.md) para permitir ingresss de eventos diretamente da sua rede virtual para seus tópicos e domínios de forma segura através de um [link privado](../private-link/private-link-overview.md) sem passar pela internet pública. O ponto final privado utiliza um endereço IP a partir do espaço de endereço VNet para o seu tópico ou domínio. Para obter mais informações conceptuais, consulte [a segurança da rede.](network-security.md)

Este artigo descreve como configurar pontos finais privados para tópicos ou domínios.

## <a name="use-azure-portal"></a>Utilizar o portal do Azure 
Esta secção mostra-lhe como usar o portal Azure para criar um ponto final privado para um tópico ou um domínio.

> [!NOTE]
> Os passos mostrados nesta secção são principalmente para tópicos. Pode utilizar passos semelhantes para criar pontos finais privados para **domínios**. 

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue para o seu tópico ou domínio.
2. Mude para o **separador 'Rede'** da página do tópico. Selecione **+ Ponto final privado** na barra de ferramentas.

    ![Adicionar ponto final privado](./media/configure-private-endpoints/add-button.png)
2. Na página **Basics,** siga estes passos: 
    1. Selecione uma **subscrição Azure** na qual pretende criar o ponto final privado. 
    2. Selecione um **grupo de recursos Azure** para o ponto final privado. 
    3. Insira um **nome** para o ponto final. 
    4. Selecione a **região** para o ponto final. O seu ponto final privado deve estar na mesma região que a sua rede virtual, mas pode numa região diferente do recurso de ligação privada (neste exemplo, um tópico de grelha de eventos). 
    5. Em seguida, selecione **Seguinte: O** botão >de recursos na parte inferior da página. 

      ![Ponto final privado - página básica](./media/configure-private-endpoints/basics-page.png)
3. Na página **de Recursos,** siga estes passos: 
    1. Para o método de ligação, se selecionar **Ligar a um recurso Azure no meu diretório,** siga estes passos. Este exemplo mostra como ligar-se a um recurso Azure no seu diretório. 
        1. Selecione a **subscrição Azure** na qual o seu **tópico/domínio** existe. 
        1. Para **o tipo de recurso**, selecione **Microsoft.EventGrid/topics** ou **Microsoft.EventGrid/domains** para o **tipo de recurso**.
        2. Para **obter recursos,** selecione um tópico/domínio da lista de drop-down. 
        3. Confirme que o **subresource do Target** está definido para **tópico** ou **domínio** (com base no tipo de recurso selecionado).    
        4. Selecione **Seguinte: Configuração >** botão na parte inferior da página. 

            ![Screenshot que mostra a página "Criar um ponto final privado - Recurso".](./media/configure-private-endpoints/resource-page.png)
    2. Se selecionar **Ligar a um recurso utilizando um ID de recurso ou um pseudónimo,** siga estes passos:
        1. Introduza a identificação do recurso. Por exemplo: `/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>`.  
        2. Para **recurso,** introduza **tópico** ou **domínio**. 
        3. (opcional) Adicione uma mensagem de pedido. 
        4. Selecione **Seguinte: Configuração >** botão na parte inferior da página. 

            ![Ponto final privado - página de recursos](./media/configure-private-endpoints/connect-azure-resource-id.png)
4. Na página **Configuração,** seleciona a sub-rede numa rede virtual para onde pretende implantar o ponto final privado. 
    1. Selecione uma **rede virtual.** Apenas as redes virtuais na subscrição e localização atualmente selecionadas estão listadas na lista de drop-down. 
    2. Selecione uma **sub-rede** na rede virtual selecionada. 
    3. Selecione **Seguinte: Tags >** botão na parte inferior da página. 

    ![Ponto final privado - página de configuração](./media/configure-private-endpoints/configuration-page.png)
5. Na página **Tags,** crie quaisquer tags (nomes e valores) que pretenda associar ao recurso de ponto final privado. Em seguida, **selecione Rever + criar** botão na parte inferior da página. 
6. No **Review + criar,** rever todas as definições e selecionar **Criar** para criar o ponto final privado. 

    ![Ponto final privado - rever & criar página](./media/configure-private-endpoints/review-create-page.png)
    

### <a name="manage-private-link-connection"></a>Gerir a ligação de ligação privada

Quando criar um ponto final privado, a ligação tem de ser aprovada. Se o recurso para o qual está a criar um ponto final privado estiver no seu diretório, pode aprovar o pedido de ligação desde que tenha permissões suficientes. Se estiver a ligar-se a um recurso Azure noutro diretório, tem de esperar que o proprietário desse recurso aprove o seu pedido de ligação.

Existem quatro estados de provisionamento:

| Ação do serviço | Estado de ponto final privado do consumidor de serviço | Descrição |
|--|--|--|
| Nenhum | Pendente | A ligação é criada manualmente e está pendente de aprovação do proprietário de recursos link privado. |
| Aprovar | Aprovado | A ligação foi aprovada automaticamente ou manualmente e está pronta a ser utilizada. |
| Rejeitar | Rejeitado | A ligação foi rejeitada pelo proprietário de recursos de ligação privada. |
| Remover | Desligado | A ligação foi removida pelo proprietário do recurso de ligação privada, o ponto final privado torna-se informativo e deve ser eliminado para limpeza. |
 
###  <a name="how-to-manage-a-private-endpoint-connection"></a>Como gerir uma ligação privada de ponto final
As seguintes secções mostram-lhe como aprovar ou rejeitar uma ligação de ponto final privado. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Na barra de pesquisa, digite **tópicos de Grade de Eventos** ou **domínios de Grade de Eventos**.
1. Selecione o **tópico** ou **domínio** que pretende gerir.
1. Selecione o separador **Rede**.
1. Se houver alguma ligação pendente, verá uma ligação listada com **pendente** no estado de provisionamento. 

### <a name="to-approve-a-private-endpoint"></a>Para aprovar um ponto final privado
Pode aprovar um ponto final privado que está no estado pendente. Para aprovar, siga estes passos: 

> [!NOTE]
> Os passos mostrados nesta secção são principalmente para tópicos. Pode utilizar passos semelhantes para aprovar pontos finais privados para **domínios**. 

1. Selecione o **ponto final privado** que deseja aprovar e selecione **Aprovar** na barra de ferramentas.

    ![Ponto final privado - estado pendente](./media/configure-private-endpoints/pending.png)
1. Na caixa de diálogo de **ligação Aprova,** introduza um comentário (opcional) e selecione **Sim**. 

    ![Ponto final privado - aprovar](./media/configure-private-endpoints/approve.png)
1. Confirme que vê o estado do ponto final como **aprovado**. 

    ![Ponto final privado - estado aprovado](./media/configure-private-endpoints/approved-status.png)

### <a name="to-reject-a-private-endpoint"></a>Rejeitar um ponto final privado
Pode rejeitar um ponto final privado que esteja no estado pendente ou aprovado. Para rejeitar, siga estes passos: 

> [!NOTE]
> Os passos mostrados nesta secção são para tópicos. Pode utilizar passos semelhantes para rejeitar pontos finais privados para **domínios**. 

1. Selecione o **ponto final privado** que pretende rejeitar e selecione **Rejeitar** na barra de ferramentas.

    ![Screenshot que mostra as ligações "Networking - Private endpoint" com "Rejeitar" selecionadas.](./media/configure-private-endpoints/reject-button.png)
1. Na caixa de diálogo de **ligação Rejeitar,** introduza um comentário (opcional) e selecione **Sim**. 

    ![Ponto final privado - rejeitar](./media/configure-private-endpoints/reject.png)
1. Confirme que vê o estado do ponto final como **rejeitado**. 

    ![Ponto final privado - estado rejeitado](./media/configure-private-endpoints/rejected-status.png)

    > [!NOTE]
    > Não pode aprovar um ponto final privado no portal Azure uma vez que é rejeitado. 


## <a name="use-azure-cli"></a>Utilizar a CLI do Azure
Para criar um ponto final privado, utilize o método [de criação de ponto final privado da rede AZ,](/cli/azure/network/private-endpoint?#az_network_private_endpoint_create) como mostra o seguinte exemplo:

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

Para descrições dos parâmetros utilizados no exemplo, consulte a documentação para [a criação de pontos de terminação privadas da rede AZ](/cli/azure/network/private-endpoint?#az_network_private_endpoint_create). Alguns pontos a notar neste exemplo são: 

- Para `private-connection-resource-id` , especificar o ID de recurso do **tópico** ou **domínio**. O exemplo anterior utiliza o tipo: tópico.
- para `group-ids` especificar `topic` ou . `domain` . No exemplo anterior, `topic` é usado. 

Para eliminar um ponto final privado, utilize o método [de eliminação do ponto de terminação privado da rede AZ,](/cli/azure/network/private-endpoint?#az_network_private_endpoint_delete) tal como mostrado no seguinte exemplo:

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> Os passos mostrados nesta secção são para tópicos. Pode utilizar passos semelhantes para criar pontos finais privados para **domínios**. 



### <a name="prerequisites"></a>Pré-requisitos
Atualize a extensão da Grelha de Eventos Azure para o CLI executando o seguinte comando: 

```azurecli-interactive
az extension update -n eventgrid
```

Se a extensão não estiver instalada, executar o seguinte comando para instalá-la: 

```azurecli-interactive
az extension add -n eventgrid
```

### <a name="create-a-private-endpoint"></a>Criar um ponto final privado
Para criar um ponto final privado, utilize o método [de criação de ponto final privado da rede AZ,](/cli/azure/network/private-endpoint?#az_network_private_endpoint_create) como mostra o seguinte exemplo:

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

Para descrições dos parâmetros utilizados no exemplo, consulte a documentação para [a criação de pontos de terminação privadas da rede AZ](/cli/azure/network/private-endpoint?#az_network_private_endpoint_create). Alguns pontos a notar neste exemplo são: 

- Para `private-connection-resource-id` , especificar o ID de recurso do **tópico** ou **domínio**. O exemplo anterior utiliza o tipo: tópico.
- para `group-ids` especificar `topic` ou . `domain` . No exemplo anterior, `topic` é usado. 

Para eliminar um ponto final privado, utilize o método [de eliminação do ponto de terminação privado da rede AZ,](/cli/azure/network/private-endpoint?#az_network_private_endpoint_delete) tal como mostrado no seguinte exemplo:

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> Os passos mostrados nesta secção são para tópicos. Pode utilizar passos semelhantes para criar pontos finais privados para **domínios**. 

#### <a name="sample-script"></a>Script de exemplo
Aqui está um guião de amostra que cria os seguintes recursos Azure:

- Grupo de recursos
- Rede virtual
- Sub-rede na rede virtual
- Tópico da grelha de eventos Azure
- Ponto final privado para o tema

> [!NOTE]
> Os passos mostrados nesta secção são para tópicos. Pode utilizar passos semelhantes para criar pontos finais privados para domínios.

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
    --location $location

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
A amostra seguinte, o snippet CLI mostra-lhe como aprovar uma ligação de ponto final privado. 

```azurecli-interactive
az eventgrid topic private-endpoint-connection approve \
    --resource-group $resourceGroupName \
    --topic-name $topicName \
    --name  $endpointName \
    --description "connection approved"
```


### <a name="reject-a-private-endpoint"></a>Rejeitar um ponto final privado
A amostra seguinte, o snippet CLI mostra-lhe como rejeitar uma ligação de ponto final privado. 

```azurecli-interactive
az eventgrid topic private-endpoint-connection reject \
    --resource-group $resourceGroupName \
    --topic-name $topicName \
    --name $endpointName \
    --description "Connection rejected"
```

### <a name="disable-public-network-access"></a>Desativar o acesso à rede pública
Por predefinição, o acesso à rede pública está ativado para um tópico ou domínio da Grade de Eventos. Para permitir o acesso apenas através de pontos finais privados, desative o acesso à rede pública executando o seguinte comando:  

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access disabled
```


## <a name="use-powershell"></a>Utilizar o PowerShell
Esta secção mostra-lhe como criar um ponto final privado para um tópico ou domínio usando o PowerShell. 

### <a name="prerequisite"></a>Pré-requisito
Siga as instruções de [Como: Use o portal para criar uma aplicação AD Azure e um diretor de serviço que possa aceder](../active-directory/develop/howto-create-service-principal-portal.md) a recursos para criar uma aplicação de Diretório Ativo Azure e anotar os valores para o **ID do Diretório (inquilino),** **ID de aplicação (cliente)** e **aplicação (cliente) secreto.** 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Prepare token e cabeçalhos para chamadas de API REST 
Executar os seguintes comandos pré-requisitos para obter um token de autenticação para usar com chamadas e autorização rest API e outras informações de cabeçalho. 

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

### <a name="create-a-subnet-with-endpoint-network-policies-disabled"></a>Criar uma sub-rede com políticas de rede de pontos finais desativadas

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

### <a name="create-an-event-grid-topic-with-a-private-endpoint"></a>Crie um tópico de grelha de eventos com um ponto final privado

> [!NOTE]
> Os passos mostrados nesta secção são para tópicos. Pode utilizar passos semelhantes para criar pontos finais privados para **domínios**. 


```azurepowershell-interactive
$body = @{"location"="<LOCATION>"; "properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json

# create topic
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $body

# verify that the topic was created
$topic=Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01"   `
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
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections?api-version=2020-06-01"   `
    -Headers $Headers   `
    | ConvertTo-Json -Depth 5

```

Quando verificar se o ponto final foi criado, deverá ver o resultado semelhante ao seguinte:

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

### <a name="approve-a-private-endpoint-connection"></a>Aprovar uma ligação privada de ponto final
A amostra seguinte, o snippet PowerShell mostra-lhe como aprovar um ponto final privado. 

> [!NOTE]
> Os passos mostrados nesta secção são para tópicos. Pode utilizar passos semelhantes para aprovar pontos finais privados para **domínios**. 

```azurepowershell-interactive
$approvedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="approved";"description"="connection approved";"actionsRequired"="none"}}} | ConvertTo-Json

# approve endpoint connection
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AzuRE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $approvedBody

# confirm that the endpoint connection was approved
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers

```

### <a name="reject-a-private-endpoint-connection"></a>Rejeitar uma ligação privada de ponto final
O exemplo a seguir mostra-lhe como rejeitar um ponto final privado utilizando o PowerShell. Pode obter o GUIADO para o ponto final privado a partir do resultado do comando GET anterior. 

> [!NOTE]
> Os passos mostrados nesta secção são para tópicos. Pode utilizar passos semelhantes para rejeitar pontos finais privados para **domínios**. 


```azurepowershell-interactive
$rejectedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="rejected";"description"="connection rejected";"actionsRequired"="none"}}} | ConvertTo-Json

# reject private endpoint
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $rejectedBody

# confirm that endpoint was rejected
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01" ` 
    -Headers $Headers
```

Pode aprovar a ligação mesmo depois de rejeitada através da API. Se usar o portal Azure, não pode aprovar um ponto final que foi rejeitado. 

## <a name="next-steps"></a>Passos seguintes
* Para saber como configurar as definições de firewall IP, consulte [a firewall IP configurar para tópicos ou domínios da Grelha de Eventos Azure](configure-firewall.md).
* Para resolver problemas de conectividade da rede, consulte [problemas de conectividade da rede troubleshoot](troubleshoot-network-connectivity.md)
