---
title: Quickstart - Envie eventos para a Grelha de Eventos
description: Neste arranque rápido, ativa os eventos da Rede de Eventos para o seu registo de contentores, em seguida, envie impulso de imagem de contentor e elimine eventos para uma aplicação de amostra.
ms.topic: article
ms.date: 08/23/2018
ms.custom: seodec18
ms.openlocfilehash: dbeba56820a520e3435eeb0c5c8dbc5aae981241
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78403236"
---
# <a name="quickstart-send-events-from-private-container-registry-to-event-grid"></a>Quickstart: Envie eventos do registo de contentores privados para a Grelha de Eventos

A Azure Event Grid é um serviço de encaminhamento de eventos totalmente gerido que fornece um consumo uniforme de eventos utilizando um modelo de subscrição de publicações. Neste arranque rápido, utiliza o Azure CLI para criar um registo de contentores, subscrever eventos de registo e, em seguida, implementar uma aplicação web de amostra para receber os eventos. Finalmente, você despoleta a imagem do recipiente `push` e `delete` eventos e visualiza a carga útil do evento na aplicação da amostra.

Depois de completar os passos deste artigo, os eventos enviados do seu registo de contentores para a Rede de Eventos aparecem na aplicação web da amostra:

![Web browser renderizando a aplicação web da amostra com três eventos recebidos][sample-app-01]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita][azure-account] antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Os comandos Azure CLI neste artigo são formatados para **a** concha bash. Se estiver a utilizar uma concha diferente, como powerShell ou Command Prompt, poderá ter de ajustar caracteres de continuação da linha ou linhas de atribuição variáveis em conformidade. Este artigo utiliza variáveis para minimizar a quantidade de edição de comando necessária.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos:

Um grupo de recursos Azure é um recipiente lógico no qual você implanta e gere os seus recursos Azure. O seguinte [grupo az criar][az-group-create] comando cria um grupo de recursos chamado *myResourceGroup* na região *oriental.* Se quiser usar um nome diferente para o seu grupo de recursos, detete `RESOURCE_GROUP_NAME` para um valor diferente.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-container-registry"></a>Criar um registo de contentores

Em seguida, insere um registo de contentores no grupo de recursos com os seguintes comandos. Antes de executar o comando [az acr criar,][az-acr-create] coloque `ACR_NAME` para um nome para o seu registo. O nome deve ser único dentro de Azure, e é restrito a 5-50 caracteres alfanuméricos.

```azurecli-interactive
ACR_NAME=<acrName>

az acr create --resource-group $RESOURCE_GROUP_NAME --name $ACR_NAME --sku Basic
```

Uma vez criado o registo, o Azure CLI devolve a saída semelhante à seguinte:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2018-08-16T20:02:46.569509+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myregistry",
  "location": "eastus",
  "loginServer": "myregistry.azurecr.io",
  "name": "myregistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}

```

## <a name="create-an-event-endpoint"></a>Criar um ponto final de evento

Nesta secção, você usa um modelo de Gestor de Recursos localizado num repositório GitHub para implementar uma aplicação web de amostra pré-construída para o Serviço de Aplicações Azure. Mais tarde, subscreve os eventos da Grelha de Eventos do seu registo e especifique esta aplicação como o ponto final para o qual os eventos são enviados.

Para implementar a aplicação de amostra, detete `SITE_NAME` para um nome único para a sua aplicação web e execute os seguintes comandos. O nome do site deve ser único dentro do Azure porque faz parte do nome de domínio totalmente qualificado (FQDN) da aplicação web. Numa secção posterior, navega para o FQDN da aplicação num navegador web para ver os eventos do seu registo.

```azurecli-interactive
SITE_NAME=<your-site-name>

az group deployment create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

Uma vez que a implementação tenha sido bem sucedida (pode demorar alguns minutos), abra um navegador e navegue para a sua aplicação web para se certificar de que está em execução:

`http://<your-site-name>.azurewebsites.net`

Deve ver a aplicação de amostras renderizada sem mensagens de evento apresentadas:

![Web browser mostrando app web da amostra sem eventos apresentados][sample-app-02]

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Subscreva eventos de registo

Em Event Grid, subscreve um *tópico* para lhe dizer quais os eventos que pretende acompanhar e para onde enviá-los. O seguinte [az eventgrid eventgrid cria][az-eventgrid-event-subscription-create] um comando subscrito supor o registo de contentores que criou, e especifica o URL da sua aplicação web como o ponto final para o qual deve enviar eventos. As variáveis ambientais que povoou em secções anteriores são reutilizadas aqui, por isso não são necessárias edições.

```azurecli-interactive
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-acr \
    --source-resource-id $ACR_REGISTRY_ID \
    --endpoint $APP_ENDPOINT
```

Quando a subscrição estiver concluída, deverá ver a saída semelhante à seguinte:

```json
{
  "destination": {
    "endpointBaseUrl": "https://eventgridviewer.azurewebsites.net/api/updates",
    "endpointType": "WebHook",
    "endpointUrl": null
  },
  "filter": {
    "includedEventTypes": [
      "All"
    ],
    "isSubjectCaseSensitive": null,
    "subjectBeginsWith": "",
    "subjectEndsWith": ""
  },
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myregistry/providers/Microsoft.EventGrid/eventSubscriptions/event-sub-acr",
  "labels": null,
  "name": "event-sub-acr",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "topic": "/subscriptions/<Subscription ID>/resourceGroups/myresourcegroup/providers/microsoft.containerregistry/registries/myregistry",
  "type": "Microsoft.EventGrid/eventSubscriptions"
}
```

