---
title: Redimensione automaticamente os valores-chave das lojas de configuração de aplicações Azure
description: Saiba como configurar uma cópia de segurança automática dos valores-chave entre lojas de Configuração de Aplicações.
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-dotnet, devx-track-azurecli
ms.topic: how-to
ms.date: 04/27/2020
ms.author: avgupta
ms.openlocfilehash: b3e0bcad7beccc31e1772fbb24ffad7f502b8140
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102454248"
---
# <a name="back-up-app-configuration-stores-automatically"></a>Fazer o back up Lojas de Configuração de Aplicativos automaticamente

Neste artigo, você vai aprender a configurar uma cópia de segurança automática de valores-chave de uma loja primária de Configuração de Aplicações Azure para uma loja secundária. A cópia de segurança automática utiliza a integração da Grelha de Eventos Azure com a Configuração da Aplicação. 

Depois de configurar a cópia de segurança automática, a Configuração da Aplicação publicará eventos na Azure Event Grid para quaisquer alterações feitas aos valores-chave numa loja de configuração. A Event Grid suporta uma variedade de serviços Azure a partir dos quais os utilizadores podem subscrever os eventos emitidos sempre que os valores-chave são criados, atualizados ou eliminados.

## <a name="overview"></a>Descrição Geral

Neste artigo, você usará o armazenamento da Fila Azure para receber eventos da Grade de Eventos e usará um gatilho de temporizador de Funções Azure para processar eventos na fila em lotes. 

Quando uma função é desencadeada, com base nos eventos, irá obter os valores mais recentes das teclas que mudaram a partir da loja primária de Configuração de Aplicações e atualizar a loja secundária em conformidade. Esta configuração ajuda a combinar várias alterações que ocorrem num curto período de tempo numa operação de backup, o que evita pedidos excessivos feitos nas suas lojas de Configuração de Aplicações.

![Diagrama que mostra a arquitetura da loja de configuração de aplicações.](./media/config-store-backup-architecture.png)

## <a name="resource-provisioning"></a>Aprovisionamento de recursos

A motivação por trás do backup de lojas de Configuração de aplicações é usar várias lojas de configuração em diferentes regiões do Azure para aumentar a geo-resiliência da sua aplicação. Para isso, as suas lojas primárias e secundárias devem estar em diferentes regiões do Azure. Todos os outros recursos criados neste tutorial podem ser a provisionados em qualquer região à sua escolha. Isto porque, se a região primária estiver em baixa, não haverá nada de novo a apoiar até que a região primária volte a ser acessível.

Neste tutorial, você vai criar uma loja secundária na `centralus` região e todos os outros recursos na `westus` região.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)].

## <a name="prerequisites"></a>Pré-requisitos 

- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) com a carga de trabalho de desenvolvimento Azure.

