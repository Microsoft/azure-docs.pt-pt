---
title: Entrega de eventos, identidade de serviço gerida e ligação privada
description: Este artigo descreve como permitir a identidade de serviço gerida para um tópico de grelha de eventos Azure. Use-o para encaminhar eventos para destinos apoiados.
ms.topic: how-to
ms.date: 01/28/2021
ms.openlocfilehash: 3e643465db7cc918499ca962c4697cb61cb4b594
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007776"
---
# <a name="event-delivery-with-a-managed-identity"></a>Entrega de eventos com identidade gerida
Este artigo descreve como ativar uma [identidade de serviço gerida](../active-directory/managed-identities-azure-resources/overview.md) para tópicos ou domínios personalizados da grelha de eventos Azure. Use-o para encaminhar eventos para destinos apoiados, como filas e tópicos de Service Bus, centros de eventos e contas de armazenamento.

Aqui estão os passos que são abordados em detalhe neste artigo:
1. Crie um tópico ou domínio personalizado com uma identidade atribuída ao sistema, ou atualize um tópico ou domínio personalizado existente para ativar a identidade. 
1. Adicione a identidade a um papel apropriado (por exemplo, Service Bus Data Sender) no destino (por exemplo, uma fila de autocarros de serviço).
1. Ao criar subscrições de eventos, permita o uso da identidade para entregar eventos ao destino. 

