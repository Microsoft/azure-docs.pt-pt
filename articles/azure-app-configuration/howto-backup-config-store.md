---
title: Valores-chave de backup automaticamente da loja de configuração de aplicações Azure
description: Saiba como configurar uma cópia de segurança automática dos valores-chave entre lojas de Configuração de Aplicações
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: how-to
ms.date: 04/27/2020
ms.author: avgupta
ms.openlocfilehash: 0b057172c822f893e602d60f77a285f0867cf368
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87367780"
---
# <a name="backup-app-configuration-stores-automatically"></a>Backup App Configuration Stores automaticamente

Neste artigo, você vai aprender a configurar uma cópia de segurança automática de valores-chave de uma loja primária de Configuração de Aplicações para uma loja secundária. Aproveita a integração da Grelha de Eventos Azure com a Configuração de Aplicações. Uma vez configurado, a Configuração de Aplicações publicará eventos na Grade de Eventos para quaisquer alterações feitas aos valores-chave numa loja de configuração. A Event Grid suporta uma variedade de serviços Azure a partir dos quais os utilizadores podem subscrever os eventos emitidos sempre que os valores-chave são criados, atualizados ou eliminados.

## <a name="overview"></a>Descrição geral

Neste tutorial, você usará uma fila de armazenamento Azure para receber eventos da Grade de Eventos e usará um gatilho de temporizador de Funções Azure para processar eventos na Fila de Armazenamento em lotes. Quando acionado, com base nos eventos, a função irá recolher os valores mais recentes das teclas que mudaram a partir da loja primária de Configuração de Aplicações e atualizar a loja secundária em conformidade. Esta configuração ajuda a combinar múltiplas alterações ocorridas num curto período de tempo numa operação de backup e evitar pedidos excessivos feitos nas suas lojas de Configuração de Aplicações.

![Arquitetura de backup de loja de configuração de aplicativos](./media/config-store-backup-architecture.png)

## <a name="resource-provisioning"></a>Aprovisionamento de Recursos

A motivação por trás do backup de lojas de Configuração de aplicações é usar várias lojas de configuração em diferentes regiões do Azure para aumentar a geo-resiliência da sua aplicação. Para isso, as suas lojas primárias e secundárias devem estar em diferentes regiões do Azure. Todos os outros recursos criados neste tutorial podem ser a provisionados em qualquer região à sua escolha. Isto porque, se a região primária estiver em baixo, não haverá nada de novo para apoiar até que a região primária volte a ser acessível.

Neste tutorial, estará a criar uma loja secundária na `centralus` região e todos os outros recursos da `westus` região.

## <a name="prerequisites"></a>Pré-requisitos

- Azure subscription - [crie uma gratuitamente](https://azure.microsoft.com/free/). Pode utilizar opcionalmente a Azure Cloud Shell.
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) com a carga de trabalho de desenvolvimento Azure.
- Faça o download e instale o [.NET Core SDK](https://dotnet.microsoft.com/download).
- Versão mais recente do Azure CLI (2.3.1 ou mais tarde). Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli). Se estiver a utilizar o Azure CLI, tem de iniciar sê-lo `az login` primeiro. Pode utilizar opcionalmente a Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

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
Substitua  `<primary_appconfig_name>` e `<secondary_appconfig_name>` por nomes únicos para as suas lojas de configuração. O nome da loja deve ser único porque é usado como um nome DNS.

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

## <a name="create-azure-storage-queue"></a>Criar fila de armazenamento Azure

Crie uma conta de armazenamento e uma fila de armazenamento para receber os eventos publicados pela Event Grid.

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

O seguinte comando cria uma subscrição de Grade de Evento para os dois eventos enviados para a sua fila de armazenamento, onde o tipo de ponto final está definido `storagequeue` e o ponto final está definido para o ID da fila. `<event_subscription_name>`Substitua-o pelo nome da sua escolha para a subscrição do evento.

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

## <a name="create-azure-functions-for-handling-events-from-storage-queue"></a>Criar funções Azure para lidar com eventos a partir da fila de armazenamento

### <a name="setup-with-ready-to-use-azure-functions"></a>Configuração com funções Azure prontas a usar

