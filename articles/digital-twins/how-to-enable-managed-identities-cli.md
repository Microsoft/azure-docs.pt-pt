---
title: Ativar uma identidade gerida para eventos de encaminhamento (pré-visualização) - CLI
titleSuffix: Azure Digital Twins
description: Veja como ativar uma identidade atribuída ao sistema para a Azure Digital Twins e use-a para encaminhar eventos, utilizando o Azure CLI.
author: baanders
ms.author: baanders
ms.date: 02/09/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: c9ce87584373bd87a8f89ecb4ea692b44d3fab4d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102202965"
---
# <a name="enable-a-managed-identity-for-routing-azure-digital-twins-events-preview-azure-cli"></a>Ativar uma identidade gerida para encaminhamento de eventos Azure Digital Twins (pré-visualização): Azure CLI

[!INCLUDE [digital-twins-managed-service-identity-selector.md](../../includes/digital-twins-managed-service-identity-selector.md)]

Este artigo descreve como permitir uma identidade atribuída ao [sistema para uma instância Azure Digital Twins](concepts-security.md#managed-identity-for-accessing-other-resources-preview) (atualmente em pré-visualização), e usar a identidade ao encaminhar eventos para destinos apoiados como [Event Hub,](../event-hubs/event-hubs-about.md) [Destinos de Autocarros](../service-bus-messaging/service-bus-messaging-overview.md)de Serviço   e Contentor de Armazenamento [Azure.](../storage/blobs/storage-blobs-introduction.md)

Este artigo acompanha-o através do processo utilizando o [**Azure CLI**](/cli/azure/what-is-azure-cli).

Aqui estão os passos que estão cobertos neste artigo: 

1. Crie uma instância Azure Digital Twins com uma identidade atribuída ao sistema ou permita a identidade atribuída ao sistema numa instância Azure Digital Twins existente. 
1. Adicione um papel ou papéis adequados à identidade. Por exemplo, atribua o papel **de remetente de dados do Azure Event Hub** à identidade se o ponto final for o Event Hub ou o papel de **Remetente de dados de autocarros do serviço Azure** se o ponto final for o Service Bus.
1. Crie um ponto final em Azure Digital Twins que seja capaz de usar identidades atribuídas ao sistema para autenticação.

## <a name="enable-system-managed-identities-for-an-instance"></a>Permitir identidades geridas pelo sistema, por exemplo 

Quando ativa uma identidade atribuída ao sistema na sua instância Azure Digital Twins, a Azure cria automaticamente uma identidade para ela no [Azure Ative Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md). Essa identidade pode então ser usada para autenticar os pontos finais da Azure Digital Twins para reencaminhamento de eventos.

Pode ativar identidades geridas pelo sistema para uma instância Azure Digital Twins **como parte da configuração inicial do caso**, ou **capacitá-la mais tarde num caso que já existe**.

Qualquer um destes métodos de criação dará as mesmas opções de configuração e o mesmo resultado final para o seu exemplo. Esta secção descreve como fazer as duas coisas.

### <a name="add-a-system-managed-identity-during-instance-creation"></a>Adicione uma identidade gerida pelo sistema durante a criação de exemplos

Nesta secção, você vai aprender como ativar uma identidade gerida pelo sistema em um caso Azure Digital Twins que está atualmente a ser criado. 

Isto é feito adicionando um `--assign-identity` parâmetro ao comando que é usado para criar o `az dt create` caso. (Para obter mais informações sobre este comando, consulte a sua [documentação de referência](/cli/azure/ext/azure-iot/dt#ext_azure_iot_az_dt_create) ou as [instruções gerais para a criação de um exemplo de Gémeos Digitais Azure).](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)

Para criar um caso com uma identidade gerida pelo sistema, adicione o  `--assign-identity` parâmetro como este:

```azurecli-interactive
az dt create -n {new_instance_name} -g {resource_group} --assign-identity
```

### <a name="add-a-system-managed-identity-to-an-existing-instance"></a>Adicione uma identidade gerida pelo sistema a uma instância existente

Nesta secção, irá adicionar uma identidade gerida pelo sistema a uma instância Azure Digital Twins que já existe.

Isto também é feito com o `az dt create` comando e `--assign-identity` o parâmetro. Em vez de fornecer um novo nome de um caso para criar, você pode fornecer o nome de um caso que já existe para atualizar o valor de `--assign-identity` para esse caso.

O comando para **permitir** a identidade gerida é o mesmo que o comando para criar uma instância com uma identidade gerida pelo sistema. Tudo o que muda é o valor do parâmetro do nome da instância:

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --assign-identity
```

Para **desativar a** identidade gerida num caso em que esteja ativada atualmente, utilize o seguinte comando similar para definir `--assign-identity` para `false` .

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --assign-identity false
```

## <a name="assign-azure-roles-to-the-identity"></a>Atribuir funções de Azure à identidade 

Uma vez criada uma identidade atribuída ao sistema para a sua instância Azure Digital Twins, terá de atribuí-la a funções apropriadas para autenticar com diferentes tipos de [pontos finais](concepts-route-events.md) para encaminhar eventos para destinos apoiados. Esta secção descreve as opções de função e como atribuí-las à identidade atribuída pelo sistema.

>[!NOTE]
> Este é um passo importante - sem ele, a identidade não será capaz de aceder aos seus pontos finais e os eventos não serão entregues.

### <a name="supported-destinations-and-azure-roles"></a>Destinos apoiados e funções de Azure 

Aqui estão as funções mínimas que uma identidade precisa para aceder a um ponto final, dependendo do tipo de destino. As funções com permissões mais elevadas (como funções de Data Owner) também funcionarão.

| Destino | Função do Azure |
| --- | --- |
| Azure Event Hubs | Ender de dados do Azure Event Hubs |
| Service Bus do Azure | Remetente de dados de ônibus de serviço Azure |
| Recipiente de armazenamento Azure | Contribuinte de Dados do Armazenamento de Blobs |

Para mais informações sobre pontos finais, rotas e tipos de destinos suportados para encaminhamento em Azure Digital Twins, consulte [*Concepts: Rotas de eventos*](concepts-route-events.md).

### <a name="assign-the-role"></a>Atribuir o papel

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

Pode adicionar o `--scopes` parâmetro ao comando de forma a atribuir a identidade a um ou mais `az dt create` âmbitos com uma função especificada. Isto pode ser usado quando primeiro cria o caso, ou mais tarde passando em nome de um caso que já existe.

Aqui está um exemplo que cria um caso com uma identidade gerida pelo sistema, e atribui a essa identidade um papel personalizado chamado `MyCustomRole` num centro de eventos.

```azurecli-interactive
az dt create -n {instance_name} -g {resource_group} --assign-identity --scopes "/subscriptions/<subscription ID>/resourceGroups/<resource_group>/providers/Microsoft.EventHub/namespaces/<Event_Hubs_namespace>/eventhubs/<event_hub_name>" --role MyCustomRole
```

Para mais exemplos de atribuições de funções com este comando, consulte o [ **az dt criar** documentação de referência](/cli/azure/ext/azure-iot/dt#ext_azure_iot_az_dt_create).

Em alternativa, também pode utilizar o grupo de comando [**de atribuição de funções az**](/cli/azure/role/assignment) para criar e gerir funções. Isto pode ser usado para suportar cenários adicionais onde você não quer agrupar a atribuição de funções com o comando criar.

## <a name="create-an-endpoint-with-identity-based-authentication"></a>Criar um ponto final com autenticação baseada na identidade

Depois de configurar uma identidade gerida pelo sistema para a sua instância Azure Digital Twins e atribuindo-lhe o papel apropriado, pode criar [pontos finais](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins) Azure Digital Twins que sejam capazes de usar a identidade para autenticação. Esta opção só está disponível para pontos finais do tipo Event Hub e Service Bus (não é suportado para a Grelha de Eventos).

>[!NOTE]
> Não é possível editar um ponto final que já tenha sido criado com identidade baseada em chaves para mudar para a autenticação baseada na identidade. Deve escolher o tipo de autenticação quando o ponto final for criado pela primeira vez.

Isto é feito adicionando um `--auth-type` parâmetro ao comando que é usado para criar o ponto `az dt endpoint create` final. (Para obter mais informações sobre este comando, consulte a [documentação de referência](/cli/azure/ext/azure-iot/dt/endpoint/create) ou as [instruções gerais para a criação de um ponto final Azure Digital Twins](how-to-manage-routes-apis-cli.md#create-the-endpoint)).

Para criar um ponto final que utilize a autenticação baseada na identidade, especifique o `IdentityBased` tipo de autenticação com o  `--auth-type` parâmetro. O exemplo abaixo ilustra isto para um ponto final do Event Hubs.

```azurecli-interactive
az dt endpoint create eventhub --endpoint-name {endpoint_name} --eventhub-resource-group {eventhub_resource_group} --eventhub-namespace {eventhub_namespace} --eventhub {eventhub_name} --auth-type IdentityBased -n {instance_name}
```

## <a name="considerations-for-disabling-system-managed-identities"></a>Considerações para desativar identidades geridas pelo sistema

Como uma identidade é gerida separadamente dos pontos finais que a utilizam, é importante considerar os efeitos que quaisquer alterações à identidade ou seus papéis podem ter nos pontos finais na sua instância Azure Digital Twins. Se a identidade for desativada, ou se um papel necessário para um ponto final for removido dela, o ponto final pode tornar-se inacessível e o fluxo de eventos será interrompido.

Para continuar a utilizar um ponto final que foi configurado com uma identidade gerida que foi agora desativada, terá de eliminar o ponto final e [reutivá-lo](how-to-manage-routes-apis-cli.md#create-an-endpoint-for-azure-digital-twins) com um tipo de autenticação diferente. Pode levar até uma hora para os eventos retomarem a entrega no ponto final após esta alteração.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre identidades geridas em Azure AD: 
* [*Identidades geridas para os recursos do Azure*](../active-directory/managed-identities-azure-resources/overview.md)