- [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Este tutorial requer a versão 2.3.1 ou posterior do CLI Azure. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é uma coleção lógica na qual os recursos do Azure são implementados e geridos.

Crie um grupo de recursos com o comando [az group create](/cli/azure/group).

O exemplo seguinte cria um grupo de recursos com o nome `<resource_group_name>` na localização `westus`.  Substitua `<resource_group_name>` por um nome exclusivo para o seu grupo de recursos.

```azurecli-interactive
resourceGroupName="<resource_group_name>"
az group create --name $resourceGroupName --location westus
```

## <a name="create-app-configuration-stores"></a>Criar lojas de configuração de aplicativos

Crie as suas lojas primárias e secundárias de Configuração de Aplicações em diferentes regiões.
Substitua `<primary_appconfig_name>` e `<secondary_appconfig_name>` por nomes únicos para as suas lojas de configuração. Cada nome de loja deve ser único porque é usado como um nome DNS.

```azurecli-interactive
primaryAppConfigName="<primary_appconfig_name>"
secondaryAppConfigName="<secondary_appconfig_name>"
az appconfig create \
  --name $primaryAppConfigName \
  --location westus \
  --resource-group $resourceGroupName\
  --sku standard

az appconfig create \
  --name $secondaryAppConfigName \
  --location centralus \
  --resource-group $resourceGroupName\
  --sku standard
```

## <a name="create-a-queue"></a>Criar uma fila

Crie uma conta de armazenamento e uma fila para receber os eventos publicados pela Event Grid.

```azurecli-interactive
storageName="<unique_storage_name>"
queueName="<queue_name>"
az storage account create -n $storageName -g $resourceGroupName -l westus --sku Standard_LRS
az storage queue create --name $queueName --account-name $storageName --auth-mode login
```

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-app-configuration-store-events"></a>Subscreva os eventos da sua loja de Configuração de Aplicações

Você subscreve estes dois eventos a partir da loja primária de Configuração de Aplicações:

- `Microsoft.AppConfiguration.KeyValueModified`
- `Microsoft.AppConfiguration.KeyValueDeleted`

O seguinte comando cria uma subscrição de Grade de Eventos para os dois eventos enviados para a sua fila. O tipo de ponto final está definido para `storagequeue` , e o ponto final está definido para o ID da fila. `<event_subscription_name>`Substitua-o pelo nome da sua escolha para a subscrição do evento.

```azurecli-interactive
storageId=$(az storage account show --name $storageName --resource-group  $resourceGroupName --query id --output tsv)
queueId="$storageId/queueservices/default/queues/$queueName"
appconfigId=$(az appconfig show --name $primaryAppConfigName --resource-group $resourceGroupName --query id --output tsv)
eventSubscriptionName="<event_subscription_name>"
az eventgrid event-subscription create \
  --source-resource-id $appconfigId \
  --name $eventSubscriptionName \
  --endpoint-type storagequeue \
  --endpoint $queueId \
  --included-event-types Microsoft.AppConfiguration.KeyValueModified Microsoft.AppConfiguration.KeyValueDeleted 
```

## <a name="create-functions-for-handling-events-from-queue-storage"></a>Criar funções para lidar com eventos a partir do armazenamento da fila

### <a name="set-up-with-ready-to-use-functions"></a>Configurar com funções prontas a usar

Neste artigo, você vai trabalhar com funções C# que têm as seguintes propriedades:
- Pilha de tempo de execução .NET Core 3.1
- Azure Functions versão de tempo de execução 3.x
- Função desencadeada pelo temporizador a cada 10 minutos

Para facilitar o seu início ao backup dos seus dados, [testámos e publicámos uma função](https://github.com/Azure/AppConfiguration/tree/master/examples/ConfigurationStoreBackup) que pode utilizar sem escoar quaisquer alterações ao código. Descarregue os ficheiros do projeto e [publique-os na sua própria aplicação de função Azure a partir do Visual Studio](../azure-functions/functions-develop-vs.md#publish-to-azure).

> [!IMPORTANT]
> Não faça alterações nas variáveis ambientais no código que descarregou. Criará as definições de aplicação necessárias na secção seguinte.
>

### <a name="build-your-own-function"></a>Construa a sua própria função

Se o código de amostra fornecido anteriormente não cumprir os seus requisitos, também pode criar a sua própria função. A sua função deve ser capaz de executar as seguintes tarefas para completar a cópia de segurança:
- Leia periodicamente o conteúdo da sua fila para ver se contém notificações da Grade de Eventos. Consulte o [SDK da fila de armazenamento](../storage/queues/storage-quickstart-queues-dotnet.md) para obter detalhes de implementação.
- Se a sua fila contiver [notificações de eventos da Grade de Eventos,](./concept-app-configuration-event.md#event-schema)extraia todas as `<key, label>` informações únicas das mensagens do evento. A combinação de chave e etiqueta é o identificador único para alterações de valor-chave na loja primária.
- Leia todas as definições da loja primária. Atualize apenas as definições na loja secundária que tenham um evento correspondente na fila. Elimine todas as definições da loja secundária que estavam presentes na fila, mas não na loja primária. Pode utilizar o [SDK de Configuração](https://github.com/Azure/AppConfiguration#sdks) de Aplicações para aceder às suas lojas de configuração programáticamente.
- Elimine as mensagens da fila se não houver exceções durante o processamento.
- Implemente o tratamento de erros de acordo com as suas necessidades. Consulte a amostra de código anterior para ver algumas exceções comuns que possa querer manusear.

Para saber mais sobre a criação de uma função, consulte: [Criar uma função em Azure que é desencadeada por um temporizador](../azure-functions/functions-create-scheduled-function.md) e [desenvolver funções Azure utilizando o Visual Studio](../azure-functions/functions-develop-vs.md).


> [!IMPORTANT]
> Use o seu melhor julgamento para escolher o horário do temporizador com base na frequência com que faz alterações na sua loja de configuração primária. Executar a função com demasiada frequência pode acabar por estrangular pedidos para a sua loja.
>


## <a name="create-function-app-settings"></a>Criar configurações de aplicativos de função

Se estiver a utilizar uma função que nós fornecemos, precisa das seguintes definições de aplicação na sua aplicação de função:
- `PrimaryStoreEndpoint`: Ponto final para a loja primária de configuração de aplicações. Um exemplo é `https://{primary_appconfig_name}.azconfig.io`.
- `SecondaryStoreEndpoint`: Ponto final para a loja de configuração de aplicações secundárias. Um exemplo é `https://{secondary_appconfig_name}.azconfig.io`.
- `StorageQueueUri`: URI de fila. Um exemplo é `https://{unique_storage_name}.queue.core.windows.net/{queue_name}`.

O seguinte comando cria as definições de aplicação necessárias na sua aplicação de função. `<function_app_name>`Substitua-o pelo nome da sua aplicação de função.

```azurecli-interactive
functionAppName="<function_app_name>"
primaryStoreEndpoint="https://$primaryAppConfigName.azconfig.io"
secondaryStoreEndpoint="https://$secondaryAppConfigName.azconfig.io"
storageQueueUri="https://$storageName.queue.core.windows.net/$queueName"
az functionapp config appsettings set --name $functionAppName --resource-group $resourceGroupName --settings StorageQueueUri=$storageQueueUri PrimaryStoreEndpoint=$primaryStoreEndpoint SecondaryStoreEndpoint=$secondaryStoreEndpoint
```


## <a name="grant-access-to-the-managed-identity-of-the-function-app"></a>Conceder acesso à identidade gerida da app de função

Utilize o seguinte comando ou o [portal Azure](../app-service/overview-managed-identity.md#add-a-system-assigned-identity) para adicionar uma identidade gerida atribuída ao sistema para a sua aplicação de função.

```azurecli-interactive
az functionapp identity assign --name $functionAppName --resource-group $resourceGroupName
```

> [!NOTE]
> Para realizar a criação de recursos e gestão de funções necessárias, a sua conta necessita de `Owner` permissões no âmbito apropriado (a sua subscrição ou grupo de recursos). Se precisar de assistência com a atribuição de funções, aprenda [a atribuir funções de Azure utilizando o portal Azure](../role-based-access-control/role-assignments-portal.md).

Utilize os seguintes comandos ou o [portal Azure](./howto-integrate-azure-managed-service-identity.md#grant-access-to-app-configuration) para garantir a identidade gerida da sua aplicação de função de acesso às suas lojas de Configuração de Aplicações. Utilize estas funções:
- Atribua o `App Configuration Data Reader` papel na loja primária de configuração de aplicações.
- Atribua o `App Configuration Data Owner` papel na loja de configuração de aplicações secundárias.

```azurecli-interactive
functionPrincipalId=$(az functionapp identity show --name $functionAppName --resource-group  $resourceGroupName --query principalId --output tsv)
primaryAppConfigId=$(az appconfig show -n $primaryAppConfigName --query id --output tsv)
secondaryAppConfigId=$(az appconfig show -n $secondaryAppConfigName --query id --output tsv)

az role assignment create \
    --role "App Configuration Data Reader" \
    --assignee $functionPrincipalId \
    --scope $primaryAppConfigId

az role assignment create \
    --role "App Configuration Data Owner" \
    --assignee $functionPrincipalId \
    --scope $secondaryAppConfigId
```

Utilize o seguinte comando ou o [portal Azure](../storage/common/storage-auth-aad-rbac-portal.md#assign-azure-roles-using-the-azure-portal) para garantir a identidade gerida da sua aplicação de função acesso à sua fila. Atribua o `Storage Queue Data Contributor` papel na fila.

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee $functionPrincipalId \
    --scope $queueId
```

## <a name="trigger-an-app-configuration-event"></a>Desencadear um evento de configuração de aplicativos

Para testar que tudo funciona, pode criar, atualizar ou eliminar um valor-chave da loja primária. Deverá ver automaticamente esta alteração na loja secundária alguns segundos depois de o temporizador acionar as Funções Azure.

```azurecli-interactive
az appconfig kv set --name $primaryAppConfigName --key Foo --value Bar --yes
```

Desencadeou o evento. Dentro de momentos, a Event Grid enviará a notificação do evento para a sua fila. *Após o próximo funcionamento programado da sua função,* veja as definições de configuração na sua loja secundária para ver se contém o valor-chave atualizado da loja primária.

> [!NOTE]
> Pode [ativar a sua função manualmente](../azure-functions/functions-manually-run-non-http.md) durante o teste e a resolução de problemas sem esperar pelo gatilho do temporizador programado.

Depois de se certificar de que a função de backup foi bem sucedida, pode ver que a chave está agora presente na sua loja secundária.

```azurecli-interactive
az appconfig kv show --name $secondaryAppConfigName --key Foo
```

```json
{
  "contentType": null,
  "etag": "eVgJugUUuopXnbCxg0dB63PDEJY",
  "key": "Foo",
  "label": null,
  "lastModified": "2020-04-27T23:25:08+00:00",
  "locked": false,
  "tags": {},
  "value": "Bar"
}
```

## <a name="troubleshooting"></a>Resolução de problemas

Se não vir o novo cenário na sua loja secundária:

- Certifique-se de que a função de backup foi ativada *depois de* ter criado a definição na sua loja primária.
- É possível que a Grade de Eventos não tenha conseguido enviar a notificação do evento para a fila a tempo. Verifique se a sua fila ainda contém a notificação do evento da sua loja principal. Se o fizer, volte a ativar a função de reserva.
- Verifique [os registos de funções Azure](../azure-functions/functions-create-scheduled-function.md#test-the-function) para obter quaisquer erros ou avisos.
- Utilize o [portal Azure](../azure-functions/functions-how-to-use-azure-function-app-settings.md#get-started-in-the-azure-portal) para garantir que a aplicação de função Azure contém valores corretos para as definições de aplicação que o Azure Functions está a tentar ler.
- Também pode configurar a monitorização e alerta para funções Azure utilizando [insights de aplicação Azure](../azure-functions/functions-monitoring.md?tabs=cmd). 


## <a name="clean-up-resources"></a>Limpar os recursos
Se pretender continuar a trabalhar com esta Configuração de Aplicações e subscrição de eventos, não limpe os recursos criados neste artigo. Se não pretender continuar, utilize o seguinte comando para eliminar os recursos criados neste artigo.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="next-steps"></a>Passos seguintes

Agora que sabe configurar a cópia de segurança automática dos seus valores-chave, saiba mais sobre como pode aumentar a geo-resiliência da sua aplicação:

- [Resiliência e recuperação após desastre](concept-disaster-recovery.md)
