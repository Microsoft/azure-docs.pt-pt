---
title: Configure firewall IP para tópicos ou domínios da Grelha de Eventos Azure (Pré-visualização)
description: Este artigo descreve como configurar as definições de firewall para tópicos ou domínios da Grelha de Eventos.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: spelluru
ms.openlocfilehash: 4aa86b3619897c310473f12e1c28101185ebf3ab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82100996"
---
# <a name="configure-ip-firewall-for-azure-event-grid-topics-or-domains-preview"></a>Configure firewall IP para tópicos ou domínios da Grelha de Eventos Azure (Pré-visualização)
Por predefinição, o tópico e o domínio são acessíveis a partir da internet desde que o pedido venha com autenticação e autorização válidas. Com firewall IP, pode restringi-lo ainda mais a apenas um conjunto de endereços IPv4 ou intervalos de endereços IPv4 na notação [CIDR (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) Os editores originários de qualquer outro endereço IP serão rejeitados e receberão uma resposta 403 (Proibida). Para obter mais informações sobre as funcionalidades de segurança da rede suportadas pela Rede de Eventos, consulte a segurança da rede para a Rede de [Eventos](network-security.md).

Este artigo descreve como configurar as definições de firewall IP para tópicos ou domínios da Grelha de Eventos do Azure.

## <a name="use-azure-portal"></a>Utilizar o portal do Azure
Esta secção mostra-lhe como usar o portal Azure para criar regras de firewall IP de entrada. Os passos mostrados nesta secção são para tópicos. Pode utilizar passos semelhantes para criar regras ip de entrada para **domínios**. 

1. No [portal Azure,](https://portal.azure.com)navegue para o tópico ou domínio da grelha de eventos e mude para o separador **Networking.**
2. Selecione **redes públicas** para permitir que toda a rede, incluindo a internet, aceda ao recurso. 

    Pode restringir o tráfego utilizando regras de firewall baseadas em IP. Especifique um único endereço IPv4 ou uma gama de endereços IP na notação de encaminhamento de domínios sem classe (CIDR). 

    ![Página de redes públicas](./media/configure-firewall/public-networks-page.png)
3. Selecione **pontos finais privados apenas** para permitir que apenas ligações de ponto final privados acedam a este recurso. Utilize o separador de **ligações de ponto final Privado** nesta página para gerir as ligações. 

    ![Página de redes públicas](./media/configure-firewall/private-endpoints-page.png)
4. Selecione **Guardar** na barra de ferramentas. 



## <a name="use-azure-cli"></a>Utilizar a CLI do Azure
Esta secção mostra-lhe como usar comandos Azure CLI para criar tópicos com regras ip de entrada. Os passos mostrados nesta secção são para tópicos. Pode utilizar passos semelhantes para criar regras ip de entrada para **domínios**. 


### <a name="prerequisites"></a>Pré-requisitos
Atualize a extensão da Grelha de Eventos Azure para o CLI executando o seguinte comando: 

```azurecli-interactive
az extension update -n eventgrid
```

Se a extensão não estiver instalada, execute o seguinte comando para instalá-la: 

```azurecli-interactive
az extension add -n eventgrid
```

### <a name="enable-or-disable-public-network-access"></a>Ativar ou desativar o acesso à rede pública
Por padrão, o acesso à rede pública está habilitado para tópicos e domínios. Também pode ativar explicitamente ou desativá-lo. Pode restringir o tráfego configurando as regras de firewall IP de entrada. 

#### <a name="enable-public-network-access-while-creating-a-topic"></a>Permitir o acesso à rede pública ao mesmo tempo que cria um tópico

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access enabled
```


#### <a name="disable-public-network-access-while-creating-a-topic"></a>Desativar o acesso à rede pública ao mesmo tempo que cria um tópico

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access disabled
```

> [!NOTE]
> Quando o acesso à rede pública é desativado para um tópico ou domínio, o tráfego através da internet pública não é permitido. Apenas as ligações privadas de ponto final serão autorizadas a aceder a estes recursos. 


#### <a name="enable-public-network-access-for-an-existing-topic"></a>Permitir o acesso à rede pública para um tópico existente

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled 
```

#### <a name="disable-public-network-access-for-an-existing-topic"></a>Desativar o acesso da rede pública para um tópico existente 

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access disabled
```

### <a name="create-a-topic-with-single-inbound-ip-rule"></a>Criar um tópico com uma única regra ip de entrada
O comando CLI da amostra seguinte cria um tópico de grelha de evento com regras ip de entrada. 

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR or CIDR MASK> allow 
```

### <a name="create-a-topic-with-multiple-inbound-ip-rules"></a>Criar um tópico com múltiplas regras ip de entrada

A amostra seguinte comando CLI cria um tópico de grelha de evento duas regras ip de entrada em um passo: 

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
Este exemplo cria primeiro um tópico de grelha de eventos e, em seguida, adiciona regras ip de entrada para o tópico em um comando separado. Também atualiza as regras ip de entrada que foram definidas no segundo comando. 

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

### <a name="remove-an-inbound-ip-rule"></a>Remover uma regra ip de entrada
O comando seguinte remove a segunda regra que criou no passo anterior, especificando apenas a primeira regra ao atualizar a definição. 

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR 1 or CIDR MASK 1> allow
```


## <a name="use-powershell"></a>Utilizar o PowerShell
Esta secção mostra-lhe como utilizar comandos Azure PowerShell para criar tópicos da Rede de Eventos Azure com regras de firewall IP de entrada. Os passos mostrados nesta secção são para tópicos. Pode utilizar passos semelhantes para criar regras ip de entrada para **domínios**. 

### <a name="prerequisites"></a>Pré-requisitos
Siga as instruções de [Como: Utilize o portal para criar uma aplicação e um diretor](../active-directory/develop/howto-create-service-principal-portal.md) de serviço da Azure AD que possam aceder a recursos para criar uma aplicação azure Ative Directory e observar os seguintes valores:

- ID do Diretório (inquilino)
- Id de aplicação (Cliente)
- Pedido (cliente) secreto

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Prepare token e cabeçalhos para chamadas REST API 
Executar os seguintes comandos pré-requisitos para obter um símbolo de autenticação para usar com chamadas Rest API, e autorização e outras informações de cabeçalho. 

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
Por padrão, o acesso à rede pública está habilitado para tópicos e domínios. Pode restringir o tráfego configurando as regras de firewall IP de entrada. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="enabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="disable-public-network-access-for-an-existing-topic"></a>Desativar o acesso da rede pública para um tópico existente
Quando o acesso à rede pública é desativado para um tópico ou domínio, o tráfego através da internet pública não é permitido. Apenas as ligações privadas de ponto final serão autorizadas a aceder a estes recursos. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="create-an-event-grid-topic-with-inbound-rules-in-one-step"></a>Criar um tópico de grelha de evento com regras de entrada em um passo

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that inbound IP rules are included. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDR or CIDR MASK>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# create the event grid topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5
```


### <a name="create-event-grid-topic-first-and-then-add-inbound-ip-rules"></a>Crie primeiro o tópico da grelha de eventos e, em seguida, adicione as regras ip de entrada

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that no inbound IP rules are specified. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled";}} | ConvertTo-Json -Depth 5

# create the Event Grid topic
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body`

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

# prepare the body for REST PUT method. Notice that it includes inbound IP rules now. This feature available in both basic and premium tiers.
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDR or CIDR MASK>";"action"="allow"}, @{"ipmask"="<IP ADDR or CIDR MASK>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# update the topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body

# verify that the topic was updated
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AzURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

```

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre a monitorização das entregas de eventos, consulte a entrega de [mensagens Monitor Event Grid](monitor-event-delivery.md).
* Para mais informações sobre a chave de autenticação, consulte [a segurança e a autenticação da Rede de Eventos.](security-authentication.md)
* Para mais informações sobre a criação de uma subscrição da Rede de Eventos Do Evento, consulte o esquema de subscrição da [Rede de Eventos](subscription-creation-schema.md).
