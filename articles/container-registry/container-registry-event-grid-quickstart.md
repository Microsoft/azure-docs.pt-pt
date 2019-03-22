---
title: Início rápido - eventos de registo de contentor do Azure de envio para o Event Grid
description: Neste início rápido, ativar eventos do Event Grid para o seu registo de contentor, em seguida, enviar push de imagem de contentor e eliminar eventos para um aplicativo de exemplo.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 08/23/2018
ms.author: danlep
ms.custom: seodec18
ms.openlocfilehash: f5c075942a29968ea57c684cd817e578df951989
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58119744"
---
# <a name="quickstart-send-events-from-private-container-registry-to-event-grid"></a>Início rápido: Enviar eventos de registo de contentor privado para o Event Grid

O Azure Event Grid é um serviço de encaminhamento de eventos totalmente gerido que fornece o consumo de eventos uniforme com um publicar-subscrever o modelo. Neste início rápido, vai utilizar a CLI do Azure para criar um registo de contentor, assinar eventos de registo, em seguida, implementar uma aplicação web de exemplo para receber os eventos. Por fim, disparar a imagem de contentor `push` e `delete` eventos e ver o payload do evento no aplicativo de exemplo.

Depois de concluir os passos neste artigo, eventos enviados a partir do registo de contentor para o Event Grid são apresentadas na aplicação web de exemplo:

![Compor a aplicação de web de exemplo com três eventos recebidos de navegador da Web][sample-app-01]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita][azure-account] antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Os comandos da CLI do Azure neste artigo são formatados para o **Bash** shell. Se estiver a utilizar um shell diferente, como o PowerShell ou da linha de comandos, terá de ajustar os caracteres de continuação de linha ou linhas de atribuição de variáveis em conformidade. Este artigo utiliza as variáveis para minimizar a quantidade de comando de edição necessárias.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contentor lógico no qual implementar e gerir recursos do Azure. O seguinte procedimento [criar grupo az] [ az-group-create] comando cria um grupo de recursos com o nome *myResourceGroup* no *eastus* região. Se pretender utilizar um nome diferente para o grupo de recursos, defina `RESOURCE_GROUP_NAME` para um valor diferente.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-container-registry"></a>Criar um registo de contentores

Em seguida, implemente um registo de contentores no grupo de recursos com os seguintes comandos. Antes de executar o [criar az acr] [ az-acr-create] comando, defina `ACR_NAME` para um nome para o seu registo. O nome tem de ser exclusivo no Azure e está restringido para 5 a 50 carateres alfanuméricos.

```azurecli-interactive
ACR_NAME=<acrName>

az acr create --resource-group $RESOURCE_GROUP_NAME --name $ACR_NAME --sku Basic
```

Quando o registo tiver sido criado, a CLI do Azure devolve resultados semelhantes ao seguinte:

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

## <a name="create-an-event-endpoint"></a>Criar um ponto de extremidade do evento

Nesta secção, vai utilizar um modelo do Resource Manager, localizado num repositório do GitHub para implementar uma aplicação de web de exemplo precompilados no App Service do Azure. Mais tarde, subscrever a eventos do Event Grid do seu registo e especifique esta aplicação como o ponto final ao qual os eventos são enviados.

Para implementar a aplicação de exemplo, defina `SITE_NAME` para um nome exclusivo para a sua aplicação web e execute os seguintes comandos. O nome do site tem de ser exclusivo no Azure porque ele faz parte do nome de domínio completamente qualificado (FQDN) da aplicação web. Numa seção posterior, navegue para o FQDN da aplicação num browser para ver eventos do seu registo.

```azurecli-interactive
SITE_NAME=<your-site-name>

az group deployment create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

Assim que a implementação foi concluída com êxito (pode demorar alguns minutos), abra um browser e navegue para a aplicação web para garantir que ela está em execução:

`http://<your-site-name>.azurewebsites.net`

Deverá ver a aplicação de exemplo processada com nenhuma mensagem de evento apresentada:

![Web browser a mostrar exemplo de aplicação web com nenhuma eventos apresentados][sample-app-02]

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Subscrever eventos de registo

Na grelha de eventos, se inscreve para um *tópico* para dizer a ele os eventos que pretende controlar e onde enviá-los. O seguinte procedimento [criar subscrição de evento do eventgrid az] [ az-eventgrid-event-subscription-create] comando subscreve o registo de contentor que criou e especifica o URL da sua aplicação web como o ponto final para o qual ele deve enviar eventos. As variáveis de ambiente preenchido nas secções anteriores são reutilizadas aqui, portanto, não edições são necessárias.

```azurecli-interactive
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-acr \
    --source-resource-id $ACR_REGISTRY_ID \
    --endpoint $APP_ENDPOINT
```

