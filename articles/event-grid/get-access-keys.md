---
title: Obtenha a chave de acesso para um recurso de Grade de Eventos
description: Este artigo descreve como obter a chave de acesso para um tópico ou domínio de Grade de Eventos
ms.topic: how-to
ms.date: 07/07/2020
ms.openlocfilehash: cd60777b2e28b82d72f8f2bf93fe0be301e9e280
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775228"
---
# <a name="get-access-keys-for-event-grid-resources-topics-or-domains"></a>Obtenha chaves de acesso para recursos da Grade de Eventos (tópicos ou domínios)
As teclas de acesso são utilizadas para autenticar uma aplicação que publica eventos para recursos da Azure Event Grid (tópicos e domínios). Recomendamos que se regenerar regularmente as suas chaves e guardá-las de forma segura. É-lhe fornecida duas teclas de acesso para que possa manter as ligações utilizando uma chave enquanto regenera a outra.

Este artigo descreve como obter teclas de acesso para um recurso de Grade de Evento (tópico ou domínio) usando o portal Azure, PowerShell ou CLI. 

## <a name="azure-portal"></a>Portal do Azure
No portal Azure, altere para o separador **teclas** de acesso do **Tópico de Grelha** de Evento ou da página de Domínio da Grelha de **Eventos** para o seu tópico ou domínio.  

:::image type="content" source="./media/get-access-keys/azure-portal.png" alt-text="Página de chaves de acesso":::

## <a name="azure-powershell"></a>Azure PowerShell
Utilize o comando [Get-AzEventGridTopicKey](/powershell/module/az.eventgrid/get-azeventgridtopickey) para obter teclas de acesso para tópicos. 

```azurepowershell-interactive
Get-AzEventGridTopicKey -ResourceGroup <RESOURCE GROUP NAME> -Name <TOPIC NAME>
```

Utilize o comando [Get-AzEventGridDomainKey](/powershell/module/az.eventgrid/get-azeventgriddomainkey) para obter chaves de acesso para domínios. 

```azurepowershell-interactive
Get-AzEventGridDomainKey -ResourceGroup <RESOURCE GROUP NAME> -Name <DOMAIN NAME>
```

## <a name="azure-cli"></a>CLI do Azure
Use a [lista de tópicos az eventgrid](/cli/azure/eventgrid/topic/key#az_eventgrid_topic_key_list) para obter chaves de acesso para tópicos. 

```azurecli-interactive
az eventgrid topic key list --resource-group <RESOURCE GROUP NAME> --name <TOPIC NAME>
```

Utilize [a lista de chaves de domínio az eventgrid](/cli/azure/eventgrid/domain/key#az_eventgrid_domain_key_list) para obter chaves de acesso para domínios. 

```azurecli-interactive
az eventgrid domain key list --resource-group <RESOURCE GROUP NAME> --name <DOMAIN NAME>
```

## <a name="next-steps"></a>Passos seguintes
Ver o seguinte artigo: [Autenticar clientes editoriais](security-authenticate-publishing-clients.md). 
