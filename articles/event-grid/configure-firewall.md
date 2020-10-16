---
title: Configure firewall IP para tópicos ou domínios da Grelha de Eventos Azure
description: Este artigo descreve como configurar as definições de firewall para tópicos ou domínios da Grade de Eventos.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: fd190a13a177b6b6d0f6b0dbcaa35d63dccd93c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324166"
---
# <a name="configure-ip-firewall-for-azure-event-grid-topics-or-domains"></a>Configure firewall IP para tópicos ou domínios da Grelha de Eventos Azure 
Por predefinição, o tópico e o domínio estão acessíveis a partir da Internet desde que o pedido venha com autenticação e autorização válidas. Com a firewall IP, pode restringi-lo ainda mais a um conjunto de endereços IPv4 ou intervalos de endereços IPv4 na notação [CIDR (Classless Inter-Domain Encaminhamento).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) Os editores originários de qualquer outro endereço IP serão rejeitados e receberão uma resposta 403 (Proibida). Para obter mais informações sobre funcionalidades de segurança de rede suportadas pela Grade de Eventos, consulte [a segurança da rede de eventos](network-security.md).

Este artigo descreve como configurar as definições de firewall IP para tópicos ou domínios da Grelha de Eventos Azure.

## <a name="use-azure-portal"></a>Utilizar o portal do Azure
Esta secção mostra-lhe como usar o portal Azure para criar regras de firewall IP de entrada. Os passos mostrados nesta secção são para tópicos. Pode utilizar passos semelhantes para criar regras ip de entrada para **domínios**. 

