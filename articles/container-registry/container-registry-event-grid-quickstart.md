---
title: Início rápido – enviar eventos para a grade de eventos
description: Neste guia de início rápido, você habilita eventos de grade de eventos para o registro de contêiner e envia eventos de envio e exclusão de imagem de contêiner para um aplicativo de exemplo.
ms.topic: article
ms.date: 08/23/2018
ms.custom: seodec18
ms.openlocfilehash: 1ff9572cf8614e3eb5d015a602ca3f878875a0a4
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455340"
---
# <a name="quickstart-send-events-from-private-container-registry-to-event-grid"></a>Início rápido: enviar eventos do registro de contêiner privado para a grade de eventos

A grade de eventos do Azure é um serviço de roteamento de eventos totalmente gerenciado que fornece consumo uniforme de eventos usando um modelo de publicação/assinatura. Neste guia de início rápido, você usa o CLI do Azure para criar um registro de contêiner, assinar eventos de registro e, em seguida, implantar um aplicativo Web de exemplo para receber os eventos. Por fim, você disparará a imagem de contêiner `push` e `delete` eventos e exibirá a carga do evento no aplicativo de exemplo.

Depois de concluir as etapas neste artigo, os eventos enviados do registro de contêiner para a grade de eventos aparecem no aplicativo Web de exemplo:

![Navegador da Web renderizando o aplicativo Web de exemplo com três eventos recebidos][sample-app-01]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita][azure-account] antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Os comandos CLI do Azure neste artigo são formatados para o Shell **bash** . Se você estiver usando um shell diferente, como o PowerShell ou o prompt de comando, talvez seja necessário ajustar os caracteres de continuação de linha ou as linhas de atribuição de variável de acordo. Este artigo usa variáveis para minimizar a quantidade de edição de comandos necessária.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos:

Um grupo de recursos do Azure é um contêiner lógico no qual você implanta e gerencia seus recursos do Azure. O comando [AZ Group Create][az-group-create] a seguir cria um grupo de recursos chamado *MyResource* Group na região *eastus* . Se você quiser usar um nome diferente para seu grupo de recursos, defina `RESOURCE_GROUP_NAME` para um valor diferente.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-container-registry"></a>Criar um registo de contentores

Em seguida, implante um registro de contêiner no grupo de recursos com os comandos a seguir. Antes de executar o comando [AZ ACR Create][az-acr-create] , defina `ACR_NAME` como um nome para o registro. O nome deve ser exclusivo no Azure e é restrito a 5-50 caracteres alfanuméricos.

```azurecli-interactive
ACR_NAME=<acrName>

az acr create --resource-group $RESOURCE_GROUP_NAME --name $ACR_NAME --sku Basic
```

Depois que o registro tiver sido criado, o CLI do Azure retornará uma saída semelhante à seguinte:

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

## <a name="create-an-event-endpoint"></a>Criar um ponto de extremidade de evento

Nesta seção, você usa um modelo do Resource Manager localizado em um repositório GitHub para implantar um aplicativo Web de exemplo pré-compilado para Azure App serviço. Posteriormente, você assina os eventos da grade de eventos do registro e especifica esse aplicativo como o ponto de extremidade para o qual os eventos são enviados.

Para implantar o aplicativo de exemplo, defina `SITE_NAME` como um nome exclusivo para seu aplicativo Web e execute os comandos a seguir. O nome do site deve ser exclusivo no Azure porque ele faz parte do FQDN (nome de domínio totalmente qualificado) do aplicativo Web. Em uma seção posterior, navegue até o FQDN do aplicativo em um navegador da Web para exibir os eventos do registro.

```azurecli-interactive
SITE_NAME=<your-site-name>

az group deployment create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

Depois que a implantação for bem-sucedida (pode levar alguns minutos), abra um navegador e navegue até seu aplicativo Web para verificar se ele está em execução:

`http://<your-site-name>.azurewebsites.net`

Você deve ver o aplicativo de exemplo renderizado sem nenhuma mensagem de evento exibida:

![Navegador da Web mostrando aplicativo Web de exemplo sem eventos exibidos][sample-app-02]

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Assinar eventos do registro

Na grade de eventos, você assina um *tópico* para informar quais eventos você deseja controlar e para onde enviá-los. O comando [AZ eventgrid Event-Subscription Create][az-eventgrid-event-subscription-create] a seguir assina o registro de contêiner que você criou e especifica a URL do seu aplicativo Web como o ponto de extremidade para o qual ele deve enviar eventos. As variáveis de ambiente preenchidas nas seções anteriores são reutilizadas aqui, portanto, nenhuma edição é necessária.

```azurecli-interactive
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-acr \
    --source-resource-id $ACR_REGISTRY_ID \
    --endpoint $APP_ENDPOINT
```

