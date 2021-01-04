---
title: Quickstart - Enviar eventos para a Grade de Eventos
description: Neste arranque rápido, você ativa eventos de Grade de Evento para o seu registo de contentores, em seguida, envie o push de imagem do recipiente e elimine eventos para uma aplicação de amostragem.
ms.topic: article
ms.date: 08/23/2018
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 2d13dd0ec5e50086e674b215d93917d6173d5af9
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/19/2020
ms.locfileid: "97694395"
---
# <a name="quickstart-send-events-from-private-container-registry-to-event-grid"></a>Quickstart: Enviar eventos do registo privado de contentores para a Grade de Eventos

A Azure Event Grid é um serviço de encaminhamento de eventos totalmente gerido que proporciona um consumo uniforme de eventos usando um modelo de subscrição de publicação. Neste arranque rápido, você usa o CLI Azure para criar um registo de contentores, subscrever eventos de registo e, em seguida, implementar uma aplicação web de amostra para receber os eventos. Finalmente, desencadeia a imagem e eventos do contentor `push` e vê a carga útil do evento na `delete` aplicação da amostra.

Depois de completar os passos deste artigo, os eventos enviados do seu registo de contentores para a Grade de Eventos aparecem na aplicação web da amostra:

![Navegador web tornando a aplicação web da amostra com três eventos recebidos][sample-app-01]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Os comandos Azure CLI neste artigo são formatados para a concha **bash.** Se estiver a utilizar uma concha diferente, como PowerShell ou Command Prompt, poderá ter de ajustar caracteres de continuação de linha ou linhas de atribuição variáveis em conformidade. Este artigo utiliza variáveis para minimizar a quantidade de edição de comando necessária.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos Azure é um recipiente lógico no qual você implanta e gere os seus recursos Azure. O seguinte [grupo az cria][az-group-create] um grupo de recursos chamado *myResourceGroup* na região *leste.* Se quiser utilizar um nome diferente para o seu grupo de recursos, desaprotendo `RESOURCE_GROUP_NAME` um valor diferente.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-container-registry"></a>Criar um registo de contentor

Em seguida, inserção de um registo de contentores no grupo de recursos com os seguintes comandos. Antes de executar o [az acr criar][az-acr-create] comando, definir `ACR_NAME` um nome para o seu registo. O nome deve ser único dentro de Azure, e é restrito a 5-50 caracteres alfanuméricos.

```azurecli-interactive
ACR_NAME=<acrName>

az acr create --resource-group $RESOURCE_GROUP_NAME --name $ACR_NAME --sku Basic
```

Uma vez criado o registo, o CLI Azure devolve a saída semelhante à seguinte:

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

## <a name="create-an-event-endpoint"></a>Criar um ponto final do evento

Nesta secção, você usa um modelo de Gestor de Recursos localizado num repositório GitHub para implementar uma aplicação web de amostra pré-construída para o Azure App Service. Mais tarde, subscreve os eventos da Grelha de Eventos do seu registo e especifica esta aplicação como o ponto final para o qual os eventos são enviados.

Para implementar a aplicação da amostra, desloque-se `SITE_NAME` a um nome único para a sua aplicação web e execute os seguintes comandos. O nome do site deve ser único dentro do Azure porque faz parte do nome de domínio totalmente qualificado (FQDN) da aplicação web. Numa secção posterior, você navega para o FQDN da aplicação em um navegador web para ver os eventos do seu registro.

```azurecli-interactive
SITE_NAME=<your-site-name>

az deployment group create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

Uma vez que a implementação tenha sido bem sucedida (pode levar alguns minutos), abra um navegador e navegue na sua aplicação web para se certificar de que está em execução:

`http://<your-site-name>.azurewebsites.net`

Deve ver a aplicação da amostra renderizada sem mensagens de evento apresentadas:

![Navegador web mostrando aplicação web de amostra sem eventos exibidos][sample-app-02]

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Subscreva eventos de registo

Em Event Grid, você subscreve um *tópico* para dizer-lhe quais eventos você quer rastrear, e para onde enviá-los. O seguinte comando [de subscrição de eventos az eventgrid][az-eventgrid-event-subscription-create] subscreve o registo de contentores que criou e especifica o URL da sua aplicação web como o ponto final para o qual deve enviar eventos. As variáveis ambientais que povoou em secções anteriores são reutilizadas aqui, pelo que não são necessárias edições.