## <a name="trigger-registry-events"></a>Eventos de registo de gatilho

Agora que a aplicação de amostras está a funcionar e que subscreveste o teu registo com a Event Grid, estás pronto para gerar alguns eventos. Nesta secção, utiliza tarefas ACR para construir e empurrar uma imagem de contentor para o seu registo. A Cr Tasks é uma característica do Registo de Contentores Azure que lhe permite construir imagens de contentores na nuvem, sem precisar do Motor Docker instalado na sua máquina local.

### <a name="build-and-push-image"></a>Construir e empurrar imagem

Execute o seguinte comando Azure CLI para construir uma imagem de recipiente a partir do conteúdo de um repositório GitHub. Por padrão, as Tarefas ACR empurram automaticamente uma imagem construída com sucesso para o seu registo, o que gera o evento `ImagePushed`.

```azurecli-interactive
az acr build --registry $ACR_NAME --image myimage:v1 -f Dockerfile https://github.com/Azure-Samples/acr-build-helloworld-node.git
```

Deve ver a saída semelhante à seguinte enquanto as Tarefas ACR constroem e, em seguida, empurra a sua imagem. A saída da amostra seguinte foi truncada para a brevidade.

```output
Sending build context to ACR...
Queued a build with build ID: aa2
Waiting for build agent...
2018/08/16 22:19:38 Using acb_vol_27a2afa6-27dc-4ae4-9e52-6d6c8b7455b2 as the home volume
2018/08/16 22:19:38 Setting up Docker configuration...
2018/08/16 22:19:39 Successfully set up Docker configuration
2018/08/16 22:19:39 Logging in to registry: myregistry.azurecr.io
2018/08/16 22:19:55 Successfully logged in
Sending build context to Docker daemon  94.72kB
Step 1/5 : FROM node:9-alpine
...
```

Para verificar se a imagem incorporada está no seu registo, execute o seguinte comando para ver as etiquetas no repositório "myimage":

```azurecli-interactive
az acr repository show-tags --name $ACR_NAME --repository myimage
```

A etiqueta "v1" da imagem que construiu deve figurar na saída, semelhante à seguinte:

```output
[
  "v1"
]
```

### <a name="delete-the-image"></a>Apagar a imagem

Agora, gere um evento `ImageDeleted` eliminando a imagem com o [repositório az acr eliminar][az-acr-repository-delete] o comando:

```azurecli-interactive
az acr repository delete --name $ACR_NAME --image myimage:v1
```

Deve ver saída semelhante à seguinte, pedindo confirmação para apagar as imagens manifestas e associadas:

```output
This operation will delete the manifest 'sha256:f15fa9d0a69081ba93eee308b0e475a54fac9c682196721e294b2bc20ab23a1b' and all the following images: 'myimage:v1'.
Are you sure you want to continue? (y/n): 
```

## <a name="view-registry-events"></a>Ver eventos de registo

Agora empurrou uma imagem para o seu registo e depois apagou-a. Navegue para a sua aplicação web Event Grid Viewer, e deverá ver eventos `ImageDeleted` e `ImagePushed`. Pode também ver um evento de validação de subscrição gerado pela execução do comando na secção [Subscrever eventos](#subscribe-to-registry-events) de registo.

A imagem seguinte mostra a aplicação de amostras com os três eventos, e o evento `ImageDeleted` é expandido para mostrar os seus detalhes.

![Navegador web mostrando a aplicação da amostra com eventos ImagePushed e ImageDeleted][sample-app-03]

Parabéns! Se vir os eventos `ImagePushed` e `ImageDeleted`, o seu registo está a enviar eventos para a Event Grid, e a Event Grid está a encaminhar esses eventos para o seu ponto final da sua aplicação web.

## <a name="clean-up-resources"></a>Limpar recursos

Assim que terminar com os recursos que criou neste arranque rápido, pode eliminá-los todos com o seguinte comando Azure CLI. Ao eliminar um grupo de recursos, todos os recursos que contém são permanentemente eliminados.

**AVISO**: Esta operação é irreversível. Certifique-se de que não precisa mais de nenhum dos recursos do grupo antes de dirigir o comando.

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME
```

## <a name="event-grid-event-schema"></a>Esquema de evento sinuoso do evento Da Grelha de Eventos

Pode encontrar a referência de schema de mensagem de evento do registo de contentores Azure na documentação da Grelha de Eventos:

[Esquema de evento da Grelha de Eventos Azure para registo de contentores](../event-grid/event-schema-container-registry.md)

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, implementou um registo de contentores, construiu uma imagem com Tarefas ACR, apagou-a e consumiu os eventos do seu registo a partir da Rede de Eventos com uma aplicação de amostra. Em seguida, passe para o tutorial de Tarefas ACR para saber mais sobre a construção de imagens de contentores na nuvem, incluindo construções automatizadas na atualização de imagem base:

> [!div class="nextstepaction"]
> [Construa imagens de contentores na nuvem com tarefas ACR](container-registry-tutorial-quick-task.md)

<!-- IMAGES -->
[sample-app-01]: ./media/container-registry-event-grid-quickstart/sample-app-01.png
[sample-app-02]: ./media/container-registry-event-grid-quickstart/sample-app-02-no-events.png
[sample-app-03]: ./media/container-registry-event-grid-quickstart/sample-app-03-with-events.png

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr/repository
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
