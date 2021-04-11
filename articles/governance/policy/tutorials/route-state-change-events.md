---
title: 'Tutorial: Rota política estado mudar eventos para Grade de Eventos com Azure CLI'
description: Neste tutorial, você configura a Grade de Eventos para ouvir eventos de mudança de estado de política e chamar um webhook.
ms.date: 03/29/2021
ms.topic: tutorial
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1fe87e4fd3349df7d8f5d57b2b2d95f95ed3fba8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105735548"
---
# <a name="tutorial-route-policy-state-change-events-to-event-grid-with-azure-cli"></a>Tutorial: Rota política estado mudar eventos para Grade de Eventos com Azure CLI

Neste artigo, você aprende a configurar subscrições de eventos da Azure Policy para enviar eventos de mudança de estado de política para um ponto final web. Os utilizadores da Azure Policy podem subscrever eventos emitidos quando ocorrem alterações de estado de política nos recursos. Estes eventos podem desencadear ganchos web, [funções Azure,](../../../azure-functions/index.yml) [Filas de Armazenamento Azure,](../../../storage/queues/index.yml)ou qualquer outro manipulador de eventos que seja suportado pela [Azure Event Grid](../../../event-grid/index.yml). Normalmente, envia eventos para um ponto final que processa os dados de eventos e efetua ações. No entanto, para simplificar este tutorial, envia os eventos para uma aplicação web que recolhe e exibe as mensagens.

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

- Este arranque rápido requer que execute a versão 2.0.76 do Azure CLI ou posterior. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

- Mesmo que tenha usado anteriormente a Azure Policy ou a Event Grid, re-registre os respetivos fornecedores de recursos:

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Provider register: Register the Azure Event Grid provider
  az provider register --namespace Microsoft.EventGrid
  ```

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Os tópicos do Event Grid são recursos do Azure e têm de ser colocados num grupo de recursos do Azure. Um grupo de recursos é uma coleção lógica na qual os recursos do Azure são implementados e geridos.

Crie um grupo de recursos com o comando [az group create](/cli/azure/group). 

O exemplo a seguir cria um grupo de recursos nomeado `<resource_group_name>` na localização _westus._ Substitua `<resource_group_name>` por um nome exclusivo para o seu grupo de recursos.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az group create --name <resource_group_name> --location westus
```

## <a name="create-an-event-grid-system-topic"></a>Criar um tópico de sistema de grelha de eventos

Agora que temos um grupo de recursos, criamos um [tópico de sistema.](../../../event-grid/system-topics.md) Um tópico de sistema em Event Grid representa um ou mais eventos publicados pelos serviços da Azure, tais como Azure Policy e Azure Event Hubs. Este tópico do sistema utiliza o `Microsoft.PolicyInsights.PolicyStates` tipo de tópico para alterações de estado da Política Azure. Substitua `<SubscriptionID>` no parâmetro de **âmbito** pelo ID da sua subscrição e no parâmetro `<resource_group_name>` de grupo de recursos pelo grupo **de** recursos anteriormente criado.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az eventgrid system-topic create --name PolicyStateChanges --location global --topic-type Microsoft.PolicyInsights.PolicyStates --source "/subscriptions/<SubscriptionID>" --resource-group "<resource_group_name>"
```

## <a name="create-a-message-endpoint"></a>Criar um ponto final de mensagem

Antes de subscrever o tópico, vamos criar o ponto final para a mensagem de evento. Normalmente, o ponto final executa as ações com base nos dados do evento. Para simplificar este início rápido, vai implementar uma [aplicação Web pré-criada](https://github.com/Azure-Samples/azure-event-grid-viewer) para apresentar as mensagens de evento. A solução implementada inclui um plano do Serviço de Aplicações, uma aplicação Web do Serviço de Aplicações e o código de origem do GitHub.

Substitua `<your-site-name>` por um nome exclusivo para a aplicação Web. O nome da aplicação Web deve ser exclusivo, porque faz parte da entrada DNS.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az deployment group create \
  --resource-group <resource_group_name> \
  --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
  --parameters siteName=<your-site-name> hostingPlanName=viewerhost
```

A implementação pode demorar alguns minutos. Após a implementação ter sido concluída com êxito, verifique a aplicação Web para verificar se está em execução. Num browser, navegue para: `https://<your-site-name>.azurewebsites.net`

Deverá ver o site sem mensagens atualmente apresentadas.

## <a name="subscribe-to-the-system-topic"></a>Subscreva o tópico do sistema

Subscreva um tópico para comunicar ao Event Grid os eventos que pretende controlar e para onde enviar esses eventos. O exemplo a seguir subscreve o tópico do sistema que criou e passa o URL da sua aplicação web como ponto final para receber notificações de eventos. Substitua `<event_subscription_name>` por um nome para a subscrição de eventos. Para `<resource_group_name>` e `<your-site-name>`, utilize o valor que criou anteriormente.