Neste tutorial, você estará trabalhando com C# Azure Funções com as seguintes propriedades:
- Pilha de tempo de execução .NET Core 3.1
- Azure Functions versão de tempo de execução 3.x
- Função desencadeada pelo temporizador a cada 10 minutos

Para facilitar o seu início ao backup dos seus dados, testámos e publicámos [Funções Azure](https://github.com/Azure/AppConfiguration/tree/master/examples/ConfigurationStoreBackup) que pode utilizar sem escoar quaisquer alterações ao código. Descarregue os ficheiros do projeto e [publique-os na sua própria App de Função Azure do Visual Studio.](/azure/azure-functions/functions-develop-vs#publish-to-azure)

> [!IMPORTANT]
> Não faça alterações nas variáveis ambientais no código que descarregou. Irá criar as definições de aplicações necessárias na secção seguinte.
>

### <a name="build-your-own-azure-functions"></a>Construa as suas próprias Funções Azure

Se o código de amostra suprido acima não satisfaça os seus requisitos, também pode criar as suas próprias Funções Azure. A sua função deve ser capaz de executar as seguintes tarefas para completar a cópia de segurança:
- Leia periodicamente o conteúdo da sua fila de armazenamento para ver se contém notificações da Grade de Eventos. Consulte o [SDK da fila de armazenamento](/azure/storage/queues/storage-quickstart-queues-dotnet) para obter detalhes de implementação.
- Se a sua fila de armazenamento contiver [notificações de eventos da Grade de Eventos,](/azure/azure-app-configuration/concept-app-configuration-event?branch=pr-en-us-112982#event-schema)extraia todas as chaves <únicas,> de etiquetas a partir de mensagens de evento. A combinação de chaves e etiquetas é o identificador único para alterações de valor-chave na loja primária.
- Leia todas as definições da loja primária. Atualize apenas as definições na loja secundária que tenham um evento correspondente na fila de armazenamento. Elimine todas as definições da loja secundária que estavam presentes na fila de armazenamento, mas não na loja primária. Pode aproveitar o [SDK de configuração da aplicação](https://github.com/Azure/AppConfiguration#sdks) para aceder programaticamente às suas lojas de configuração.
- Elimine as mensagens da fila de armazenamento se não houver exceções durante o processamento.
- Certifique-se de que implementa o manuseamento de erros de acordo com as suas necessidades. Pode consultar a amostra de código acima para ver algumas exceções comuns que possa querer manusear.

Para saber mais sobre a criação de Funções Azure, consulte: [Criar uma função em Azure que é desencadeada por um temporizador](/azure/azure-functions/functions-create-scheduled-function) e [desenvolver funções de Azure utilizando o Visual Studio](/azure/azure-functions/functions-develop-vs).


> [!IMPORTANT]
> Use o seu melhor julgamento para escolher o horário do temporizador com base na frequência com que faz alterações na sua loja primária de config. Lembre-se, executar a função com demasiada frequência pode acabar por estrangular pedidos para a sua loja.
>


## <a name="create-azure-function-app-settings"></a>Criar configurações de App de função Azure

Se estiver a utilizar as Funções Azure que fornecemos, precisa das seguintes definições de aplicação na sua App de Função Azure:
- `PrimaryStoreEndpoint`: Ponto final para a loja primária de configuração de aplicações. Por exemplo, `https://{primary_appconfig_name}.azconfig.io`
- `SecondaryStoreEndpoint`: Ponto final para a loja de configuração de aplicações secundárias. Por exemplo, `https://{secondary_appconfig_name}.azconfig.io`
- `StorageQueueUri`: Fila de armazenamento URI. Por exemplo, `https://{unique_storage_name}.queue.core.windows.net/{queue_name}`

O seguinte comando cria as definições de aplicação necessárias na sua App de Função Azure. `<function_app_name>`Substitua-o pelo nome da sua App de Função Azure.

```azurecli-interactive
functionAppName="<function_app_name>"
primaryStoreEndpoint="https://$primaryAppConfigName.azconfig.io"
secondaryStoreEndpoint="https://$secondaryAppConfigName.azconfig.io"
storageQueueUri="https://$storageName.queue.core.windows.net/$queueName"
az functionapp config appsettings set --name $functionAppName --resource-group $resourceGroupName --settings StorageQueueUri=$storageQueueUri PrimaryStoreEndpoint=$primaryStoreEndpoint SecondaryStoreEndpoint=$secondaryStoreEndpoint
```


## <a name="grant-access-to-the-managed-identity-of-azure-function-app"></a>Conceder acesso à identidade gerida da App de Função Azure

Utilize o seguinte comando ou [portal Azure](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity) para adicionar uma identidade gerida atribuída ao sistema para a sua App de Função Azure.

```azurecli-interactive
az functionapp identity assign --name $functionAppName --resource-group $resourceGroupName
```

> [!NOTE]
> Para realizar a criação de recursos e gestão de funções necessárias, a sua conta necessita de `'Owner'` permissões no âmbito apropriado (a sua subscrição ou grupo de recursos). Se precisar de assistência com a atribuição de funções, aprenda [a adicionar ou remover atribuições de funções Azure utilizando o portal Azure](/azure/role-based-access-control/role-assignments-portal).

Utilize os seguintes comandos ou [portal Azure](/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity#grant-access-to-app-configuration) para garantir a identidade gerida da sua App de Função Azure acesso às suas lojas de Configuração de Aplicações.
- Atribua `App Configuration Data Reader` papel na loja primária de configuração de aplicações.
- Atribuir `App Configuration Data Owner` papel na loja de configuração de aplicações secundárias.

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

Utilize o seguinte comando ou [portal Azure](/azure/storage/common/storage-auth-aad-rbac-portal#assign-rbac-roles-using-the-azure-portal) para garantir a identidade gerida da sua App de Função Azure ao seu Armazenamento. 
- Atribuir `Storage Queue Data Contributor` papel na fila de armazenamento.

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee $functionPrincipalId \
    --scope $queueId
```

## <a name="trigger-an-app-configuration-event"></a>Desencadear um evento de configuração de aplicativos

Para testar que tudo funciona, pode criar/atualizar/eliminar um valor chave da loja primária. Deverá ver automaticamente esta alteração na loja secundária alguns segundos depois de as Funções Azure terem sido ativadas pelo temporizador.

```azurecli-interactive
az appconfig kv set --name $primaryAppConfigName --key Foo --value Bar --yes
```

Desencadeou o evento e, em alguns momentos, a Event Grid enviará a notificação do evento para a sua Fila de Armazenamento Azure. ***Após a próxima execução programada das suas Funções Azure,*** veja as definições de configuração na sua loja secundária para ver se contém o valor de chave atualizado da loja primária.

> [!NOTE]
> Pode [ativar manualmente as suas Funções Azure](/azure/azure-functions/functions-manually-run-non-http) durante os testes e a resolução de problemas sem esperar pelo gatilho do temporizador programado.

Depois de se certificar de que a função de backup funciona com sucesso, pode ver que a chave está agora presente na sua loja secundária.

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

- Certifique-se de que a função de backup foi ativada ***depois de*** ter criado a definição na sua loja primária.
- É possível que a Grade de Eventos não tenha sido capaz de enviar a notificação do evento para a fila de armazenamento a tempo. Verifique se a sua fila de armazenamento ainda contém a notificação do evento da sua loja primária, e se o fizer, volte a ativar a função de backup.
- Verifique [os registos de funções Azure](/azure/azure-functions/functions-create-scheduled-function#test-the-function) para obter quaisquer erros ou avisos.
- Utilize [o portal Azure](/azure/azure-functions/functions-how-to-use-azure-function-app-settings#get-started-in-the-azure-portal) para garantir que a App de Função Azure contém valores corretos para as Definições de Aplicação que as suas Funções Azure estão a tentar ler.
- Também pode configurar a monitorização e alerta para as suas Funções Azure utilizando [insights de aplicação Azure.](/azure/azure-functions/functions-monitoring?tabs=cmd) 


## <a name="clean-up-resources"></a>Limpar os recursos
Se pretender continuar a trabalhar com esta Configuração de Aplicações e subscrição de eventos, não limpe os recursos criados neste artigo. Se não quiser continuar, utilize o comando seguinte para eliminar os recursos que criou neste artigo.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="next-steps"></a>Passos seguintes

Agora que sabe configurar a cópia de segurança automática dos seus valores-chave, saiba mais sobre como pode aumentar a geo-resiliência da sua aplicação:

- [Resiliência e recuperação após desastre](concept-disaster-recovery.md)