Quando a subscrição estiver concluída, deve ver um resultado semelhante ao seguinte:

```JSON
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

## <a name="trigger-registry-events"></a>Eventos de registo do acionador

Agora que a aplicação de exemplo está ativo e em execução e tive subscrito para o seu registo com o Event Grid, está pronto para gerar alguns eventos. Nesta secção, vai utilizar tarefas do ACR para criar e enviar uma imagem de contentor para o seu registo. Tarefas de ACR é um recurso do Azure Container Registry permite-lhe criar imagens de contentor na cloud, sem a necessidade do motor do Docker instalado no seu computador local.

### <a name="build-and-push-image"></a>Criar e emitir imagem

Execute o seguinte comando da CLI do Azure para criar uma imagem de contentor com o conteúdo de um repositório do GitHub. Por predefinição, as tarefas de ACR automaticamente envia por push uma imagem criada com êxito para o seu registo, o que gera o `ImagePushed` eventos.

```azurecli-interactive
az acr build --registry $ACR_NAME --image myimage:v1 -f Dockerfile https://github.com/Azure-Samples/acr-build-helloworld-node.git
```

Deverá ver um resultado semelhante ao seguinte enquanto as tarefas de ACR cria e, em seguida, envia sua imagem. A seguinte saída de exemplo foi truncada para fins de brevidade.

```console
$ az acr build -r $ACR_NAME --image myimage:v1 -f Dockerfile https://github.com/Azure-Samples/acr-build-helloworld-node.git
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

Para verificar se a imagem criada está no seu registo, execute o seguinte comando para ver as etiquetas no repositório "myimage":

```azurecli-interactive
az acr repository show-tags --name $ACR_NAME --repository myimage
```

A etiqueta "v1" da imagem criou deve aparecer na saída, semelhante ao seguinte:

```console
$ az acr repository show-tags --name $ACR_NAME --repository myimage
[
  "v1"
]
```

### <a name="delete-the-image"></a>Eliminar a imagem

Agora, gerar uma `ImageDeleted` eventos ao eliminar a imagem com o [eliminação de repositório az acr] [ az-acr-repository-delete] comando:

```azurecli-interactive
az acr repository delete --name $ACR_NAME --image myimage:v1
```

Deverá ver um resultado semelhante ao seguinte, solicitar a confirmação de eliminar o manifesto e imagens associadas:

```console
$ az acr repository delete --name $ACR_NAME --image myimage:v1
This operation will delete the manifest 'sha256:f15fa9d0a69081ba93eee308b0e475a54fac9c682196721e294b2bc20ab23a1b' and all the following images: 'myimage:v1'.
Are you sure you want to continue? (y/n): y
```

## <a name="view-registry-events"></a>Ver eventos de registo

Agora enviada por push uma imagem para o seu registo e, em seguida, eliminado. Navegue para a aplicação web do Visualizador de grelha de eventos, e verá ambos `ImageDeleted` e `ImagePushed` eventos. Também poderá ver um evento de validação de assinatura gerado ao executar o comando a [subscrever a eventos de registo](#subscribe-to-registry-events) secção.

Captura de ecrã seguinte mostra o exemplo de aplicação com três eventos e o `ImageDeleted` eventos é expandido para mostrar os detalhes.

![Navegador da Web que mostra a aplicação de exemplo com eventos ImagePushed e ImageDeleted][sample-app-03]

Parabéns! Se vir a `ImagePushed` e `ImageDeleted` eventos, o registo está a enviar eventos para o Event Grid e Event Grid é o reencaminhamento esses eventos para o ponto de final de aplicação web.

## <a name="clean-up-resources"></a>Limpar recursos

Quando tiver terminado com os recursos criados neste início rápido, pode eliminá-los tudo com o seguinte comando da CLI do Azure. Quando elimina um grupo de recursos, todos os recursos que nele contidos serão permanentemente eliminados.

**AVISO**: Esta operação é irreversível. Certifique-se de que já não necessita de qualquer um dos recursos no grupo antes de executar o comando.

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME
```

## <a name="event-grid-event-schema"></a>Esquema de eventos do Event Grid

Pode encontrar a referência de esquema de mensagem de eventos do Azure Container Registry na documentação do Event Grid:

[Esquema de eventos do Azure Event Grid para o registo de contentor](../event-grid/event-schema-container-registry.md)

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, implementou um registo de contentor, criou uma imagem com tarefas do ACR, eliminado e, tiver consumido eventos do seu registo do Event Grid com um aplicativo de exemplo. Em seguida, avance para o tutorial de tarefas do ACR para saber mais sobre a criação de imagens de contentor na cloud, incluindo automatizada compilações na atualização da imagem base:

> [!div class="nextstepaction"]
> [Criar imagens de contentor na cloud com tarefas do ACR](container-registry-tutorial-quick-task.md)

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