```azurecli-interactive
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-acr \
    --source-resource-id $ACR_REGISTRY_ID \
    --endpoint $APP_ENDPOINT
```

Quando a subscrição estiver concluída, deverá ver uma saída semelhante à seguinte:

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

## <a name="trigger-registry-events"></a>Eventos de registo de desencadeamento

Agora que a aplicação da amostra está a funcionar e que subscreveu o seu registo com a Grade de Eventos, está pronto para gerar alguns eventos. Nesta secção, utiliza-se ACR Tasks para construir e empurrar uma imagem de contentor para o seu registo. ACR Tasks é uma característica do Registo de Contentores Azure que lhe permite construir imagens de contentores na nuvem, sem precisar do Motor Docker instalado na sua máquina local.

### <a name="build-and-push-image"></a>Construir e empurrar imagem

Execute o seguinte comando Azure CLI para construir uma imagem de contentor a partir do conteúdo de um repositório GitHub. Por predefinição, a ACR Tasks empurra automaticamente uma imagem construída com sucesso para o seu registo, o que gera o `ImagePushed` evento.

```azurecli-interactive
az acr build --registry $ACR_NAME --image myimage:v1 -f Dockerfile https://github.com/Azure-Samples/acr-build-helloworld-node.git
```

Deve ver a saída semelhante à seguinte enquanto a ACR Tasks constrói e, em seguida, empurra a sua imagem. A seguinte produção de amostra foi truncada para a brevidade.

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

Para verificar se a imagem incorporada está no seu registo, execute o seguinte comando para visualizar as etiquetas no repositório "myimage":

```azurecli-interactive
az acr repository show-tags --name $ACR_NAME --repository myimage
```

A etiqueta "v1" da imagem que construiu deve aparecer na saída, semelhante à seguinte:

```output
[
  "v1"
]
```

### <a name="delete-the-image"></a>Apagar a imagem

Agora, gere um `ImageDeleted` evento eliminando a imagem com o comando [de eliminação do repositório az acr:][az-acr-repository-delete]

```azurecli-interactive
az acr repository delete --name $ACR_NAME --image myimage:v1
```

Deverá ver uma saída semelhante à seguinte, pedindo confirmação para apagar o manifesto e as imagens associadas:

```output
This operation will delete the manifest 'sha256:f15fa9d0a69081ba93eee308b0e475a54fac9c682196721e294b2bc20ab23a1b' and all the following images: 'myimage:v1'.
Are you sure you want to continue? (y/n): 
```

## <a name="view-registry-events"></a>Ver eventos de registo

Agora empurrou uma imagem para o seu registo e depois apagou-a. Navegue para a sua aplicação web Event Grid Viewer, e deverá ver ambos `ImageDeleted` e `ImagePushed` eventos. Poderá também ver um evento de validação de subscrição gerado executando o comando na secção [Descênso para eventos de registo.](#subscribe-to-registry-events)

A imagem que se segue mostra a aplicação da amostra com os três eventos, e o `ImageDeleted` evento é expandido para mostrar os seus detalhes.

![Navegador web mostrando a app da amostra com eventos ImagePushed e ImageDeleted][sample-app-03]

Parabéns! Se vir os `ImagePushed` eventos e `ImageDeleted` eventos, o seu registo está a enviar eventos para a Grade de Eventos, e a Grade de Eventos está a encaminhar esses eventos para o ponto final da sua aplicação web.

## <a name="clean-up-resources"></a>Limpar os recursos

Uma vez terminado os recursos que criou neste arranque rápido, pode eliminá-los todos com o seguinte comando Azure CLI. Quando elimina um grupo de recursos, todos os recursos que contém são permanentemente eliminados.

**AVISO:** Esta operação é irreversível. Certifique-se de que já não precisa de nenhum dos recursos do grupo antes de executar o comando.

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME
```

## <a name="event-grid-event-schema"></a>Esquema de eventos do Event Grid

Pode encontrar a referência de mensagem do registo do registo do contentor Azure na documentação da Grelha de Eventos:

[Esquema de evento de Azure Event Grid para registo de contentores](../event-grid/event-schema-container-registry.md)

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, você implantou um registo de contentores, construiu uma imagem com tarefas ACR, apagou-a e consumiu os eventos do seu registo a partir da Grade de Eventos com uma aplicação de amostragem. Em seguida, passe para o tutorial de tarefas ACR para saber mais sobre a construção de imagens de contentores na nuvem, incluindo construções automatizadas na atualização da imagem base:

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