O ponto final para a aplicação Web tem de incluir o sufixo `/api/updates/`.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Create the subscription
az eventgrid system-topic event-subscription create \
  --name <event_subscription_name> \
  --resource-group <resource_group_name> \
  --system-topic-name PolicyStateChanges \
  --endpoint https://<your-site-name>.azurewebsites.net/api/updates
```

Verifique a aplicação Web novamente e repare que um evento de validação de subscrição foi enviado para a mesma. Selecione o ícone do olho para expandir os dados do evento. O Event Grid envia o evento de validação para que o ponto final possa verificar que pretende receber dados de eventos. A aplicação Web inclui código para validar a subscrição.

:::image type="content" source="../media/route-state-change-events/view-subscription-event.png" alt-text="Screenshot do evento de validação de subscrição da Grade de Eventos na aplicação web pré-construída.":::

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

Neste arranque rápido, cria-se uma atribuição de política e atribui-se uma etiqueta na definição **de grupos de recursos.** Esta definição de política identifica grupos de recursos que estão a faltar à etiqueta configurada durante a atribuição de políticas.

Executar o seguinte comando para criar uma atribuição de política no âmbito do grupo de recursos que criou para manter o tópico da grelha de eventos:

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az policy assignment create --name 'requiredtags-events' --display-name 'Require tag on RG' --scope '<ResourceGroupScope>' --policy '<policy definition ID>' --params '{ "tagName": { "value": "EventTest" } }'
```

O comando anterior utiliza as seguintes informações:

- **Nome** – O nome real da atribuição. Para este exemplo, foram _utilizados eventos de etiquetas necessárias._
- **DisplayName** – O nome da atribuição de política a apresentar. Neste caso, está a utilizar _a etiqueta Requerer em RG_.
- **Âmbito** – Um âmbito determina que recursos ou agrupamento de recursos em que a atribuição de política é imposta. Pode ir desde uma subscrição a grupos de recursos. Não se esqueça de substituir &lt;âmbito&gt; pelo nome do seu grupo de recursos. O formato para um âmbito de grupo de recursos é `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>` .
- **Política** – O ID de definição de política, com base no qual está a utilizar para criar a atribuição. Neste caso, é o ID da definição de política _Requerer uma etiqueta em grupos de recursos_. Para obter o ID de definição de política, execute este comando: `az policy definition list --query "[?displayName=='Require a tag on resource groups']"`

Depois de criar a atribuição de política, aguarde que uma notificação de evento **Microsoft.PolicyInsights.PolicyStateCreated** apareça na aplicação web. O grupo de recursos que criamos mostra um `data.complianceState` valor de _NonCompliant_ para iniciar.

:::image type="content" source="../media/route-state-change-events/view-policystatecreated-event.png" alt-text="Screenshot da subscrição de Event Grid Policy State Criado evento para o grupo de recursos na aplicação web pré-construída.":::

> [!NOTE]
> Se o grupo de recursos herdar outras atribuições políticas da hierarquia do grupo de subscrição ou gestão, os eventos para cada um deles também são exibidos. Confirme que o evento é para a atribuição neste tutorial avaliando o `data.policyDefinitionId` imóvel.

## <a name="trigger-a-change-on-the-resource-group"></a>Desencadear uma mudança no grupo de recursos

Para tornar o grupo de recursos conforme, é necessária uma etiqueta com o nome **EventTest.** Adicione a etiqueta ao grupo de recursos com o seguinte comando substituindo `<SubscriptionID>` pelo seu ID de subscrição e `<ResourceGroup>` com o nome do grupo de recursos:

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az tag create --resource-id '/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>' --tags EventTest=true
```

Depois de adicionar a etiqueta necessária ao grupo de recursos, aguarde que uma notificação de evento **Microsoft.PolicyInsights.PolicyStateChanged** apareça na aplicação web. Expanda o evento e o `data.complianceState` valor agora mostra _compatível._

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender continuar a trabalhar com esta aplicação web e com a subscrição do evento Azure Policy, não limpe os recursos criados neste artigo. Se não pretende continuar, use o seguinte comando para apagar os recursos criados neste artigo.

Substitua `<resource_group_name>` pelo grupo de recursos que criou acima.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Passos seguintes

Agora que sabe como criar tópicos e subscrições de eventos para a Azure Policy, saiba mais sobre eventos de mudança de estado de política e o que a Grade de Eventos pode ajudá-lo a fazer:

- [Reagir aos acontecimentos de mudança de estado da Política Azure](../concepts/event-overview.md)
- [Detalhes do esquema da política Azure para a Grelha de Eventos](../../../event-grid/event-schema-policy.md)
- [Sobre o Event Grid](../../../event-grid/overview.md)