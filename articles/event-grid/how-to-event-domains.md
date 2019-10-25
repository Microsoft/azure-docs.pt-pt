---
title: Publicar eventos com domínios de evento com a grade de eventos do Azure
description: Mostra como gerenciar grandes conjuntos de tópicos na grade de eventos do Azure e publicar eventos para eles usando domínios de evento.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 10/22/2019
ms.openlocfilehash: 1d07227249806b7d54523af66817a170c19354ee
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72786543"
---
# <a name="manage-topics-and-publish-events-using-event-domains"></a>Gerenciar tópicos e publicar eventos usando domínios de evento

Este artigo mostra como:

* Criar um domínio de grade de eventos
* Tópicos de assinatura para a grade de eventos
* Listar chaves
* Publicar eventos em um domínio

Para saber mais sobre domínios de evento, consulte [entender domínios de evento para gerenciar tópicos de grade de eventos](event-domains.md).

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Instalar o recurso de visualização

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-event-domain"></a>Criar um domínio de evento

Para gerenciar grandes conjuntos de tópicos, crie um domínio de evento.

# <a name="azure-clitabazurecli"></a>[CLI do Azure](#tab/azurecli)

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid domain create \
  -g <my-resource-group> \
  --name <my-domain-name> \
  -l <location>
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain-name> `
  -Location <location>
```
---

A criação bem-sucedida retorna os seguintes valores:

```json
{
  "endpoint": "https://<my-domain-name>.westus2-1.eventgrid.azure.net/api/events",
  "id": "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>",
  "inputSchema": "EventGridSchema",
  "inputSchemaMapping": null,
  "location": "westus2",
  "name": "<my-domain-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "<my-resource-group>",
  "tags": null,
  "type": "Microsoft.EventGrid/domains"
}
```

Observe o `endpoint` e `id` conforme eles são necessários para gerenciar o domínio e publicar eventos.

## <a name="manage-access-to-topics"></a>Gerenciar o acesso a tópicos

O gerenciamento de acesso a tópicos é feito por meio da [atribuição de função](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli). A atribuição de função usa o controle de acesso baseado em função para limitar as operações nos recursos do Azure a usuários autorizados em um determinado escopo.

A grade de eventos tem duas funções internas, que podem ser usadas para atribuir acesso a usuários específicos em vários tópicos em um domínio. Essas funções são `EventGrid EventSubscription Contributor (Preview)`, que permite a criação e exclusão de assinaturas e `EventGrid EventSubscription Reader (Preview)`, que só permite a listagem de assinaturas de evento.

# <a name="azure-clitabazurecli"></a>[CLI do Azure](#tab/azurecli)
O CLI do Azure de comando a seguir limita `alice@contoso.com` para criar e excluir assinaturas de evento somente no tópico `demotopic1`:

```azurecli-interactive
az role assignment create \
  --assignee alice@contoso.com \
  --role "EventGrid EventSubscription Contributor (Preview)" \
  --scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
O comando do PowerShell a seguir limita `alice@contoso.com` para criar e excluir assinaturas de evento somente no tópico `demotopic1`:

```azurepowershell-interactive
New-AzureRmRoleAssignment `
  -SignInName alice@contoso.com `
  -RoleDefinitionName "EventGrid EventSubscription Contributor (Preview)" `
  -Scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```
---

Para obter mais informações sobre como gerenciar o acesso para operações de grade de eventos, consulte [segurança e autenticação da grade de eventos](./security-authentication.md).

## <a name="create-topics-and-subscriptions"></a>Criar tópicos e subscrições

O serviço de grade de eventos cria e gerencia automaticamente o tópico correspondente em um domínio com base na chamada para criar uma assinatura de evento para um tópico de domínio. Não há nenhuma etapa separada para criar um tópico em um domínio. Da mesma forma, quando a última assinatura de evento de um tópico é excluída, o tópico também é excluído.

Assinar um tópico em um domínio é o mesmo que assinar qualquer outro recurso do Azure. Para a ID do recurso de origem, especifique a ID de domínio do evento retornada ao criar o domínio anteriormente. Para especificar o tópico que você deseja assinar, adicione `/topics/<my-topic>` ao final da ID do recurso de origem. Para criar uma assinatura de evento de escopo de domínio que receba todos os eventos no domínio, especifique a ID de domínio do evento sem especificar nenhum tópico.

Normalmente, o usuário ao qual você concedeu acesso na seção anterior criaria a assinatura. Para simplificar este artigo, você cria a assinatura. 

# <a name="azure-clitabazurecli"></a>[CLI do Azure](#tab/azurecli)

```azurecli-interactive
az eventgrid event-subscription create \
  --name <event-subscription> \
  --source-resource-id "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" \
  --endpoint https://contoso.azurewebsites.net/api/updates
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```azurepowershell-interactive
New-AzureRmEventGridSubscription `
  -ResourceId "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" `
  -EventSubscriptionName <event-subscription> `
  -Endpoint https://contoso.azurewebsites.net/api/updates
```

---

Se você precisar de um ponto de extremidade de teste para inscrever seus eventos no, sempre será possível implantar um [aplicativo Web pré-criado](https://github.com/Azure-Samples/azure-event-grid-viewer) que exibe os eventos de entrada. Você pode enviar seus eventos para seu site de teste em `https://<your-site-name>.azurewebsites.net/api/updates`.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

As permissões definidas para um tópico são armazenadas em Azure Active Directory e devem ser excluídas explicitamente. A exclusão de uma assinatura de evento não revogará o acesso de um usuário para criar assinaturas de evento se eles tiverem acesso de gravação em um tópico.


## <a name="publish-events-to-an-event-grid-domain"></a>Publicar eventos em um domínio da grade de eventos

Publicar eventos em um domínio é o mesmo que [publicar em um tópico personalizado](./post-to-custom-topic.md). No entanto, em vez de publicar no tópico personalizado, você publica todos os eventos no ponto de extremidade do domínio. Nos dados de evento JSON, você especifica o tópico para o qual deseja que os eventos sejam acessados. A seguinte matriz de eventos resultaria em um evento com `"id": "1111"` para o tópico `demotopic1` enquanto o evento com `"id": "2222"` seria enviado para o tópico `demotopic2`:

```json
[{
  "topic": "demotopic1",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "demotopic2",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

# <a name="azure-clitabazurecli"></a>[CLI do Azure](#tab/azurecli)
Para obter o ponto de extremidade de domínio com CLI do Azure, use

```azurecli-interactive
az eventgrid domain show \
  -g <my-resource-group> \
  -n <my-domain>
```

Para obter as chaves para um domínio, use:

```azurecli-interactive
az eventgrid domain key list \
  -g <my-resource-group> \
  -n <my-domain>
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Para obter o ponto de extremidade de domínio com o PowerShell, use

```azurepowershell-interactive
Get-AzureRmEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```

Para obter as chaves para um domínio, use:

```azurepowershell-interactive
Get-AzureRmEventGridDomainKey `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```
---

Em seguida, use seu método favorito para fazer um HTTP POST para publicar seus eventos em seu domínio da grade de eventos.

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre conceitos de alto nível em domínios de evento e por que eles são úteis, consulte a [visão geral conceitual dos domínios de evento](event-domains.md).
