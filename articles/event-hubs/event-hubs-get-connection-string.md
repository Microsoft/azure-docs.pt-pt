---
title: Obtenha cordas de ligação - Hubs de eventos Azure / Microsoft Docs
description: Este artigo fornece instruções para obter uma cadeia de ligação que os clientes podem usar para ligar aos Hubs de Eventos Azure.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79264937"
---
# <a name="get-an-event-hubs-connection-string"></a>Obtenha uma cadeia de conexão De Hubs de Eventos

Para utilizar os Centros de Eventos, é necessário criar um espaço de nome sinuoso do Event Hubs. Um espaço de nome é um recipiente de deteção para vários centros de eventos ou tópicos de Kafka. Este espaço de nome dá-lhe um [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)único. Uma vez criado um espaço de nome, pode obter a cadeia de ligação necessária para comunicar com os Centros de Eventos.

A cadeia de ligação para os Hubs de Eventos Azure tem os seguintes componentes incorporados no mesmo,

* FQDN = o FQDN do espaço de nome eventHubs que criou (inclui o nome do espaço de nome EventHubs seguido de servicebus.windows.net)
* SharedAccessKeyName = o nome que escolheu para as teclas SAS da sua aplicação
* SharedAccessKey = o valor gerado da chave.

O modelo de corda de ligação parece
```
Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>
```

Uma cadeia de conexão de exemplo pode parecer`Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=`

Este artigo acompanha-o através de várias formas de obter a cadeia de ligação.

## <a name="get-connection-string-from-the-portal"></a>Obtenha a cadeia de ligação do portal
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

## <a name="getting-the-connection-string-with-azure-powershell"></a>Obter a cadeia de ligação com Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pode utilizar o [Get-AzEventHubKey](/powershell/module/az.eventhub/get-azeventhubkey) para obter a cadeia de ligação para o nome de política/regra específico, como mostrado abaixo:

```azurepowershell-interactive
Get-AzEventHubKey -ResourceGroupName dummyresourcegroup -NamespaceName dummynamespace -AuthorizationRuleName RootManageSharedAccessKey
```

## <a name="getting-the-connection-string-with-azure-cli"></a>Obter a cadeia de ligação com O ClI Azure
Pode utilizar o seguinte para obter a corda de ligação para o espaço de nome:

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

* [Descrição geral dos Event Hubs](event-hubs-what-is-event-hubs.md)
* [Criar um Hub de Eventos](event-hubs-create.md)
