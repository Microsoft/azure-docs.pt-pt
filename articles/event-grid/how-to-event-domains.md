---
title: Gerir grandes conjuntos de tópicos do Azure Event Grid com domínios de eventos
description: Mostra como gerenciar grandes conjuntos de tópicos do Azure Event Grid e publicar eventos aos mesmos com domínios de evento.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 01/17/2019
ms.openlocfilehash: c49044d8bd96efb7e86cf54509c32033900be305
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58180913"
---
# <a name="manage-topics-and-publish-events-using-event-domains"></a>Tópicos de gerir e publicar eventos através de domínios de eventos

Este artigo mostra como:

* Criar um domínio do Event Grid
* Subscrever tópicos do event grid
* Listar chaves
* Publicar eventos para um domínio

Para saber mais sobre os domínios de eventos, veja [compreender os domínios de eventos para o gerenciamento de tópicos do Event Grid](event-domains.md).

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Instale a funcionalidade de pré-visualização

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-event-domain"></a>Criar um domínio de eventos

Para gerir grandes conjuntos de tópicos, crie um domínio de evento.

Para a CLI do Azure, utilize:

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid domain create \
  -g <my-resource-group> \
  --name <my-domain-name> \
  -l <location>
```

Para o PowerShell, utilize:

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain-name> `
  -Location <location>
```

A criação com êxito devolve os valores seguintes:

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

Tenha em atenção a `endpoint` e `id` à medida que são necessários para gerir o domínio e publicar eventos.

## <a name="manage-access-to-topics"></a>Gerir o acesso aos tópicos

Gerir o acesso aos tópicos é feita por meio [atribuição de função](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli). Atribuição de função usa o controlo de acesso baseado em funções para limitar as operações em recursos do Azure para os utilizadores autorizados num determinado âmbito.

Grelha de eventos tem duas funções incorporadas, que pode utilizar para atribuir utilizadores particulares acesso sobre vários tópicos dentro de um domínio. Estas funções são `EventGrid EventSubscription Contributor (Preview)`, que permite a criação e eliminação das subscrições, e `EventGrid EventSubscription Reader (Preview)`, que permite que apenas para a lista de subscrições de eventos.

Os seguintes limites de comando da CLI do Azure `alice@contoso.com` para criar e eliminar subscrições de eventos apenas no tópico `demotopic1`:

```azurecli-interactive
az role assignment create \
  --assignee alice@contoso.com \
  --role "EventGrid EventSubscription Contributor (Preview)" \
  --scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```

Os seguintes limites de comando do PowerShell `alice@contoso.com` para criar e eliminar subscrições de eventos apenas no tópico `demotopic1`:

```azurepowershell-interactive
New-AzureRmRoleAssignment `
  -SignInName alice@contoso.com `
  -RoleDefinitionName "EventGrid EventSubscription Contributor (Preview)" `
  -Scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```

Para obter mais informações sobre a gestão de acesso para operações do Event Grid, veja [Event Grid segurança e autenticação](./security-authentication.md).

## <a name="create-topics-and-subscriptions"></a>Criar tópicos e subscrições

O serviço do Event Grid cria e gere o tópico correspondente num domínio com base na chamada para criar uma subscrição de evento para um tópico de domínio automaticamente. Não existe passo separado para criar um tópico num domínio. Da mesma forma, quando a última subscrição de evento para um tópico é excluída, o tópico é eliminado também.

Subscrever um tópico num domínio é igual a subscrição a qualquer outro recurso do Azure. Para o ID de recurso de origem, especifique o ID de domínio do evento devolvido ao criar o domínio anteriormente. Para especificar o tópico que pretende subscrever, adicione `/topics/<my-topic>` ao final do ID de recurso de origem. Para criar uma subscrição de evento de âmbito de domínio que recebe a todos os eventos no domínio, especifique o ID de evento de domínio sem especificar qualquer tópico.

Normalmente, é concedido ao utilizador aceder na que secção anterior cria a subscrição. Para simplificar este artigo, crie a subscrição. 

Para a CLI do Azure, utilize:

```azurecli-interactive
az eventgrid event-subscription create \
  --name <event-subscription> \
  --source-resource-id "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" \
  --endpoint https://contoso.azurewebsites.net/api/updates
```

Para o PowerShell, utilize:

```azurepowershell-interactive
New-AzureRmEventGridSubscription `
  -ResourceId "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" `
  -EventSubscriptionName <event-subscription> `
  -Endpoint https://contoso.azurewebsites.net/api/updates
```

Se precisar de um ponto final de teste para subscrever os eventos, sempre pode implementar um [aplicação web pré-criados](https://github.com/Azure-Samples/azure-event-grid-viewer) que apresenta os eventos de entrada. Pode enviar seus eventos ao seu Web site de teste em `https://<your-site-name>.azurewebsites.net/api/updates`.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

As permissões que estão definidas para um tópico são armazenadas no Azure Active Directory e tem de ser eliminadas explicitamente. A eliminar uma subscrição de evento não revogar um acesso de utilizadores para criar subscrições de eventos, se tiver acesso de escrita sobre um tópico.


## <a name="publish-events-to-an-event-grid-domain"></a>Publicar eventos para um domínio de grelha de eventos

A publicação de eventos a um domínio é o mesmo que [publicar um tópico personalizado](./post-to-custom-topic.md). No entanto, em vez de publicação para o tópico personalizado, publicar todos os eventos para o ponto final de domínio. Os dados de eventos do JSON, vai especificar o tópico que pretende que os eventos para ir para. A matriz de eventos a seguir iria resultar em eventos com `"id": "1111"` para o tópico `demotopic1` ao evento com `"id": "2222"` seriam enviados para o tópico `demotopic2`:

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

Para obter o ponto de final de domínio com a CLI do Azure, utilize

```azurecli-interactive
az eventgrid domain show \
  -g <my-resource-group> \
  -n <my-domain>
```

Para obter as chaves para um domínio, utilize:

```azurecli-interactive
az eventgrid domain key list \
  -g <my-resource-group> \
  -n <my-domain>
```

Para obter o ponto de extremidade do domínio com o PowerShell, utilize

```azurepowershell-interactive
Get-AzureRmEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```

Para obter as chaves para um domínio, utilize:

```azurepowershell-interactive
Get-AzureRmEventGridDomainKey `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```

E, em seguida, utilizar o seu método preferido de fazer com que um HTTP POST para publicar seus eventos ao seu domínio do Event Grid.

## <a name="next-steps"></a>Passos Seguintes

* Para obter mais informações sobre conceitos de alto nível em domínios de eventos e por que eles são úteis, consulte a [descrição geral conceptual dos domínios de evento](event-domains.md).
