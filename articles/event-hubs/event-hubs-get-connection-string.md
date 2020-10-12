---
title: Obter cadeia de conexão - Azure Event Hubs / Microsoft Docs
description: Este artigo fornece instruções para obter uma cadeia de ligação que os clientes podem usar para ligar aos Azure Event Hubs.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 5ae6c66ddbbf4b9946e7037e1a7723043bf60507
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86537195"
---
# <a name="get-an-event-hubs-connection-string"></a>Obtenha uma cadeia de conexão Event Hubs

Para utilizar os Centros de Eventos, precisa de criar um espaço de nomes de Centros de Eventos. Um espaço de nome é um recipiente de deteção para vários centros de eventos ou tópicos kafka. Este espaço de nome dá-lhe um [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)único. Uma vez criado um espaço de nome, pode obter a cadeia de ligação necessária para comunicar com os Centros de Eventos.

A cadeia de ligação para Azure Event Hubs tem os seguintes componentes incorporados no mesmo,

* FQDN = o FQDN do espaço de nomes EventHubs que criou (inclui o nome de espaço de nome EventHubs seguido de servicebus.windows.net)
* SharedAccessKeyName = o nome que escolheu para as teclas SAS da sua aplicação
* SharedAccessKey = o valor gerado da chave.

O modelo de corda de conexão parece
```
Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>
```

Um exemplo de ligação cadeia pode parecer `Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=`

Este artigo acompanha-o através de várias formas de obter a cadeia de ligação.

## <a name="get-connection-string-from-the-portal"></a>Obtenha a cadeia de ligação do portal
1. Inscreva-se no [portal Azure](https://portal.azure.com). 
2. Selecione **Todos os serviços** no menu de navegação à esquerda. 
3. Selecione **Centros de Eventos** na secção **Analytics.** 
4. Na lista de centros de eventos, selecione o seu centro de eventos.
6. Na página **'Event Hubs Namespace',** selecione **Políticas de Acesso Partilhado** no menu esquerdo.

    ![Item do menu de Políticas de Acesso Compartilhado](./media/event-hubs-get-connection-string/event-hubs-get-connection-string1.png)
7. Selecione uma **política de acesso partilhado** na lista de políticas. O padrão é nomeado: **RootManageSharedAccessPolicy**. Pode adicionar uma política com permissões apropriadas (ler, escrever) e usar essa política. 

    ![Políticas de acesso compartilhados de Centros de Eventos](./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png)
8. Selecione o botão de **cópia** ao lado do campo **de teclas primária de ligação.** 

    ![Event Hubs - obtenha a cadeia de conexão](./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png)

## <a name="getting-the-connection-string-with-azure-powershell"></a>Obter a cadeia de ligação com Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pode utilizar o [Get-AzEventHubKey](/powershell/module/az.eventhub/get-azeventhubkey) para obter o fio de ligação para o nome de política/regra específico, como mostrado abaixo:

```azurepowershell-interactive
Get-AzEventHubKey -ResourceGroupName dummyresourcegroup -NamespaceName dummynamespace -AuthorizationRuleName RootManageSharedAccessKey
```

## <a name="getting-the-connection-string-with-azure-cli"></a>Obter a cadeia de ligação com Azure CLI
Pode utilizar o seguinte para obter a cadeia de ligação para o espaço de nomes:

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --name RootManageSharedAccessKey
```

Ou pode usar o seguinte para obter a cadeia de ligação para uma entidade EventHub:

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --eventhub-name dummyeventhub --name RootManageSharedAccessKey
```

Para obter mais informações sobre os comandos Azure CLI para Os Centros de Eventos, consulte [Azure CLI para Centros de Eventos.](/cli/azure/eventhubs)

## <a name="next-steps"></a>Passos seguintes

Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Descrição geral dos Event Hubs](./event-hubs-about.md)
* [Criar um Centro de Eventos](event-hubs-create.md)
