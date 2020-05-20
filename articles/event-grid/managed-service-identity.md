---
title: Entrega de eventos com identidade de serviço gerida
description: Este artigo descreve como ativar a identidade de serviço gerida para um tópico de grelha de eventos Azure. Use-o para encaminhar eventos para destinos apoiados.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 04/24/2020
ms.author: spelluru
ms.openlocfilehash: 4d96f28b98cccada2ac5c77589acc6df1430bb02
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83700650"
---
# <a name="event-delivery-with-managed-identity"></a>Entrega de eventos com identidade gerida
Este artigo descreve como ativar a [identidade de serviço gerida](../active-directory/managed-identities-azure-resources/overview.md) para um tópico ou domínio da grelha de eventos. Use-o para encaminhar eventos para destinos suportados, como filas de ônibus de serviço e tópicos, centros de eventos e contas de armazenamento.

Aqui estão os passos que são abordados em detalhe neste artigo:
1. Criar um tópico ou domínio com uma identidade (ou) atualização de identidade (ou) de um tópico ou domínio existente para permitir a identidade. 
2. Adicione a identidade a uma função apropriada (exemplo: Prestador de dados do autocarro) no destino (exemplo: uma fila de ônibus de serviço)
3. Ao criar subscrições de eventos, permita o uso da identidade para entregar eventos ao destino. 

## <a name="create-a-topic-or-domain-with-an-identity"></a>Criar um tópico ou domínio com uma identidade
Primeiro, vamos ver como criar um tópico ou um domínio com uma identidade gerida pelo sistema.

### <a name="using-azure-portal"></a>Com o Portal do Azure
Pode ativar a identidade atribuída ao sistema para um tópico/domínio enquanto a cria no portal Azure. A imagem que se segue mostra como ativar a identidade gerida pelo sistema para um tópico. Basicamente, seleciona a opção Ativar o **sistema atribuído** à identidade na página **Avançada** do assistente de criação de tópicos. Você verá esta opção na página **avançada** do assistente de criação de domínio também. 

![Ativar a identidade ao criar um tópico](./media/managed-service-identity/create-topic-identity.png)

### <a name="using-azure-cli"></a>Utilizar a CLI do Azure
Também pode utilizar o Azure CLI para criar um tópico ou domínio com uma identidade atribuída ao sistema. Utilize o `az eventgrid topic create` comando com o parâmetro definido para `--identity` `systemassigned` . Se não especificar um valor para este parâmetro, o valor predefinido `noidentity` é utilizado. 

