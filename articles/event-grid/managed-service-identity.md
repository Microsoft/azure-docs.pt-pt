---
title: Entrega de eventos, identidade de serviço gerida e ligação privada
description: Este artigo descreve como permitir a identidade de serviço gerida para um tópico de grelha de eventos Azure. Use-o para encaminhar eventos para destinos apoiados.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 76f10b4627dc9578b1e616a868eab03431b59b69
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625283"
---
# <a name="event-delivery-with-a-managed-identity"></a>Entrega de eventos com identidade gerida
Este artigo descreve como usar uma [identidade de serviço gerida](../active-directory/managed-identities-azure-resources/overview.md) para um tópico de sistema de grelha de eventos Azure, tópico personalizado ou domínio. Use-o para encaminhar eventos para destinos apoiados, como filas e tópicos de Service Bus, centros de eventos e contas de armazenamento.



## <a name="prerequisites"></a>Pré-requisitos
1. Atribua uma identidade atribuída ao sistema a um tópico do sistema, a um tópico personalizado ou a um domínio. 
    - Para tópicos e domínios personalizados, consulte [Ativar a identidade gerida para tópicos e domínios personalizados.](enable-identity-custom-topics-domains.md) 
    - Para tópicos do sistema, consulte [Ativar a identidade gerida para tópicos do sistema](enable-identity-system-topics.md)
1. Adicione a identidade a um papel apropriado (por exemplo, Service Bus Data Sender) no destino (por exemplo, uma fila de autocarros de serviço). Para etapas detalhadas, consulte [adicionar identidade às funções de Azure nos destinos](add-identity-roles.md)

    > [!NOTE]
    > Atualmente, não é possível entregar eventos usando [pontos finais privados.](../private-link/private-endpoint-overview.md) Para mais informações, consulte a secção [de pontos finais privados](#private-endpoints) no final deste artigo. 

## <a name="create-event-subscriptions-that-use-an-identity"></a>Criar subscrições de eventos que usem uma identidade
Depois de ter um tópico ou tópico ou domínio personalizado da grelha de eventos com uma identidade gerida pelo sistema e ter adicionado a identidade ao papel adequado no destino, está pronto para criar subscrições que utilizem a identidade. 

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
Para conhecer as identidades geridas, veja [o que são identidades geridas para os recursos da Azure.](../active-directory/managed-identities-azure-resources/overview.md)