Quando a assinatura for concluída, você deverá ver uma saída semelhante à seguinte:

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

## <a name="trigger-registry-events"></a>Disparar eventos de registro

Agora que o aplicativo de exemplo está em execução e você se inscreveu no registro com a grade de eventos, você está pronto para gerar alguns eventos. Nesta seção, você usará tarefas ACR para criar e enviar por push uma imagem de contêiner para o registro. As tarefas ACR são um recurso do registro de contêiner do Azure que permite que você crie imagens de contêiner na nuvem, sem precisar do mecanismo do Docker instalado no computador local.

### <a name="build-and-push-image"></a>Criar e enviar imagem por push

Execute o comando CLI do Azure a seguir para criar uma imagem de contêiner do conteúdo de um repositório GitHub. Por padrão, as tarefas ACR enviam automaticamente uma imagem criada com êxito para o registro, o que gera o evento `ImagePushed`.

```azurecli-interactive
az acr build --registry $ACR_NAME --image myimage:v1 -f Dockerfile https://github.com/Azure-Samples/acr-build-helloworld-node.git
```

Você deverá ver uma saída semelhante à seguinte, enquanto as tarefas ACR são compiladas e, em seguida, enviam a imagem por push. A seguinte saída de exemplo foi truncada para fins de brevidade.

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

Para verificar se a imagem interna está no registro, execute o seguinte comando para exibir as marcas no repositório "MYIMAGE":

```azurecli-interactive
az acr repository show-tags --name $ACR_NAME --repository myimage
```

A marca "v1" da imagem que você criou deve aparecer na saída, semelhante à seguinte:

```console
$ az acr repository show-tags --name $ACR_NAME --repository myimage
[
  "v1"
]
```

### <a name="delete-the-image"></a>Excluir a imagem

Agora, gere um evento de `ImageDeleted` excluindo a imagem com o comando [AZ ACR Repository Delete][az-acr-repository-delete] :

```azurecli-interactive
az acr repository delete --name $ACR_NAME --image myimage:v1
```

Você deverá ver uma saída semelhante à seguinte, solicitando a confirmação para excluir o manifesto e as imagens associadas:

```console
$ az acr repository delete --name $ACR_NAME --image myimage:v1
This operation will delete the manifest 'sha256:f15fa9d0a69081ba93eee308b0e475a54fac9c682196721e294b2bc20ab23a1b' and all the following images: 'myimage:v1'.
Are you sure you want to continue? (y/n): y
```

## <a name="view-registry-events"></a>Exibir eventos do registro

Agora você enviou por push uma imagem para o registro e a excluiu. Navegue até o aplicativo Web do Visualizador de grade de eventos e você deverá ver os eventos `ImageDeleted` e `ImagePushed`. Você também pode ver um evento de validação de assinatura gerado executando o comando na seção [assinar eventos de registro](#subscribe-to-registry-events) .

A captura de tela a seguir mostra o aplicativo de exemplo com os três eventos, e o `ImageDeleted` evento é expandido para mostrar seus detalhes.

![Navegador da Web mostrando o aplicativo de exemplo com eventos ImagePushed e ImageDeleted][sample-app-03]

Parabéns! Se você vir os eventos `ImagePushed` e `ImageDeleted`, o registro está enviando eventos para a grade de eventos e a grade de eventos está encaminhando esses eventos para o ponto de extremidade do aplicativo Web.

## <a name="clean-up-resources"></a>Limpar recursos

Depois de concluir os recursos criados neste guia de início rápido, você poderá excluí-los com o comando CLI do Azure a seguir. Quando você exclui um grupo de recursos, todos os recursos que ele contém são excluídos permanentemente.

**Aviso**: esta operação é irreversível. Certifique-se de que você não precisa mais de nenhum dos recursos do grupo antes de executar o comando.

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME
```

## <a name="event-grid-event-schema"></a>Esquema de evento da grade de eventos

Você pode encontrar a referência de esquema de mensagem de evento do registro de contêiner do Azure na documentação da grade de eventos:

[Esquema de evento da grade de eventos do Azure para registro de contêiner](../event-grid/event-schema-container-registry.md)

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você implantou um registro de contêiner, criou uma imagem com tarefas ACR, excluiu-a e consumiu os eventos do registro da grade de eventos com um aplicativo de exemplo. Em seguida, vá para o tutorial de tarefas do ACR para saber mais sobre como criar imagens de contêiner na nuvem, incluindo compilações automatizadas na atualização da imagem base:

> [!div class="nextstepaction"]
> [Criar imagens de contêiner na nuvem com tarefas ACR](container-registry-tutorial-quick-task.md)

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