```azurecli-interactive
# create a topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

Da mesma forma, pode usar o `az eventgrid domain create` comando para criar um domínio com uma identidade gerida pelo sistema.

## <a name="enable-identity-for-an-existing-topic-or-domain"></a>Ativar a identidade para um tópico ou domínio existente
Na última secção, aprendeu a ativar a identidade gerida pelo sistema enquanto criava um tópico ou um domínio. Nesta secção, aprende-se a ativar a identidade gerida pelo sistema para um tópico ou domínio existente. 

### <a name="using-azure-portal"></a>Com o Portal do Azure
1. Navegue para o [portal do Azure](https://portal.azure.com)
2. Procure **tópicos** de grelha de eventos na barra de pesquisa.
3. Selecione o **tópico** para o qual pretende ativar a identidade gerida. 
4. Mude para o separador **Identidade.** 
5. Ligue o interruptor para ativar a identidade. 

    Pode utilizar passos semelhantes para permitir a identidade de um domínio de grelha de eventos.

### <a name="using-azure-cli"></a>Utilizar a CLI do Azure
Utilize o comando definido para ativar a identidade atribuída ao `az eventgrid topic update` sistema para um tópico `--identity` `systemassigned` existente. Se quiser desativar a identidade, especifique `noidentity` como valor. 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

O comando para atualizar um domínio existente é semelhante `az eventgrid domain update` ().

## <a name="supported-destinations-and-role-based-access-check-rbac-roles"></a>Destinos suportados e funções de Verificação de Acesso baseada em Papéis (RBAC)
Depois de ativar a identidade para o tópico ou domínio da grelha de eventos, o Azure cria automaticamente uma identidade no Diretório Ativo Azure (Azure AD). Adicione esta identidade às funções RBAC apropriadas para que o tópico ou domínio possa encaminhar eventos para destinos apoiados. Por exemplo, adicione a identidade ao papel de Remetente de Dados do **Azure Event Hubs** para um espaço de nome de Event Hubs para que o tópico da grelha de eventos possa encaminhar eventos para centros de eventos nesse espaço de nome.  

Atualmente, a Azure Event Grid suporta tópicos ou domínios configurados com identidade gerida atribuída pelo sistema para encaminhar eventos para os seguintes destinos. Esta tabela também lhe dá os papéis em que a identidade deve estar para que o tópico possa transmitir os eventos.

| Destino | Função RBAC | 
| ----------- | --------- | 
| Filas e tópicos de ônibus de serviço | [Remetente de dados de ônibus de serviço Azure](../service-bus-messaging/authenticate-application.md#built-in-rbac-roles-for-azure-service-bus) |
| Hub de eventos | [Remetente de dados do Azure Event Hubs](../event-hubs/authorize-access-azure-active-directory.md#built-in-rbac-roles-for-azure-event-hubs) | 
| Armazenamento de blobs | [Contribuinte de dados blob de armazenamento](../storage/common/storage-auth-aad-rbac-portal.md#rbac-roles-for-blobs-and-queues) |
| Armazenamento de filas |[Remetente de mensagem de dados de fila de armazenamento](../storage/common/storage-auth-aad-rbac-portal.md#rbac-roles-for-blobs-and-queues) | 

## <a name="add-identity-to-rbac-roles-on-destinations"></a>Adicionar identidade às funções RBAC em destinos
Esta secção descreve como adicionar a identidade para o seu tópico ou domínio a uma função RBAC. 

### <a name="using-azure-portal"></a>Com o Portal do Azure
Pode utilizar o **portal Azure** para atribuir a identidade tópico/domínio a uma função adequada para que o tópico/domínio possa encaminhar eventos para o destino. 

O exemplo seguinte adiciona uma identidade gerida para um tópico de grelha de evento chamado **msitesttópico** para a função De sender de dados de ônibus de **serviço azure** para um espaço de **nome** de ônibus de serviço que contém uma fila ou recurso tópico. Quando se adiciona ao papel ao nível do espaço de nome, o tópico pode encaminhar eventos para todas as entidades com o espaço de nome. 

1. Navegue para o seu espaço de **nome service Bus** no portal [Azure](https://portal.azure.com). 
2. Selecione **Controlo de Acesso** no painel esquerdo. 
3. Selecione **Adicionar** na secção **de atribuição de funções.** 
4. Na página **adicionar uma função** de atribuição, faça os seguintes passos:
    1. Selecione o papel. Neste caso, é o Remetente de Dados de **Autocarros de Serviço Azure.** 
    2. Selecione a **identidade** para o seu tópico ou domínio. 
    3. selecione **Guardar** para salvar a configuração.

Os passos são semelhantes para adicionar uma identidade a outras funções mencionadas na tabela. 

### <a name="using-azure-cli"></a>Utilizar a CLI do Azure
O exemplo nesta secção mostra-lhe como usar o **Azure CLI** para adicionar uma identidade a uma função RBAC. Os comandos da amostra são para tópicos de grelha de eventos. Os comandos para domínios da grelha de eventos são semelhantes. 

#### <a name="get-principal-id-for-the-topics-system-identity"></a>Obtenha identificação principal para a identidade do sistema do tópico 
Em primeiro lugar, obtenha a identificação principal da identidade gerida pelo sistema do tópico e atribua a identidade a papéis apropriados.

```azurecli-interactive
topic_pid=$(az ad sp list --display-name "$<TOPIC NAME>" --query [].objectId -o tsv)
```

#### <a name="create-a-role-assignment-for-event-hubs-at-various-scopes"></a>Criar uma atribuição de funções para centros de eventos em vários âmbitos 
O exemplo cli seguinte mostra como adicionar a identidade de um tópico ao papel de Remetente de **Dados do Azure Event Hubs** a nível do espaço de nome ou ao nível do hub do evento. Se criar a atribuição de papéis no espaço de nome, o tópico pode encaminhar eventos para todos os centros de eventos nesse espaço de nome. Se criar ao nível do centro do evento, o tópico só pode encaminhar eventos para esse centro de eventos específico. 


```azurecli-interactive
role="Azure Event Hubs Data Sender" 
namespaceresourceid=$(az eventhubs namespace show -n $<EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 
eventhubresourceid=$(az eventhubs eventhub show -n <EVENT HUB NAME> --namespace-name <EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one event hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$eventhubresourceid" 
```

#### <a name="create-a-role-assignment-for-service-bus-topic-at-various-scopes"></a>Criar uma atribuição de funções para o tópico de ônibus de serviço em vários âmbitos 
O exemplo cli seguinte mostra como adicionar a identidade de um tópico ao papel de Remetente de Dados de **Ônibus de Serviço Azure** ao nível do espaço de nome ou ao nível do tópico do Ônibus de serviço. Se criar a atribuição de funções no espaço de nome, o tópico da grelha de eventos pode transmitir eventos a todas as entidades (filas de ônibus de serviço ou tópicos) dentro desse espaço de nome. Se criar na fila de ônibus de serviço ou nível tópico, o tópico da grelha de eventos só pode transmitir eventos para essa fila ou tópico específico do Service Bus. 

```azurecli-interactive
role="Azure Service Bus Data Sender" 
namespaceresourceid=$(az servicebus namespace show -n $RG\SB -g "$RG" --query "{I:id}" -o tsv 
sbustopicresourceid=$(az servicebus topic show -n topic1 --namespace-name $RG\SB -g "$RG" --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$sbustopicresourceid" 
```

## <a name="create-event-subscriptions-that-use-identity"></a>Criar subscrições de eventos que utilizem identidade
Depois de ter um tópico ou um domínio com uma identidade gerida pelo sistema e adicionado a identidade ao papel apropriado no destino, está pronto para criar subscrições que utilizem a identidade. 

### <a name="using-azure-portal"></a>Com o Portal do Azure
Ao criar uma subscrição de evento, verá uma opção para permitir o uso da identidade atribuída ao sistema para um ponto final na secção **DETALHES ENDPOINT.** 

![Ativar a identidade ao criar subscrição de eventos para a fila service Bus](./media/managed-service-identity/service-bus-queue-subscription-identity.png)

Também pode ativar a utilização de identidade atribuída ao sistema para ser utilizada para letras mortas no separador **funcionalidades Adicionais.** 

![Ativar a identidade atribuída ao sistema para letras mortas](./media/managed-service-identity/enable-deadletter-identity.png)

### <a name="using-azure-cli---service-bus-queue"></a>Utilização da Azure CLI - fila de ônibus de serviço 
Nesta secção, aprende-se a utilizar o **Azure CLI** para permitir o uso da identidade atribuída ao sistema para entregar eventos a uma fila de ônibus de serviço. A identidade deve ser membro da função **de Remetente** de Dados de Autocarros de Serviço Saque. Deve também ser um membro do papel de Contribuinte de **Dados blob** de armazenamento na conta de armazenamento que é usada para letras mortas. 

#### <a name="define-variables"></a>Definir variáveis
Em primeiro lugar, especifique os valores para as seguintes variáveis a utilizar no comando CLI. 

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the service bus queue resource id
queueid=$(az servicebus queue show --namespace-name <SERVICE BUS NAMESPACE NAME> --name <QUEUE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
sb_esname = "<Specify a name for the event subscription>" 
```

#### <a name="create-an-event-subscription-using-managed-identity-for-delivery"></a>criar uma subscrição de evento usando identidade gerida para entrega 
Este comando de amostra cria uma subscrição de evento para um tópico de grelha de evento com tipo de ponto final definido para **a fila de ônibus de serviço**. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sb_esname 
```

#### <a name="create-an-event-subscription-using-managed-identity-for-delivery-and-dead-lettering"></a>criar uma subscrição de evento usando identidade gerida para entrega e letras mortas
Este comando de amostra cria uma subscrição de evento para um tópico de grelha de evento com tipo de ponto final definido para **a fila de ônibus de serviço**. Especifica igualmente que a identidade gerida pelo sistema deve ser utilizada para letras mortas. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sb_esnameq 
```

### <a name="azure-cli---event-hubs"></a>Azure CLI - Hubs de Eventos 
Nesta secção, aprende-se a utilizar o **Azure CLI** para permitir o uso da identidade atribuída ao sistema para entregar eventos a um centro de eventos. A identidade deve ser membro do papel de Remetente de Dados do **Azure Event Hubs.** Deve também ser um membro do papel de Contribuinte de **Dados blob** de armazenamento na conta de armazenamento que é usada para letras mortas. 

#### <a name="define-variables"></a>Definir variáveis
```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

hubid=$(az eventhubs eventhub show --name <EVENT HUB NAME> --namespace-name <NAMESPACE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
eh_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery"></a>criar subscrição de evento usando identidade gerida para entrega 
Este comando de amostra cria uma subscrição de evento para um tópico de grelha de eventocom o tipo de ponto final definido para **Os Hubs de Eventos**. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type eventhub 
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    -n $sbq_esname 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery--deadletter"></a>Criar subscrição de evento utilizando identidade gerida para entrega + deadletter 
Este comando de amostra cria uma subscrição de evento para um tópico de grelha de eventocom o tipo de ponto final definido para **Os Hubs de Eventos**. Especifica igualmente que a identidade gerida pelo sistema deve ser utilizada para letras mortas. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    --deadletter-identity-endpoint $eh_deadletterendpoint
    --deadletter-identity systemassigned 
    -n $eh_esname 
```

### <a name="azure-cli---azure-storage-queue"></a>Azure CLI - Fila de Armazenamento Azure 
Nesta secção, aprende-se a utilizar o **Azure CLI** para permitir que a utilização da identidade atribuída ao sistema entregue eventos a uma fila de Armazenamento Azure. A identidade deve ser membro da função de Contribuinte de **Dados blob** de armazenamento na conta de armazenamento.

#### <a name="define-variables"></a>Definir variáveis  

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the storage account resource id
storageid=$(az storage account show --name <STORAGE ACCOUNT NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)

# build the resource id for the queue
queueid="$storageid/queueservices/default/queues/<QUEUE NAME>" 

sa_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery"></a>Criar subscrição de evento usando identidade gerida para entrega 

```azurecli-interactive
az eventgrid event-subscription create 
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sa_esname 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery--deadletter"></a>Criar subscrição de evento utilizando identidade gerida para entrega + deadletter 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sa_esname 
```



## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre identidades de serviço geridas, consulte [as identidades geridas para os recursos do Azure.](../active-directory/managed-identities-azure-resources/overview.md) 