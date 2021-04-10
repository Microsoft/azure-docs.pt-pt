---
title: 'Quickstart: Route Azure Cache para eventos Redis para o ponto final da web com Azure CLI'
description: Use a Grelha de Eventos Azure para subscrever a Azure Cache para eventos Redis, desencadear um evento e ver os resultados.
author: curib
ms.author: cauribeg
ms.date: 1/5/2021
ms.topic: quickstart
ms.service: cache
ms.openlocfilehash: 7f33ca0043400962054fabb1aadb1da612fe5426
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99806431"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-azure-cli"></a>Quickstart: Route Azure Cache para eventos Redis para o ponto final da web com Azure CLI

O Azure Event Grid é um serviço de eventos para a cloud. Neste arranque rápido, você usará o CLI Azure para subscrever a Azure Cache para eventos Redis, desencadear um evento e ver os resultados.

Normalmente, envia eventos para um ponto final que processa os dados de eventos e efetua ações. No entanto, para simplificar este arranque rápido, irá enviar eventos para uma aplicação web que irá recolher e exibir as mensagens. Quando completar os passos descritos neste quickstart, verá que os dados do evento foram enviados para a aplicação web.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o CLI localmente, este quickstart requer que esteja a executar a versão mais recente do Azure CLI (2.0.70 ou mais tarde). Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

Se não estiver a utilizar o Cloud Shell, primeiro tem de iniciar sessão com `az login`.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Os tópicos da Grelha de Eventos são implantados como recursos individuais da Azure e devem ser aprovisionados sob um grupo de recursos Azure. Um grupo de recursos é uma coleção lógica na qual os recursos da Azure são implantados e geridos.

Crie um grupo de recursos com o comando [az group create](/cli/azure/group). 

O exemplo seguinte cria um grupo de recursos com o nome `<resource_group_name>` na localização *westcentralus*.  Substitua `<resource_group_name>` por um nome exclusivo para o seu grupo de recursos.

```azurecli-interactive
az group create --name <resource_group_name> --location westcentralus
```

## <a name="create-a-cache-instance"></a>Criar uma instância de cache

```azurecli-interactive
#/bin/bash

# Create a Basic C0 (256 MB) Azure Cache for Redis instance
az redis create --name <cache_name> --resource-group <resource_group_name> --location westcentralus --sku Basic --vm-size C0
```


## <a name="create-a-message-endpoint"></a>Criar um ponto final de mensagem

Antes de subscrever o tópico, vamos criar o ponto final para a mensagem de evento. Normalmente, o ponto final executa as ações com base nos dados do evento. Para simplificar este início rápido, vai implementar uma [aplicação Web pré-criada](https://github.com/Azure-Samples/azure-event-grid-viewer) para apresentar as mensagens de evento. A solução implementada inclui um plano do Serviço de Aplicações, uma aplicação Web do Serviço de Aplicações e o código de origem do GitHub.

Substitua `<your-site-name>` por um nome exclusivo para a aplicação Web. O nome da aplicação Web deve ser exclusivo, porque faz parte da entrada DNS.

```azurecli-interactive
sitename=<your-site-name>

az group deployment create \
  --resource-group <resource_group_name> \
  --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
  --parameters siteName=$sitename hostingPlanName=viewerhost
```

A implementação pode demorar alguns minutos. Após a implementação ter sido concluída com êxito, verifique a aplicação Web para verificar se está em execução. Num browser, navegue para: `https://<your-site-name>.azurewebsites.net`

Deverá ver o site sem mensagens atualmente apresentadas.

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-azure-cache-for-redis-instance"></a>Subscreva o seu Azure Cache para a instância Redis

Neste passo, você subscreverá um tópico para dizer à Grade de Eventos quais eventos você deseja rastrear e para onde enviar esses eventos. O exemplo a seguir subscreve o Azure Cache para a instância Redis que criou e passa o URL da sua aplicação web como ponto final para a notificação do evento. Substitua `<event_subscription_name>` por um nome para a subscrição de eventos. Para `<resource_group_name>` e `<cache_name>`, utilize o valor que criou anteriormente.

O ponto final para a aplicação Web tem de incluir o sufixo `/api/updates/`.

```azurecli-interactive
cacheId=$(az redis show --name <cache_name> --resource-group <resource_group_name> --query id --subscription <subscription_id>)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --source-resource-id $cacheId \
  --name <event_subscription_name> \
  --endpoint $endpoint
```

Verifique a aplicação Web novamente e repare que um evento de validação de subscrição foi enviado para a mesma. Selecione o ícone do olho para expandir os dados do evento. O Event Grid envia o evento de validação para que o ponto final possa verificar que pretende receber dados de eventos. A aplicação Web inclui código para validar a subscrição.

  :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Espectador de grelha de eventos Azure.":::

## <a name="trigger-an-event-from-azure-cache-for-redis"></a>Desencadear um evento de Azure Cache para Redis

Agora, vamos acionar um evento para ver como o Event Grid distribui a mensagem para o ponto final. Vamos exportar os dados armazenados na sua Cache Azure para a instância Redis. Mais uma vez, use os valores para `{cache_name}` `{resource_group_name}` e criou mais cedo.

```azurecli-interactive
az redis export  --ids '/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}' \
    --prefix '<prefix_for_exported_files>' \
    --container '<SAS_url>'  
```

Acionou o evento e o Event Grid enviou a mensagem para o ponto final que configurou ao subscrever. Verifique a aplicação Web para ver o evento que acabámos de enviar.


```json
[{
"id": "e1ceb52d-575c-4ce4-8056-115dec723cff",
  "eventType": "Microsoft.Cache.ExportRDBCompleted",
  "topic": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
  "data": {
    "name": "ExportRDBCompleted",
    "timestamp": "2020-12-10T18:07:54.4937063+00:00",
    "status": "Succeeded"
  },
  "subject": "ExportRDBCompleted",
  "dataversion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-12-10T18:07:54.4937063+00:00"
}]

```

## <a name="clean-up-resources"></a>Limpar os recursos
Se planeia continuar a trabalhar com este Azure Cache para a redis instância e subscrição de eventos, não limpe os recursos criados neste arranque rápido. Se não pretender continuar, utilize o seguinte comando para eliminar os recursos criados neste arranque rápido.

Substitua `<resource_group_name>` pelo grupo de recursos que criou acima.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Passos seguintes

Agora que sabe como criar tópicos e subscrições de eventos, saiba mais sobre a Azure Cache para Eventos Redis e o que a Grade de Eventos pode ajudá-lo a fazer:

- [Reagir a Azure Cache para eventos redis](cache-event-grid.md)
- [Sobre o Event Grid](../event-grid/overview.md)
