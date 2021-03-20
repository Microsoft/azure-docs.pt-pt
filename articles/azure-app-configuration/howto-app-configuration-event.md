---
title: Utilize a grelha de eventos para notificações de alteração de dados de configuração de aplicações
description: Saiba como utilizar subscrições de eventos de configuração de aplicações do Azure app para enviar eventos de modificação de valor chave para um ponto final web
services: azure-app-configuration
author: AlexandraKemperMS
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: how-to
ms.date: 03/04/2020
ms.author: alkemper
ms.custom: devx-track-azurecli
ms.openlocfilehash: c188a4b7fe8e9223faa1cdeb52ae01ed83b94d84
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99549784"
---
# <a name="use-event-grid-for-app-configuration-data-change-notifications"></a>Utilize a grelha de eventos para notificações de alteração de dados de configuração de aplicações

Neste artigo, aprende a configurar subscrições de eventos de Configuração de Aplicação de Aplicações Azure para enviar eventos de modificação de valor chave para um ponto final web. Os utilizadores da Azure App Configuration podem subscrever eventos emitidos sempre que os valores-chave são modificados. Estes eventos podem desencadear ganchos web, funções Azure, filas de armazenamento Azure ou qualquer outro manipulador de eventos que seja suportado pela Azure Event Grid. Normalmente, envia eventos para um ponto final que processa os dados de eventos e efetua ações. No entanto, para simplificar este artigo, vai enviar eventos para uma aplicação Web que recolhe e apresenta as mensagens.

## <a name="prerequisites"></a>Pré-requisitos

- Azure subscription - [crie uma gratuitamente](https://azure.microsoft.com/free/). Pode utilizar opcionalmente a Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o CLI localmente, este artigo requer que esteja a executar a versão mais recente do Azure CLI (2.0.70 ou mais tarde). Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

Se não estiver a utilizar o Cloud Shell, primeiro tem de iniciar sessão com `az login`.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Os tópicos do Event Grid são recursos do Azure e têm de ser colocados num grupo de recursos do Azure. Um grupo de recursos é uma coleção lógica na qual os recursos do Azure são implementados e geridos.

Crie um grupo de recursos com o comando [az group create](/cli/azure/group). 

O exemplo a seguir cria um grupo de recursos nomeado `<resource_group_name>` na localização *westus.*  Substitua `<resource_group_name>` por um nome exclusivo para o seu grupo de recursos.

```azurecli-interactive
az group create --name <resource_group_name> --location westus
```

## <a name="create-an-app-configuration-store"></a>Criar uma loja de configuração de aplicativos

`<appconfig_name>`Substitua-o por um nome único para a sua loja de configuração e `<resource_group_name>` pelo grupo de recursos que criou anteriormente. O nome deve ser único porque é usado como um nome DNS.

```azurecli-interactive
az appconfig create \
  --name <appconfig_name> \
  --location westus \
  --resource-group <resource_group_name> \
  --sku free
```

## <a name="create-a-message-endpoint"></a>Criar um ponto final de mensagem

Antes de subscrever o tópico, vamos criar o ponto final para a mensagem de evento. Normalmente, o ponto final executa as ações com base nos dados do evento. Para simplificar este início rápido, vai implementar uma [aplicação Web pré-criada](https://github.com/Azure-Samples/azure-event-grid-viewer) para apresentar as mensagens de evento. A solução implementada inclui um plano do Serviço de Aplicações, uma aplicação Web do Serviço de Aplicações e o código de origem do GitHub.

Substitua `<your-site-name>` por um nome exclusivo para a aplicação Web. O nome da aplicação Web deve ser exclusivo, porque faz parte da entrada DNS.

```azurecli-interactive
$sitename=<your-site-name>

az deployment group create \
  --resource-group <resource_group_name> \
  --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
  --parameters siteName=$sitename hostingPlanName=viewerhost
```

A implementação pode demorar alguns minutos. Após a implementação ter sido concluída com êxito, verifique a aplicação Web para verificar se está em execução. Num browser, navegue para: `https://<your-site-name>.azurewebsites.net`

Deverá ver o site sem mensagens atualmente apresentadas.

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-app-configuration-store"></a>Subscreva a sua loja de Configuração de Aplicações

Subscreva um tópico para comunicar ao Event Grid os eventos que pretende controlar e para onde enviar esses eventos. O exemplo a seguir subscreve a Configuração de Aplicações que criou e passa o URL da sua aplicação web como ponto final para notificação de eventos. Substitua `<event_subscription_name>` por um nome para a subscrição de eventos. Para `<resource_group_name>` e `<appconfig_name>`, utilize o valor que criou anteriormente.

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

## <a name="trigger-an-app-configuration-event"></a>Desencadear um evento de configuração de aplicativos

Agora, vamos acionar um evento para ver como o Event Grid distribui a mensagem para o ponto final. Crie um valor-chave utilizando o `<appconfig_name>` anterior.

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

## <a name="clean-up-resources"></a>Limpar os recursos
Se pretender continuar a trabalhar com esta Configuração de Aplicações e subscrição de eventos, não limpe os recursos criados neste artigo. Se não quiser continuar, utilize o comando seguinte para eliminar os recursos que criou neste artigo.

Substitua `<resource_group_name>` pelo grupo de recursos que criou acima.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Passos seguintes

Agora que sabe como criar tópicos e subscrições de eventos, saiba mais sobre eventos de valor-chave e o que a Grade de Eventos pode ajudá-lo a fazer:

- [Reagir a eventos Key-Value](concept-app-configuration-event.md)
- [Sobre o Event Grid](../event-grid/overview.md)
- [Manipuladores de grelha de eventos Azure](../event-grid/event-handlers.md)
