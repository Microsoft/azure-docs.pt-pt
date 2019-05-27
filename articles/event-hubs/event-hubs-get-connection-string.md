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
ms.openlocfilehash: edd197fb6d578df064c67a422767e3e70a0c8142
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66158876"
---
# <a name="get-an-event-hubs-connection-string"></a>Obter uma cadeia de ligação dos Hubs de eventos

Para utilizar os Hubs de eventos, terá de criar um espaço de nomes de Hubs de eventos. Um espaço de nomes é um contentor de âmbito para vários hubs de eventos ou tópicos do Kafka. Este espaço de nomes fornece um exclusivo [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name). Depois de criar um espaço de nomes, pode obter a cadeia de ligação necessária para comunicar com os Hubs de eventos.

A cadeia de ligação para os Hubs de eventos do Azure tem os seguintes componentes incorporados dentro da mesma,

* FQDN = o FQDN do espaço de nomes EventHubs que criou (inclui o nome de espaço de nomes de EventHubs seguido servicebus.windows.net)
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
3. Selecione **os Hubs de eventos** no **Analytics** secção. 
4. Na lista de hubs de eventos, selecione o seu hub de eventos.
6. Sobre o **espaço de nomes de Hubs de eventos** página, selecione **políticas de acesso partilhado** no menu da esquerda.

    ![Partilhado o item de menu de políticas de acesso](./media/event-hubs-get-connection-string/event-hubs-get-connection-string1.png)
7. Selecione um **partilhado a política de acesso** na lista de políticas. A predefinição um com o nome: **RootManageSharedAccessPolicy**. Pode adicionar uma política com permissões adequadas (leitura, escrita) e utilizar essa política. 

    ![Os Hubs de eventos compartilhado políticas de acesso](./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png)
8. Selecione o **cópia** junto aos **ligação chave primária da cadeia de caracteres** campo. 

    ![Os Hubs de eventos - obter cadeia de ligação](./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png)

## <a name="getting-the-connection-string-with-azure-powershell"></a>Obter a cadeia de ligação com o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pode utilizar o [Get-AzEventHubNamespaceKey](/powershell/module/az.eventhub/get-azeventhubkey) para obter a cadeia de ligação para o nome de política/regra específica, conforme mostrado abaixo:

```azurepowershell-interactive
Get-AzEventHubKey -ResourceGroupName dummyresourcegroup -NamespaceName dummynamespace -AuthorizationRuleName RootManageSharedAccessKey
```

## <a name="getting-the-connection-string-with-azure-cli"></a>Obter a cadeia de ligação com a CLI do Azure
Pode utilizar o seguinte para obter a cadeia de ligação para o espaço de nomes:

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --name RootManageSharedAccessKey
```

Para obter mais informações sobre os comandos da CLI do Azure para os Hubs de eventos, consulte [CLI do Azure para os Hubs de eventos](/cli/azure/eventhubs).

## <a name="next-steps"></a>Passos Seguintes

Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Descrição geral dos Hubs de Eventos](event-hubs-what-is-event-hubs.md)
* [Criar um Hub de Eventos](event-hubs-create.md)
