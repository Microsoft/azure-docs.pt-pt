---
title: Adicione identidade gerida a um papel no destino Azure Event Grid
description: Este artigo descreve como adicionar identidade gerida a funções Azure em destinos como Azure Service Bus e Azure Event Hubs.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 1578e4c24201614ce89351b3c3cee52a09cadc30
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280484"
---
# <a name="grant-managed-identity-the-access-to-event-grid-destination"></a>Grant geriu identidade o acesso ao destino Event Grid
Esta secção descreve como adicionar a identidade para o tópico do seu sistema, tópico personalizado ou domínio a um papel Azure. 

## <a name="prerequisites"></a>Pré-requisitos
Atribuir uma identidade gerida atribuída ao sistema utilizando instruções dos seguintes artigos:

- [Tópicos ou domínios personalizados](enable-identity-custom-topics-domains.md)
- [Tópicos de sistema](enable-identity-system-topics.md)

## <a name="supported-destinations-and-azure-roles"></a>Destinos apoiados e funções de Azure
Depois de ativar a identidade para o tópico ou domínio personalizado da grelha de eventos, o Azure cria automaticamente uma identidade no Azure Ative Directory. Adicione esta identidade às funções Azure apropriadas para que o tópico ou domínio personalizado possa encaminhar eventos para destinos suportados. Por exemplo, adicione a identidade ao papel **de Remetente de dados do Azure Event Hubs** para um espaço de nomes Azure Event Hubs para que o tópico personalizado da grelha de eventos possa encaminhar eventos para centros de eventos nesse espaço de nome. 

Atualmente, a grelha de eventos Azure suporta tópicos ou domínios personalizados configurados com uma identidade gerida atribuída ao sistema para encaminhar eventos para os seguintes destinos. Esta tabela também lhe dá os papéis em que a identidade deve estar para que o tópico personalizado possa encaminhá-lo os eventos.

| Destino | Função do Azure | 
| ----------- | --------- | 
| Filas e tópicos de autocarro de serviço | [Remetente de dados de ônibus de serviço Azure](../service-bus-messaging/authenticate-application.md#azure-built-in-roles-for-azure-service-bus) |
| Azure Event Hubs | [Ender de dados do Azure Event Hubs](../event-hubs/authorize-access-azure-active-directory.md#azure-built-in-roles-for-azure-event-hubs) | 
| Armazenamento de Blobs do Azure | [Contribuinte de Dados do Armazenamento de Blobs](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) |
| Armazenamento de Filas do Azure |[Remetente de mensagem de dados de fila de armazenamento](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) | 


## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure
Pode utilizar o portal Azure para atribuir o tópico personalizado ou a identidade de domínio a um papel apropriado para que o tópico ou domínio personalizado possa encaminhar eventos para o destino. 

O exemplo a seguir adiciona uma identidade gerida para um tópico personalizado da grelha de eventos chamado **msitesttopic** para o papel **de remetente de dados de autocarros do serviço Azure** para um espaço de nomes de Service Bus que contém uma fila ou recurso tópico. Quando adiciona ao papel ao nível do espaço de nome, o tópico personalizado da grelha de eventos pode encaminhar eventos para todas as entidades dentro do espaço de nome. 

1. Aceda ao seu **espaço de nomes** de Service Bus no [portal Azure.](https://portal.azure.com) 
1. Selecione **Controlo de Acesso** no painel esquerdo. 
1. **Selecione Adicionar** na secção Adicionar uma tarefa **de função.** 
1. Na página **De atribuição de funções,** faça os seguintes passos:
    1. Selecione o papel. Neste caso, é o Remetente de Dados de **Autocarros da Azure Service.** 
    1. Selecione a **identidade** para o tópico ou domínio personalizado da grelha de evento. 
    1. **Selecione Guardar** para guardar a configuração.

Os passos são semelhantes para adicionar uma identidade a outros papéis mencionados na tabela. 

## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure
O exemplo nesta secção mostra-lhe como usar o CLI Azure para adicionar uma identidade a um papel de Azure. Os comandos da amostra são para tópicos personalizados da grelha de eventos. Os comandos para domínios da grelha de eventos são semelhantes. 

### <a name="get-the-principal-id-for-the-custom-topics-system-identity"></a>Obtenha a identificação principal para a identidade do sistema do tópico personalizado 
Primeiro, obtenha a identificação principal da identidade gerida pelo sistema do tópico personalizado e atribua a identidade a funções apropriadas.

```azurecli-interactive
topic_pid=$(az ad sp list --display-name "$<TOPIC NAME>" --query [].objectId -o tsv)
```

### <a name="create-a-role-assignment-for-event-hubs-at-various-scopes"></a>Criar uma atribuição de funções para centros de eventos em vários âmbitos 
O exemplo cli a seguir mostra como adicionar a identidade de um tópico personalizado ao papel **de Remetente de Dados do Azure Event Hubs** ao nível do espaço de nome ou ao nível do centro de eventos. Se criar a atribuição de funções ao nível do espaço de nome, o tópico personalizado pode encaminhar eventos para todos os centros de eventos nesse espaço de nome. Se criar uma atribuição de funções ao nível do centro de eventos, o tópico personalizado só pode encaminhar eventos para esse centro de eventos específico. 


```azurecli-interactive
role="Azure Event Hubs Data Sender" 
namespaceresourceid=$(az eventhubs namespace show -n $<EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 
eventhubresourceid=$(az eventhubs eventhub show -n <EVENT HUB NAME> --namespace-name <EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one event hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$eventhubresourceid" 
```

### <a name="create-a-role-assignment-for-a-service-bus-topic-at-various-scopes"></a>Crie uma atribuição de funções para um tópico de ônibus de serviço em vários âmbitos 
O exemplo cli a seguir mostra como adicionar a identidade de um tópico personalizado da grelha de eventos ao papel **de Remetente de dados de autocarros do serviço Azure** ao nível do espaço de nome ou ao nível do tópico do Service Bus. Se criar a atribuição de funções ao nível do espaço de nome, o tópico da grelha de eventos pode encaminhar eventos para todas as entidades (filas ou tópicos do Service Bus) dentro desse espaço de nome. Se criar uma atribuição de funções na fila do Service Bus ou no nível tópico, o tópico personalizado da grelha de eventos só pode encaminhar eventos para essa fila ou tópico específico do Service Bus. 

```azurecli-interactive
role="Azure Service Bus Data Sender" 
namespaceresourceid=$(az servicebus namespace show -n $RG\SB -g "$RG" --query "{I:id}" -o tsv 
sbustopicresourceid=$(az servicebus topic show -n topic1 --namespace-name $RG\SB -g "$RG" --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$sbustopicresourceid" 
```

## <a name="next-steps"></a>Passos seguintes
Agora que atribuiu uma identidade atribuída ao seu sistema, tópico personalizado ou domínio, e adicionou a identidade às funções apropriadas nos destinos, consulte [Entregar eventos usando a identidade gerida](managed-service-identity.md) na entrega de eventos a destinos usando a identidade.


