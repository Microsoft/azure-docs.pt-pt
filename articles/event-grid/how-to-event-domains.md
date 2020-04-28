---
title: Publique eventos com domínios de eventos com grelha de eventos Azure
description: Mostra como gerir grandes conjuntos de tópicos na Azure Event Grid e publicar eventos para eles usando domínios de eventos.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 10/22/2019
ms.openlocfilehash: 1d07227249806b7d54523af66817a170c19354ee
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "72786543"
---
# <a name="manage-topics-and-publish-events-using-event-domains"></a>Gerir tópicos e publicar eventos usando domínios de eventos

Este artigo mostra como:

* Criar um domínio de Grelha de Eventos
* Subscreva tópicos da grelha de eventos
* Lista de chaves
* Publique eventos para um domínio

Para saber sobre domínios de eventos, consulte [compreender os domínios do evento para gerir tópicos da Grelha de Eventos.](event-domains.md)

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Instalar função de pré-visualização

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-event-domain"></a>Criar um Domínio de Evento

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

Reparem `endpoint` no `id` e como são obrigados a gerir o domínio e a publicar eventos.

## <a name="manage-access-to-topics"></a>Gerir o acesso a tópicos

A gestão do acesso a tópicos é feita através de atribuição de [funções.](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) A atribuição de funções utiliza o controlo de acesso baseado em funções para limitar as operações nos recursos do Azure a utilizadores autorizados num determinado âmbito.

O Event Grid tem duas funções incorporadas, que pode utilizar para atribuir acesso a determinados utilizadores em vários tópicos dentro de um domínio. Estas funções são `EventGrid EventSubscription Contributor (Preview)`, que permitem a criação e eliminação de subscrições, e `EventGrid EventSubscription Reader (Preview)`, que apenas permite a listagem de subscrições de eventos.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azurecli)
Os seguintes limites `alice@contoso.com` de comando Azure CLI para a `demotopic1`criação e apagando subscrições de eventos apenas sobre o tópico:

```azurecli-interactive
az role assignment create \
  --assignee alice@contoso.com \
  --role "EventGrid EventSubscription Contributor (Preview)" \
  --scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)
Os seguintes limites `alice@contoso.com` de comando PowerShell para criar `demotopic1`e eliminar subscrições de eventos apenas sobre tópico:

```azurepowershell-interactive
New-AzureRmRoleAssignment `
  -SignInName alice@contoso.com `
  -RoleDefinitionName "EventGrid EventSubscription Contributor (Preview)" `
  -Scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```
---

Para obter mais informações sobre a gestão do acesso às operações da Rede de Eventos, consulte [a segurança e a autenticação da Rede de Eventos.](./security-authentication.md)

## <a name="create-topics-and-subscriptions"></a>Criar tópicos e subscrições

O serviço De Rede de Eventos cria e gere automaticamente o tópico correspondente num domínio baseado na chamada para criar uma subscrição de evento para um tópico de domínio. Não há nenhum passo separado para criar um tópico num domínio. Da mesma forma, quando a última subscrição do evento para um tópico é eliminada, o tópico também é eliminado.

Subscrever um tema num domínio é o mesmo que subscrever qualquer outro recurso Azure. Para o ID do recurso de origem, especifique o ID do domínio do evento devolvido ao criar o domínio mais cedo. Para especificar o tópico a que `/topics/<my-topic>` pretende subscrever, adicione ao fim o ID do recurso de origem. Para criar uma subscrição de evento de âmbito de domínio que receba todos os eventos no domínio, especifique o ID do domínio do evento sem especificar quaisquer tópicos.

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

Se precisar de um ponto final de teste para subscrever os seus eventos, pode sempre implementar uma [aplicação web pré-construída](https://github.com/Azure-Samples/azure-event-grid-viewer) que exibe os eventos que estão a chegar. Pode enviar os seus eventos `https://<your-site-name>.azurewebsites.net/api/updates`para o seu site de teste em .

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

As permissões definidas para um tópico são armazenadas no Diretório Ativo Azure e devem ser eliminadas explicitamente. A redução de uma subscrição de eventos não revogará o acesso dos utilizadores à criação de subscrições de eventos se tiverem acesso a um tópico.


## <a name="publish-events-to-an-event-grid-domain"></a>Publique eventos para um domínio de grelha de eventos

Publicar eventos num domínio é o mesmo que [publicar para um tópico personalizado.](./post-to-custom-topic.md) No entanto, em vez de publicar para o tópico personalizado, publica todos os eventos para o ponto final do domínio. Nos dados do evento JSON, especifica o tópico a que deseja que os eventos se desloquem. O seguinte conjunto de eventos `"id": "1111"` resultaria `demotopic1` em `"id": "2222"` evento com tema, enquanto evento com seria enviado para tópico: `demotopic2`

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
Para obter o ponto final de domínio com o Azure CLI, use

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

# <a name="powershell"></a>[PowerShell](#tab/powershell)
Para obter o ponto final de domínio com powerShell, use

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

E, em seguida, use o seu método favorito de fazer um POST HTTP para publicar os seus eventos no seu domínio De Event Grid.

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre conceitos de alto nível nos domínios do Evento e por que são úteis, consulte a [visão geral conceptual dos Domínios do Evento.](event-domains.md)
