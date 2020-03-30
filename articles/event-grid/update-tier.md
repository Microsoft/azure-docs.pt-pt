---
title: Atualizar o nível de preços de um tópico ou domínio da Grelha de Eventos Azure
description: Este artigo descreve como atualizar o nível de preços de um tópico ou domínio azure Event Grid (básico a premium, premium ao básico) usando o portal Azure, Azure CLI e Azure PowerShell.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: 1e92a9f0b9ba007b289c634995b04f4f1575310a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79300144"
---
# <a name="update-pricing-tier"></a>Nível de preços de atualização 
Este artigo mostra-lhe como atualizar o nível de preços de um tópico ou domínio da Rede de Eventos Azure utilizando o portal Azure, o Azure CLI e o Azure PowerShell. 

## <a name="use-azure-portal"></a>Utilizar o portal do Azure
Esta secção mostra como alterar o nível de preços de um tópico ou domínio no portal Azure. 

### <a name="overview-page"></a>Página de visão geral
Pode alterar o nível de preços de um tópico ou domínio na página **'Overview'.** O exemplo que se segue mostra como atualizar um tópico do nível básico para o nível premium. Os passos para desvalorizar do nível premium para o nível básico são semelhantes.

1. No [portal Azure,](https://portal.azure.com)navegue para o tópico ou página de domínio. 
2. Na página **'Visão Geral',** selecione o nível de preços atual (no exemplo seguinte, é **básico**.)
    
    ![Selecione o atual nível de preços](./media/update-tier/select-tier.png)
3. Na página do nível de **preços,** altere o nível e selecione **OK**. 

    ![Atualizar o nível de preços](./media/update-tier/change-tier.png)
4. Verifique o estado da operação na página **Notificações.**

    ![Estado da atualização](./media/update-tier/status.png)    
5. Confirme que vê o nível atualizado na página **'Overview'.** 

    ![Estado da atualização](./media/update-tier/changed-tier.png)

### <a name="networking-page"></a>Página de rede
Pode **atualizar** do nível básico para o nível premium na página **de Networking.** No entanto, não se pode desvalorizar do nível premium para o nível básico nesta página. 

1. No [portal Azure,](https://portal.azure.com)navegue para o tópico ou página de domínio. 
2. Na página **de Networking,** altere para o separador de **ligações de ponto final privado (pré-visualização).** 
3. Se o nível de preços atual for **básico,** consulte a seguinte mensagem. Selecione-o. 

    ![Nível de atualização na página de ligações de ponto final privado](./media/update-tier/private-endpoint-connections-page.png)
4. Na **página 'Atualizar para o nível de preços premium',** selecione **Sim**. 
    
    ![Confirmar atualização](./media/update-tier/message-private-endpoint-connection.png)
5. Verifique o estado da operação na página **Notificações.**

    ![Estado da atualização](./media/update-tier/status.png)



## <a name="use-azure-cli"></a>Utilizar a CLI do Azure
Esta secção mostra-lhe como utilizar comandos Azure CLI para alterar o nível de preços de um tópico ou domínio. 

### <a name="upgrade-a-topic-from-basic-to-premium"></a>Atualizar um tópico do básico para o premium

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""premium\""}}"
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>Downgrade um tópico de premium para básico

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""basic\""}}"
```

### <a name="upgrade-a-domain-from-basic-to-premium"></a>Atualizar um domínio de básico para premium

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""premium\""}}"
```

### <a name="downgrade-a-domain-from-premium-to-basic"></a>Downgrade um domínio de prémio para básico

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""basic\""}}"
```



## <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell
Esta secção mostra-lhe como usar comandos PowerShell para alterar o nível de preços de um tópico ou domínio. 

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

### <a name="upgrade-a-topic-from-basic-to-premium"></a>Atualizar um tópico do básico para o premium

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>Downgrade um tópico de premium para básico

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="upgrade-a-domain-from-basic-to-premium"></a>Atualizar um domínio de básico para premium

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-domain-from-premium-to-basic"></a>Downgrade um domínio de prémio para básico

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

## <a name="next-steps"></a>Passos seguintes
Para tópicos e domínios de nível premium, pode configurar pontos finais privados para restringir o acesso a partir de redes virtuais selecionadas. Para obter instruções passo a passo, consulte [os pontos finais privados da Configuração](configure-private-endpoints.md).
