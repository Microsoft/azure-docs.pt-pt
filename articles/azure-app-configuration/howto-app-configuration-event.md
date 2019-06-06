---
title: Tutorial para configurar a configuração de aplicações do Azure para enviar eventos para um ponto final web | Documentos da Microsoft
description: Neste tutorial, saiba como configurar subscrições de eventos de configuração de aplicações do Azure para enviar eventos de modificação de chave-valor para um ponto final web.
services: azure-app-configuration
documentationcenter: ''
author: jimmyca
manager: yegu
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 2cb9ad28a21842987f8c0f7c75151ab8c7fe6fa0
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735361"
---
# <a name="quickstart-route-azure-app-configuration-events-to-a-web-endpoint-with-azure-cli"></a>Início rápido: Encaminhar eventos de configuração de aplicações do Azure para um ponto final web com a CLI do Azure

Os utilizadores de configuração de aplicações do Azure podem subscrever a eventos que são emitidos sempre que os valores de chave são modificados. Esses eventos podem acionar webhooks, as funções do Azure, filas de armazenamento do Azure ou qualquer outro manipulador de eventos que é suportado pelo [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/event-handlers). Neste artigo, ficará a saber como utilizar a CLI do Azure para subscrever a eventos de configuração de aplicações do Azure.

Normalmente, envia eventos para um ponto final que processa os dados de eventos e efetua ações. No entanto, para simplificar este artigo, vai enviar eventos para uma aplicação Web que recolhe e apresenta as mensagens.

Quando concluir os passos descritos neste artigo, pode ver que os dados do evento foram enviados para a aplicação Web.

![Ver evento da subscrição](./media/quickstarts/event-grid/view-results.png)

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a que está a executar a versão mais recente da CLI do Azure (2.0.24 ou posterior). Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Se não estiver a utilizar o Cloud Shell, primeiro tem de iniciar sessão com `az login`.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Os tópicos do Event Grid são recursos do Azure e têm de ser colocados num grupo de recursos do Azure. Um grupo de recursos é uma coleção lógica na qual os recursos do Azure são implementados e geridos.

Crie um grupo de recursos com o comando [az group create](/cli/azure/group). 

O exemplo seguinte cria um grupo de recursos chamado `<resource_group_name>` no *westus* localização.  Substitua `<resource_group_name>` por um nome exclusivo para o seu grupo de recursos.

```azurecli-interactive
az group create --name <resource_group_name> --location westus
```

## <a name="create-an-app-configuration"></a>Criar uma configuração de aplicações

Substitua `<appconfig_name>` com um nome exclusivo para a sua configuração de aplicação, e `<resource_group_name>` com o grupo de recursos que criou anteriormente. O nome deve ser exclusivo, porque é utilizado como um nome DNS.

```azurecli-interactive
az appconfig create \
  --name <appconfig_name> \
  --location westus \
  --resource-group <resource_group_name>
```

## <a name="create-a-message-endpoint"></a>Criar um ponto final de mensagem

Antes de subscrever o tópico, vamos criar o ponto final para a mensagem de evento. Normalmente, o ponto final executa as ações com base nos dados do evento. Para simplificar este início rápido, vai implementar uma [aplicação Web pré-criada](https://github.com/Azure-Samples/azure-event-grid-viewer) para apresentar as mensagens de evento. A solução implementada inclui um plano do Serviço de Aplicações, uma aplicação Web do Serviço de Aplicações e o código de origem do GitHub.

Substitua `<your-site-name>` por um nome exclusivo para a aplicação Web. O nome da aplicação Web deve ser exclusivo, porque faz parte da entrada DNS.

```azurecli-interactive
$sitename=<your-site-name>

az group deployment create \
  --resource-group <resource_group_name> \
  --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
  --parameters siteName=$sitename hostingPlanName=viewerhost
```

A implementação pode demorar alguns minutos. Após a implementação ter sido concluída com êxito, verifique a aplicação Web para verificar se está em execução. Num browser, navegue para: `https://<your-site-name>.azurewebsites.net`

Deverá ver o site sem mensagens atualmente apresentadas.

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-app-configuration"></a>Subscrever a configuração da aplicação

Subscreva um tópico para comunicar ao Event Grid os eventos que pretende controlar e para onde enviar esses eventos. O exemplo seguinte subscreve à configuração de aplicação que criou e transmite o URL da sua aplicação web como o ponto final para notificação de eventos. Substitua `<event_subscription_name>` por um nome para a subscrição de eventos. Para `<resource_group_name>` e `<appconfig_name>`, utilize o valor que criou anteriormente.

O ponto final para a aplicação Web tem de incluir o sufixo `/api/updates/`.

```azurecli-interactive
appconfigId=$(az appconfig show --name <appconfig_name> --resource-group <resource_group_name> --query id --output tsv)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --resource-id $appconfigId \
  --name <event_subscription_name> \
  --endpoint $endpoint
```

Verifique a aplicação Web novamente e repare que um evento de validação de subscrição foi enviado para a mesma. Selecione o ícone do olho para expandir os dados do evento. O Event Grid envia o evento de validação para que o ponto final possa verificar que pretende receber dados de eventos. A aplicação Web inclui código para validar a subscrição.

![Ver evento da subscrição](./media/quickstarts/event-grid/view-subscription-event.png)

## <a name="trigger-an-app-configuration-event"></a>Acionar um evento de configuração de aplicações

Agora, vamos acionar um evento para ver como o Event Grid distribui a mensagem para o ponto final. Criar uma chave-valor com o `<appconfig_name>` anteriores.

```azurecli-interactive
az appconfig kv set --name <appconfig_name> --key Foo --value Bar --yes
```

Acionou o evento e o Event Grid enviou a mensagem para o ponto final que configurou ao subscrever. Verifique a aplicação Web para ver o evento que acabámos de enviar.

```json
[{
  "id": "deb8e00d-8c64-4b6e-9cab-282259c7674f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/eventDemoGroup/providers/microsoft.appconfiguration/configurationstores/{appconfig-name}",
  "subject": "https://{appconfig-name}.azconfig.io/kv/Foo",
  "data": {
    "key": "Foo",
    "etag": "a1LIDdNEIV6wCnfv3xaip7fMXD3"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T18:59:54Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]

```

## <a name="clean-up-resources"></a>Limpar recursos
Se planeia continuar a trabalhar com esta configuração de aplicações e a subscrição de evento, não limpe os recursos criados neste artigo. Se não quiser continuar, utilize o comando seguinte para eliminar os recursos que criou neste artigo.

Substitua `<resource_group_name>` pelo grupo de recursos que criou acima.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Passos Seguintes

Agora que sabe como criar tópicos e subscrições de eventos, saiba mais sobre eventos de chave-valor e o Event Grid pode ajudá-lo a fazer:

- [Reagir a eventos de chave-valor](concept-app-configuration-event.md)
- [Sobre o Event Grid](../event-grid/overview.md)