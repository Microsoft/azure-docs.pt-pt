---
title: Obter cadeia de ligação - Event Hubs do Azure | Documentos da Microsoft
description: Este artigo fornece instruções para obter uma cadeia de ligação que os clientes podem utilizar para ligar ao Event Hubs do Azure.
services: event-hubs
documentationcenter: na
author: spelluru
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 02/19/2019
ms.author: spelluru
ms.openlocfilehash: 744151a1ce8cde630e26c17ccf06569ebd0efb61
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771007"
---
# <a name="get-an-event-hubs-connection-string"></a>Obter uma cadeia de ligação dos Hubs de eventos

Para utilizar os Hubs de eventos, terá de criar um espaço de nomes de Hubs de eventos. Um namespace é um contêiner de escopo para vários hubs de eventos ou tópicos de Kafka. Este espaço de nomes fornece um exclusivo [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name). Depois de criar um espaço de nomes, pode obter a cadeia de ligação necessária para comunicar com os Hubs de eventos.

A cadeia de ligação para os Hubs de eventos do Azure tem os seguintes componentes incorporados dentro da mesma,

* FQDN = o FQDN do namespace EventHubs que você criou (ele inclui o nome do namespace EventHubs seguido por servicebus.windows.net)
* SharedAccessKeyName = o nome que escolheu para chaves SAS da sua aplicação
* SharedAccessKey = o valor gerado da chave.

O modelo de cadeia de ligação é semelhante a
```
Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>
```

Como pode ser uma cadeia de ligação de exemplo `Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=`

Este artigo o orienta através de várias formas de obter a cadeia de ligação.

## <a name="get-connection-string-from-the-portal"></a>Obter cadeia de ligação a partir do portal
1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
2. Selecione **todos os serviços** no menu de navegação à esquerda. 
3. Selecione **hubs de eventos** na seção **análise** . 
4. Na lista de hubs de eventos, selecione seu hub de eventos.
6. Na página **namespace de hubs de eventos** , selecione **políticas de acesso compartilhado** no menu à esquerda.

    ![Item de menu políticas de acesso compartilhado](./media/event-hubs-get-connection-string/event-hubs-get-connection-string1.png)
7. Selecione uma **política de acesso compartilhado** na lista de políticas. O padrão é chamado: **RootManageSharedAccessPolicy**. Você pode adicionar uma política com permissões apropriadas (leitura, gravação) e usar essa política. 

    ![Políticas de acesso compartilhado dos hubs de eventos](./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png)
8. Selecione o botão **copiar** ao lado do campo **cadeia de conexão-chave primária** . 

    ![Hubs de eventos-obter cadeia de conexão](./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png)

## <a name="getting-the-connection-string-with-azure-powershell"></a>Obter a cadeia de ligação com o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Você pode usar o [Get-AzEventHubNamespaceKey](/powershell/module/az.eventhub/get-azeventhubkey) para obter a cadeia de conexão para o nome de política/regra específico, conforme mostrado abaixo:

```azurepowershell-interactive
Get-AzEventHubKey -ResourceGroupName dummyresourcegroup -NamespaceName dummynamespace -AuthorizationRuleName RootManageSharedAccessKey
```

## <a name="getting-the-connection-string-with-azure-cli"></a>Obter a cadeia de ligação com a CLI do Azure
Pode utilizar o seguinte para obter a cadeia de ligação para o espaço de nomes:

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --name RootManageSharedAccessKey
```

Ou você pode usar o seguinte para obter a cadeia de conexão para uma entidade do EventHub:

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --eventhub-name dummyeventhub --name RootManageSharedAccessKey
```

Para obter mais informações sobre CLI do Azure comandos para hubs de eventos, consulte [CLI do Azure para os hubs de eventos](/cli/azure/eventhubs).

## <a name="next-steps"></a>Passos seguintes

Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Event Hubs Overview (Descrição Geral dos Hubs de Eventos)](event-hubs-what-is-event-hubs.md)
* [Criar um Hub de Eventos](event-hubs-create.md)
