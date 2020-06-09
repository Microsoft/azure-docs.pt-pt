---
title: Publicar eventos com domínios de eventos com Azure Event Grid
description: Mostra como gerir grandes conjuntos de tópicos em Azure Event Grid e publicar eventos para eles usando domínios de eventos.
services: event-grid
author: femila
ms.service: event-grid
ms.author: femila
ms.topic: conceptual
ms.date: 10/22/2019
ms.openlocfilehash: 10a0b138c702d4e4d09608c42959c68e97790f81
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84560978"
---
# <a name="manage-topics-and-publish-events-using-event-domains"></a>Gerir tópicos e publicar eventos usando domínios de eventos

Este artigo mostra como:

* Criar um domínio de Grade de Eventos
* Subscreva os tópicos da grelha de eventos
* Chaves da lista
* Publicar eventos para um domínio

Para conhecer os domínios do evento, consulte [os domínios do evento para gerir tópicos de Grelha de Eventos.](event-domains.md)

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Instale a função de pré-visualização

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-event-domain"></a>Criar um domínio de evento

Para gerir grandes conjuntos de tópicos, crie um domínio de evento.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azurecli)

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid domain create \
  -g <my-resource-group> \
  --name <my-domain-name> \
  -l <location>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)
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

A criação bem sucedida devolve os seguintes valores:

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

Note o `endpoint` e como eles são `id` obrigados a gerir o domínio e publicar eventos.

## <a name="manage-access-to-topics"></a>Gerir o acesso a tópicos

Gerir o acesso a tópicos é feito através da [atribuição de funções.](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) A atribuição de funções utiliza o controlo de acesso baseado em funções para limitar as operações nos recursos da Azure a utilizadores autorizados num determinado âmbito.

O Event Grid tem duas funções incorporadas, que podes usar para atribuir acesso a determinados utilizadores em vários tópicos dentro de um domínio. Estas funções são `EventGrid EventSubscription Contributor (Preview)` , que permite a criação e eliminação de subscrições, e , que `EventGrid EventSubscription Reader (Preview)` apenas permite a listagem de subscrições de eventos.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azurecli)
Os seguintes limites de comando Azure CLI `alice@contoso.com` para criar e eliminar subscrições de eventos apenas no `demotopic1` tópico:

```azurecli-interactive
az role assignment create \
  --assignee alice@contoso.com \
  --role "EventGrid EventSubscription Contributor (Preview)" \
  --scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)
Os seguintes limites de comando PowerShell `alice@contoso.com` para criar e eliminar subscrições de eventos apenas no `demotopic1` tópico:

```azurepowershell-interactive
New-AzureRmRoleAssignment `
  -SignInName alice@contoso.com `
  -RoleDefinitionName "EventGrid EventSubscription Contributor (Preview)" `
  -Scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```
---

Para obter mais informações sobre a gestão do acesso às operações da Grade de Eventos, consulte [a segurança e a autenticação da Grade de Eventos.](./security-authentication.md)

## <a name="create-topics-and-subscriptions"></a>Criar tópicos e subscrições

O serviço 'Grade de Eventos' cria e gere automaticamente o tópico correspondente num domínio baseado na chamada para criar uma subscrição de evento para um tópico de domínio. Não há um passo separado para criar um tópico num domínio. Da mesma forma, quando a última subscrição do evento para um tópico é eliminada, o tópico também é eliminado.

Subscrever um tópico num domínio é o mesmo que subscrever qualquer outro recurso Azure. Para o ID do recurso de origem, especifique o ID do domínio do evento devolvido ao criar o domínio anteriormente. Para especificar o tópico a que pretende subscrever, adicione `/topics/<my-topic>` ao fim do ID do recurso de origem. Para criar uma subscrição de evento de âmbito de domínio que receba todos os eventos no domínio, especifique o ID do domínio do evento sem especificar quaisquer tópicos.

Normalmente, o utilizador a que concedeu acesso na secção anterior criaria a subscrição. Para simplificar este artigo, cria a subscrição. 

# <a name="azure-cli"></a>[CLI do Azure](#tab/azurecli)

```azurecli-interactive
az eventgrid event-subscription create \
  --name <event-subscription> \
  --source-resource-id "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" \
  --endpoint https://contoso.azurewebsites.net/api/updates
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurepowershell-interactive
New-AzureRmEventGridSubscription `
  -ResourceId "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" `
  -EventSubscriptionName <event-subscription> `
  -Endpoint https://contoso.azurewebsites.net/api/updates
```

---

Se precisar de um ponto final de teste para subscrever os seus eventos, pode sempre implementar uma [aplicação web pré-construída](https://github.com/Azure-Samples/azure-event-grid-viewer) que exibe os eventos que chegam. Pode enviar os seus eventos para o seu site de testes em `https://<your-site-name>.azurewebsites.net/api/updates` .

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

As permissões definidas para um tópico são armazenadas no Diretório Ativo Azure e devem ser eliminadas explicitamente. A eliminação de uma subscrição de eventos não revogará o acesso dos utilizadores à criação de subscrições de eventos se tiverem acesso a um tópico.


## <a name="publish-events-to-an-event-grid-domain"></a>Publicar eventos para um domínio de grade de eventos

Publicar eventos num domínio é o mesmo que [publicar um tópico personalizado.](./post-to-custom-topic.md) No entanto, em vez de publicar para o tópico personalizado, publica todos os eventos no ponto final de domínio. Nos dados do evento JSON, especifica o tópico a que deseja que os eventos se desemosem. A seguinte variedade de eventos resultaria em evento com `"id": "1111"` o tópico `demotopic1` enquanto o evento com seria enviado `"id": "2222"` para o tópico `demotopic2` :

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

# <a name="azure-cli"></a>[CLI do Azure](#tab/azurecli)
Para obter o ponto final de domínio com Azure CLI, use

```azurecli-interactive
az eventgrid domain show \
  -g <my-resource-group> \
  -n <my-domain>
```

Para obter as chaves de um domínio, use:

```azurecli-interactive
az eventgrid domain key list \
  -g <my-resource-group> \
  -n <my-domain>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)
Para obter o ponto final de domínio com PowerShell, use

```azurepowershell-interactive
Get-AzureRmEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```

Para obter as chaves de um domínio, use:

```azurepowershell-interactive
Get-AzureRmEventGridDomainKey `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```
---

E, em seguida, use o seu método favorito de fazer um HTTP POST para publicar os seus eventos no seu domínio De Grelha de Eventos.

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre conceitos de alto nível em domínios de eventos e por que eles são úteis, consulte a [visão geral conceptual dos Domínios de Eventos.](event-domains.md)
