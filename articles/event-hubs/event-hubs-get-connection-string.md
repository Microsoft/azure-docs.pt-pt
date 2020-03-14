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
ms.openlocfilehash: 77a768f907ad989a457ee498f26ad0f6e004f786
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79264937"
---
# <a name="get-an-event-hubs-connection-string"></a>Obter uma cadeia de ligação dos Hubs de eventos

Para utilizar os Hubs de eventos, terá de criar um espaço de nomes de Hubs de eventos. Um espaço de nome é um recipiente de deteção para vários centros de eventos ou tópicos de Kafka. Este espaço de nome dá-lhe um [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)único. Depois de criar um espaço de nomes, pode obter a cadeia de ligação necessária para comunicar com os Hubs de eventos.

A cadeia de ligação para os Hubs de eventos do Azure tem os seguintes componentes incorporados dentro da mesma,

* FQDN = o FQDN do espaço de nome eventHubs que criou (inclui o nome do espaço de nome EventHubs seguido de servicebus.windows.net)
* SharedAccessKeyName = o nome que escolheu para chaves SAS da sua aplicação
* SharedAccessKey = o valor gerado da chave.

O modelo de cadeia de ligação é semelhante a
```
Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>
```

Uma cadeia de conexão de exemplo pode parecer `Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=`

Este artigo o orienta através de várias formas de obter a cadeia de ligação.

## <a name="get-connection-string-from-the-portal"></a>Obter cadeia de ligação a partir do portal
1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
2. Selecione **todos os serviços** no menu de navegação à esquerda. 
3. Selecione Centros de **Eventos** na secção **Analytics.** 
4. Na lista de centros de eventos, selecione o seu centro de eventos.
6. Na página Nomespace do **Event Hubs,** selecione Políticas de **Acesso Partilhado** no menu esquerdo.

    ![Item do menu de políticas de acesso partilhado](./media/event-hubs-get-connection-string/event-hubs-get-connection-string1.png)
7. Selecione uma política de **acesso partilhado** na lista de políticas. O padrão é nomeado: **RootManageSharedAccessPolicy**. Pode adicionar uma política com permissões adequadas (ler, escrever) e usar essa política. 

    ![Políticas de acesso partilhado sintetmente ao Event Hubs](./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png)
8. Selecione o botão **de cópia** ao lado do campo de teclas primário de **ligação.** 

    ![Hubs de eventos - obtenha cadeia de ligação](./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png)

## <a name="getting-the-connection-string-with-azure-powershell"></a>Obter a cadeia de ligação com o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pode utilizar o [Get-AzEventHubKey](/powershell/module/az.eventhub/get-azeventhubkey) para obter a cadeia de ligação para o nome de política/regra específico, como mostrado abaixo:

```azurepowershell-interactive
Get-AzEventHubKey -ResourceGroupName dummyresourcegroup -NamespaceName dummynamespace -AuthorizationRuleName RootManageSharedAccessKey
```

## <a name="getting-the-connection-string-with-azure-cli"></a>Obter a cadeia de ligação com a CLI do Azure
Pode utilizar o seguinte para obter a cadeia de ligação para o espaço de nomes:

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --name RootManageSharedAccessKey
```

Ou pode utilizar o seguinte para obter a cadeia de ligação para uma entidade EventHub:

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --eventhub-name dummyeventhub --name RootManageSharedAccessKey
```

Para mais informações sobre os comandos Azure CLI para Hubs de Eventos, consulte [o Azure CLI para Centros](/cli/azure/eventhubs)de Eventos .

## <a name="next-steps"></a>Passos seguintes

Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Descrição geral dos Hubs de Eventos](event-hubs-what-is-event-hubs.md)
* [Criar um Hub de Eventos](event-hubs-create.md)