> [!NOTE]
> Atualmente, não é possível entregar eventos usando [pontos finais privados.](../private-link/private-endpoint-overview.md) Para mais informações, consulte a secção [de pontos finais privados](#private-endpoints) no final deste artigo. 

## <a name="create-a-custom-topic-or-domain-with-an-identity"></a>Criar um tópico ou domínio personalizado com uma identidade
Primeiro, vamos ver como criar um tópico ou um domínio com uma identidade gerida pelo sistema.

### <a name="use-the-azure-portal"></a>Utilizar o portal do Azure
Pode ativar a identidade atribuída ao sistema para um tópico ou domínio personalizado enquanto o cria no portal Azure. A imagem a seguir mostra como ativar uma identidade gerida pelo sistema para um tópico personalizado. Basicamente, selecione a opção **Ativar** a identidade atribuída no sistema atribuído na página **Avançada** do assistente de criação de tópicos. Você verá esta opção na página **Avançada** do assistente de criação de domínio também. 

![Ativar a identidade ao criar um tópico personalizado](./media/managed-service-identity/create-topic-identity.png)

### <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure
Também pode utilizar o CLI Azure para criar um tópico ou domínio personalizado com uma identidade atribuída ao sistema. Utilize o `az eventgrid topic create` comando com o parâmetro definido para `--identity` `systemassigned` . Se não especificar um valor para este parâmetro, o valor predefinido `noidentity` é utilizado. 

```azurecli-interactive
# create a custom topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

Da mesma forma, pode utilizar o `az eventgrid domain create` comando para criar um domínio com uma identidade gerida pelo sistema.

## <a name="enable-an-identity-for-an-existing-custom-topic-or-domain"></a>Ativar uma identidade para um tópico ou domínio personalizado existente
Na secção anterior, aprendeu a ativar uma identidade gerida pelo sistema enquanto criava um tópico personalizado ou um domínio. Nesta secção, aprende-se a ativar uma identidade gerida pelo sistema para um tópico ou domínio personalizado existente. 

### <a name="use-the-azure-portal"></a>Utilizar o portal do Azure
O procedimento que se segue mostra-lhe como ativar a identidade gerida pelo sistema para um tópico personalizado. Os passos para permitir uma identidade para um domínio são semelhantes. 

1. Aceda ao [Portal do Azure](https://portal.azure.com).
2. Procure **tópicos de grelha de eventos** na barra de pesquisa no topo.
3. Selecione o **tópico personalizado** para o qual deseja ativar a identidade gerida. 
4. Mude para o **separador Identidade.** 
5. Ligue  o interruptor para ativar a identidade. 
1. **Selecione Guarde** na barra de ferramentas para guardar a definição. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-topic.png" alt-text="Página de identidade para um tópico personalizado"::: 

Pode utilizar passos semelhantes para permitir uma identidade para um domínio de grelha de eventos.

### <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure
Utilize o `az eventgrid topic update` comando com conjunto para permitir a identidade atribuída ao sistema para um tópico personalizado `--identity` `systemassigned` existente. Se quiser desativar a identidade, especifique `noidentity` como o valor. 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

O comando para a atualização de um domínio existente é semelhante `az eventgrid domain update` ().

## <a name="supported-destinations-and-azure-roles"></a>Destinos apoiados e funções de Azure
Depois de ativar a identidade para o tópico ou domínio personalizado da grelha de eventos, o Azure cria automaticamente uma identidade no Azure Ative Directory. Adicione esta identidade às funções Azure apropriadas para que o tópico ou domínio personalizado possa encaminhar eventos para destinos suportados. Por exemplo, adicione a identidade ao papel **de Remetente de dados do Azure Event Hubs** para um espaço de nomes Azure Event Hubs para que o tópico personalizado da grelha de eventos possa encaminhar eventos para centros de eventos nesse espaço de nome. 

Atualmente, a grelha de eventos Azure suporta tópicos ou domínios personalizados configurados com uma identidade gerida atribuída ao sistema para encaminhar eventos para os seguintes destinos. Esta tabela também lhe dá os papéis em que a identidade deve estar para que o tópico personalizado possa encaminhá-lo os eventos.

| Destino | Função do Azure | 
| ----------- | --------- | 
| Filas e tópicos de autocarro de serviço | [Remetente de dados de ônibus de serviço Azure](../service-bus-messaging/authenticate-application.md#azure-built-in-roles-for-azure-service-bus) |
| Azure Event Hubs | [Ender de dados do Azure Event Hubs](../event-hubs/authorize-access-azure-active-directory.md#azure-built-in-roles-for-azure-event-hubs) | 
| Armazenamento de Blobs do Azure | [Contribuinte de Dados do Armazenamento de Blobs](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) |
| Armazenamento de Filas do Azure |[Remetente de mensagem de dados de fila de armazenamento](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) | 

## <a name="add-an-identity-to-azure-roles-on-destinations"></a>Adicione uma identidade aos papéis da Azure nos destinos
Esta secção descreve como adicionar a identidade para o seu tópico ou domínio personalizado a um papel Azure. 

### <a name="use-the-azure-portal"></a>Utilizar o portal do Azure
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

### <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure
O exemplo nesta secção mostra-lhe como usar o CLI Azure para adicionar uma identidade a um papel de Azure. Os comandos da amostra são para tópicos personalizados da grelha de eventos. Os comandos para domínios da grelha de eventos são semelhantes. 

#### <a name="get-the-principal-id-for-the-custom-topics-system-identity"></a>Obtenha a identificação principal para a identidade do sistema do tópico personalizado 
Primeiro, obtenha a identificação principal da identidade gerida pelo sistema do tópico personalizado e atribua a identidade a funções apropriadas.

```azurecli-interactive
topic_pid=$(az ad sp list --display-name "$<TOPIC NAME>" --query [].objectId -o tsv)
```

#### <a name="create-a-role-assignment-for-event-hubs-at-various-scopes"></a>Criar uma atribuição de funções para centros de eventos em vários âmbitos 
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

#### <a name="create-a-role-assignment-for-a-service-bus-topic-at-various-scopes"></a>Crie uma atribuição de funções para um tópico de ônibus de serviço em vários âmbitos 
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

## <a name="create-event-subscriptions-that-use-an-identity"></a>Criar subscrições de eventos que usem uma identidade
Depois de ter um tópico personalizado da grelha de eventos ou um domínio com uma identidade gerida pelo sistema e ter adicionado a identidade ao papel apropriado no destino, está pronto para criar subscrições que utilizem a identidade. 

### <a name="use-the-azure-portal"></a>Utilizar o portal do Azure
Ao criar uma subscrição de eventos, vê uma opção para ativar a utilização de uma identidade atribuída ao sistema para um ponto final na secção **ENDPOINT DETAILS.** 

![Ativar a identidade ao criar uma subscrição de eventos para uma fila de ônibus de serviço](./media/managed-service-identity/service-bus-queue-subscription-identity.png)

Também pode ativar a utilização de uma identidade atribuída ao sistema para ser utilizada para a inscrição de letras mortas no separador **Funcionalidades Adicionais.** 

![Permitir a identidade atribuída ao sistema para a inscrição morta](./media/managed-service-identity/enable-deadletter-identity.png)

### <a name="use-the-azure-cli---service-bus-queue"></a>Use a fila Azure CLI - Service Bus 
Nesta secção, você aprende a usar o CLI Azure para permitir a utilização de uma identidade atribuída ao sistema para entregar eventos a uma fila de ônibus de serviço. A identidade deve ser membro do papel de remetente de **dados de autocarros do serviço Azure.** Também deve ser um membro da função de contribuinte de **dados da Blob de Armazenamento** na conta de armazenamento que é usada para a inscrição. 

#### <a name="define-variables"></a>Definir variáveis
Em primeiro lugar, especifique os valores para as seguintes variáveis a utilizar no comando CLI. 

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the service bus queue resource id
queueid=$(az servicebus queue show --namespace-name <SERVICE BUS NAMESPACE NAME> --name <QUEUE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
sb_esname = "<Specify a name for the event subscription>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Criar uma subscrição de evento usando uma identidade gerida para entrega 
Este comando de amostra cria uma subscrição de evento para um tópico personalizado da grelha de evento com um tipo de ponto final definido para a **fila do Service Bus**. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sb_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery-and-dead-lettering"></a>Crie uma subscrição de evento usando uma identidade gerida para entrega e letras mortas
Este comando de amostra cria uma subscrição de evento para um tópico personalizado da grelha de evento com um tipo de ponto final definido para a **fila do Service Bus**. Especifica igualmente que a identidade gerida pelo sistema deve ser utilizada para a inscrição. 

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

### <a name="use-the-azure-cli---event-hubs"></a>Use o Azure CLI - Centros de Eventos 
Nesta secção, você aprende a usar o CLI Azure para permitir a utilização de uma identidade atribuída ao sistema para entregar eventos a um centro de eventos. A identidade deve ser membro do papel de Remetente de **Dados do Azure Event Hubs.** Também deve ser um membro da função de contribuinte de **dados da Blob de Armazenamento** na conta de armazenamento que é usada para a inscrição. 

#### <a name="define-variables"></a>Definir variáveis
```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID CUSTOM TOPIC NAME>"

hubid=$(az eventhubs eventhub show --name <EVENT HUB NAME> --namespace-name <NAMESPACE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
eh_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Criar uma subscrição de evento usando uma identidade gerida para entrega 
Este comando de amostra cria uma subscrição de evento para um tópico personalizado da grelha de evento com um tipo de ponto final definido para **Centros de Eventos.** 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type eventhub 
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    -n $sbq_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>Criar uma subscrição de evento usando uma identidade gerida para entrega + deadletter 
Este comando de amostra cria uma subscrição de evento para um tópico personalizado da grelha de evento com um tipo de ponto final definido para **Centros de Eventos.** Especifica igualmente que a identidade gerida pelo sistema deve ser utilizada para a inscrição. 

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

### <a name="use-the-azure-cli---azure-storage-queue"></a>Utilize a fila Azure CLI - Azure Storage 
Nesta secção, aprende-se a utilizar o CLI Azure para permitir a utilização de uma identidade atribuída ao sistema para entregar eventos a uma fila de Armazenamento Azure. A identidade deve ser um membro da **função de remetente de mensagens de dados de fila de armazenamento** na conta de armazenamento. Também deve ser um membro da função de contribuinte de **dados da Blob de Armazenamento** na conta de armazenamento que é usada para a inscrição.

#### <a name="define-variables"></a>Definir variáveis  

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID CUSTOM TOPIC NAME>"

# get the storage account resource id
storageid=$(az storage account show --name <STORAGE ACCOUNT NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)

# build the resource id for the queue
queueid="$storageid/queueservices/default/queues/<QUEUE NAME>" 

sa_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Criar uma subscrição de evento usando uma identidade gerida para entrega 

```azurecli-interactive
az eventgrid event-subscription create 
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sa_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>Criar uma subscrição de evento usando uma identidade gerida para entrega + deadletter 

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

## <a name="private-endpoints"></a>Pontos finais privados
Atualmente, não é possível entregar eventos usando [pontos finais privados.](../private-link/private-endpoint-overview.md) Ou seja, não há suporte se tiver requisitos rigorosos de isolamento de rede onde o tráfego de eventos entregues não deve sair do espaço IP privado. 

No entanto, se os seus requisitos exigirem uma forma segura de enviar eventos usando um canal encriptado e uma identidade conhecida do remetente (neste caso, Grade de Evento) usando espaço IP público, poderá entregar eventos a Event Hubs, Service Bus ou Azure Storage service usando um tópico personalizado de rede de eventos Azure ou um domínio com identidade gerida pelo sistema configurado como mostrado neste artigo. Em seguida, pode utilizar um link privado configurado em Funções Azure ou o seu webhook implantado na sua rede virtual para puxar eventos. Consulte a amostra: [Ligue-se a pontos finais privados com funções Azure](/samples/azure-samples/azure-functions-private-endpoints/connect-to-private-endpoints-with-azure-functions/).

Nesta configuração, o tráfego passa pelo IP/internet público, desde a Grade de Eventos até Centros de Eventos, Service Bus ou Azure Storage, mas o canal pode ser encriptado e é utilizada uma identidade gerida da Grade de Eventos. Se configurar as suas Funções Azure ou webhook implantados na sua rede virtual para utilizar um Event Hubs, Service Bus ou Azure Storage via link privado, essa secção do tráfego permanecerá, evidentemente, dentro do Azure.


## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre identidades de serviço geridas, consulte [quais são identidades geridas para recursos Azure](../active-directory/managed-identities-azure-resources/overview.md). 