1. No [portal Azure,](https://portal.azure.com)navegue para o tópico ou domínio da grelha de eventos e mude para o separador **'Rede'.**
2. Selecione **redes públicas** para permitir que toda a rede, incluindo a internet, aceda ao recurso. 

    Pode restringir o tráfego utilizando regras de firewall baseadas em IP. Especifique um único endereço IPv4 ou uma gama de endereços IP na notação de encaminhamento inter-domínio (CIDR) sem classe. 

    ![Screenshot que mostra a página "Acesso à rede pública" com "redes públicas" selecionadas.](./media/configure-firewall/public-networks-page.png)
3. Selecione **pontos finais privados apenas** para permitir que apenas ligações privadas de ponto final acedam a este recurso. Utilize o **separador de ligações de ponto final privado** nesta página para gerir as ligações. 

    ![Página de redes públicas](./media/configure-firewall/private-endpoints-page.png)
4. Selecione **Guardar** na barra de ferramentas. 



## <a name="use-azure-cli"></a>Utilizar a CLI do Azure
Esta secção mostra-lhe como usar comandos Azure CLI para criar tópicos com regras IP de entrada. Os passos mostrados nesta secção são para tópicos. Pode utilizar passos semelhantes para criar regras ip de entrada para **domínios**. 


### <a name="prerequisites"></a>Pré-requisitos
Atualize a extensão da Grelha de Eventos Azure para o CLI executando o seguinte comando: 

```azurecli-interactive
az extension update -n eventgrid
```

Se a extensão não estiver instalada, executar o seguinte comando para instalá-la: 

```azurecli-interactive
az extension add -n eventgrid
```

### <a name="enable-or-disable-public-network-access"></a>Permitir ou desativar o acesso à rede pública
Por padrão, o acesso à rede pública está ativado para tópicos e domínios. Também pode ativá-lo explicitamente ou desativá-lo. Pode restringir o tráfego configurando as regras de firewall IP de entrada. 

#### <a name="enable-public-network-access-while-creating-a-topic"></a>Permitir o acesso à rede pública ao mesmo tempo que cria um tópico

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access enabled
```


#### <a name="disable-public-network-access-while-creating-a-topic"></a>Desativar o acesso à rede pública ao criar um tópico

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access disabled
```

> [!NOTE]
> Quando o acesso à rede pública é desativado para um tópico ou domínio, o tráfego através da internet pública não é permitido. Apenas ligações privadas de ponto final serão autorizadas a aceder a estes recursos. 


#### <a name="enable-public-network-access-for-an-existing-topic"></a>Permitir o acesso à rede pública para um tópico existente

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled 
```

#### <a name="disable-public-network-access-for-an-existing-topic"></a>Desativar o acesso à rede pública para um tópico existente 

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access disabled
```

### <a name="create-a-topic-with-single-inbound-ip-rule"></a>Crie um tópico com regra ip de entrada única
O seguinte comando CLI da amostra cria um tópico de grelha de evento com regras IP de entrada. 

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR or CIDR MASK> allow 
```

### <a name="create-a-topic-with-multiple-inbound-ip-rules"></a>Crie um tópico com várias regras ip de entrada

O seguinte comando CLI da amostra cria um tópico de grelha de evento duas regras IP de entrada em um passo: 

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR 1 or CIDR MASK 1> allow \
    --inbound-ip-rules <IP ADDR 2 or CIDR MASK 2> allow
```

### <a name="update-an-existing-topic-to-add-inbound-ip-rules"></a>Atualizar um tópico existente para adicionar regras ip de entrada
Este exemplo cria primeiro um tópico de grelha de eventos e, em seguida, adiciona regras IP de entrada para o tópico em um comando separado. Também atualiza as regras ip de entrada que foram definidas no segundo comando. 

```azurecli-interactive

# create the event grid topic first
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location

# add inbound IP rules to an existing topic
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR or CIDR MASK> allow

# later, update topic with additional ip rules
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR 1 or CIDR MASK 1> allow \
    --inbound-ip-rules <IP ADDR 2 or CIDR MASK 2> allow
```

### <a name="remove-an-inbound-ip-rule"></a>Remover uma regra IP de entrada
O comando a seguir remove a segunda regra que criou no passo anterior especificando apenas a primeira regra enquanto atualiza a definição. 

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR 1 or CIDR MASK 1> allow
```


## <a name="use-powershell"></a>Utilizar o PowerShell
Esta secção mostra-lhe como usar comandos Azure PowerShell para criar tópicos Azure Event Grid com regras de firewall IP de entrada. Os passos mostrados nesta secção são para tópicos. Pode utilizar passos semelhantes para criar regras ip de entrada para **domínios**. 

### <a name="prerequisites"></a>Pré-requisitos
Siga as instruções de [Como: Use o portal para criar uma aplicação AD AD E um chefe de serviço que possa aceder](../active-directory/develop/howto-create-service-principal-portal.md) aos recursos para criar uma aplicação de Diretório Ativo Azure e anotar os seguintes valores:

- ID do Diretório (inquilino)
- ID de aplicação (Cliente)
- Pedido (cliente) secreto

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Prepare token e cabeçalhos para chamadas de API REST 
Executar os seguintes comandos pré-requisitos para obter um token de autenticação para usar com chamadas REST API, e autorização e outras informações de cabeçalho. 

```azurepowershell-interactive
# replace <CLIENT ID> and <CLIENT SECRET>
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get the authentication token. Replace <TENANT ID>
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token `
    -Body $body `
    -ContentType 'application/x-www-form-urlencoded'

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
$Headers.Add("Content-Type","application/json")
```

### <a name="enable-public-network-access-for-an-existing-topic"></a>Permitir o acesso à rede pública para um tópico existente
Por padrão, o acesso à rede pública está ativado para tópicos e domínios. Pode restringir o tráfego configurando as regras de firewall IP de entrada. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="enabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="disable-public-network-access-for-an-existing-topic"></a>Desativar o acesso à rede pública para um tópico existente
Quando o acesso à rede pública é desativado para um tópico ou domínio, o tráfego através da internet pública não é permitido. Apenas ligações privadas de ponto final serão autorizadas a aceder a estes recursos. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="create-an-event-grid-topic-with-inbound-rules-in-one-step"></a>Crie um tópico de grelha de eventos com regras de entrada num só passo

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that inbound IP rules are included. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDR or CIDR MASK>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# create the event grid topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5
```


### <a name="create-event-grid-topic-first-and-then-add-inbound-ip-rules"></a>Crie primeiro o tópico da grelha de eventos e, em seguida, adicione regras ip de entrada

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that no inbound IP rules are specified. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled";}} | ConvertTo-Json -Depth 5

# create the Event Grid topic
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body`

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

# prepare the body for REST PUT method. Notice that it includes inbound IP rules now. This feature is available in both basic and premium tiers.
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDR or CIDR MASK>";"action"="allow"}, @{"ipmask"="<IP ADDR or CIDR MASK>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# update the topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body

# verify that the topic was updated
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AzURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

```

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre a monitorização das entregas de eventos, consulte [a entrega de mensagens monitor a Grelha de Eventos](monitor-event-delivery.md).
* Para obter mais informações sobre a chave de autenticação, consulte [a segurança e a autenticação da Grade de Eventos.](security-authentication.md)
* Para obter mais informações sobre a criação de uma subscrição da Azure Event Grid, consulte [o esquema de subscrição da Event Grid](subscription-creation-schema.md).
* Para resolver problemas de conectividade da rede, consulte [problemas de conectividade da rede troubleshoot](troubleshoot-network-connectivity.md)
