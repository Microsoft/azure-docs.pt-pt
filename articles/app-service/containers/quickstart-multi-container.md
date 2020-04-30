---
title: 'Quickstart: Criar uma aplicação multi-contentor'
description: Inicie-se com aplicações multi-contentores no Azure App Service, implementando a sua primeira aplicação multi-contentores.
keywords: serviço de aplicações azure, web app, linux, docker, compor, multicontentor, multi-contentor, web app para contentores, vários contentores, recipiente, wordpress, azure db para mysql, base de dados de produção com contentores
author: msangapu-msft
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: ab8e416e460546dcf758c83aa8b357139c6dbefb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82085880"
---
# <a name="create-a-multi-container-preview-app-using-a-docker-compose-configuration"></a>Criar uma aplicação multi-contentor (pré-visualização) utilizando uma configuração Docker Compose

> [!NOTE]
> O multi-contentor está em pré-visualização.

A [Aplicação Web para Contentores](app-service-linux-intro.md) proporciona uma forma flexível de utilizar imagens do Docker. Este quickstart mostra como implementar uma aplicação multi-contentor (pré-visualização) para web app para contentores na [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) usando uma configuração Docker Compose.

Este início rápido deverá ser concluído no Cloud Shell, mas também poderá executar estes comandos localmente com a [CLI do Azure](/cli/azure/install-azure-cli) (2.0.32 ou posterior). 

![Aplicação com vários contentores de exemplo na Aplicação Web para Contentores][1]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Transferir o exemplo

Neste início rápido, irá utilizar o ficheiro compose do [Docker](https://docs.docker.com/compose/wordpress/#define-the-project). O ficheiro de configuração pode ser encontrado em [Exemplos do Azure](https://github.com/Azure-Samples/multicontainerwordpress).

[!code-yml[Main](../../../azure-app-service-multi-container/docker-compose-wordpress.yml)]

No Cloud Shell, crie um diretório de início rápido e, em seguida, altere-o.

```bash
mkdir quickstart

cd $HOME/quickstart
```

Em seguida, execute o seguinte comando para clonar o repositório da aplicação de exemplo para o seu diretório de início rápido. Em seguida, mude para o diretório `multicontainerwordpress`.

```bash
git clone https://github.com/Azure-Samples/multicontainerwordpress

cd multicontainerwordpress
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

Na Cloud Shell, crie um [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) grupo de recursos com o comando. O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *E.U.A. Centro-Sul*. Para ver todas as localizações suportadas para o Serviço de Aplicações no Linux no escalão **Standard**, execute o comando [`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az-appservice-list-locations).

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

Geralmente, o grupo de recursos e os recursos são criados numa região perto de si.

Quando o comando for concluído, uma saída JSON mostra as propriedades do grupo de recursos.

## <a name="create-an-azure-app-service-plan"></a>Criar um plano do Serviço de Aplicações do Azure

Na Cloud Shell, crie um plano de [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) Serviço de Aplicações no grupo de recursos com o comando.

O exemplo seguinte cria um Plano do Serviço de Aplicações com o nome `myAppServicePlan`, no escalão de preços **Standard** (`--sku S1`) e num contentor do Linux (`--is-linux`).

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

Quando o plano do Serviço de Aplicações tiver sido criado, a CLI do Azure mostra informações semelhantes ao seguinte exemplo:

<pre>
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "South Central US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "South Central US",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
</pre>

## <a name="create-a-docker-compose-app"></a>Criar uma aplicação Docker Compose

> [!NOTE]
> Docker Compose on Azure App Services tem atualmente um limite de 4.000 caracteres neste momento.

No seu terminal do Cloud Shell, crie uma [aplicação Web](app-service-linux-intro.md) com vários contentores no plano do Serviço de Aplicações `myAppServicePlan` com o comando [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). Não se esqueça _ \<_ de substituir app_name>por um `0-9`nome `-`único de aplicação (caracteres válidos são `a-z`, e ).

```azurecli
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --multicontainer-config-type compose --multicontainer-config-file compose-wordpress.yml
```

Quando a aplicação Web tiver sido criada, a CLI do Azure mostra informações semelhantes ao seguinte exemplo:

<pre>
{
  "additionalProperties": {},
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app_name&gt;.azurewebsites.net",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

### <a name="browse-to-the-app"></a>Navegar para a aplicação

Navegue para a aplicação implementada em (`http://<app_name>.azurewebsites.net`). A aplicação pode demorar alguns minutos a carregar. Se receber uma mensagem de erro, aguarde mais alguns minutos e, em seguida, atualize o browser.

![Aplicação com vários contentores de exemplo na Aplicação Web para Contentores][1]

**Parabéns,** criou uma aplicação multi-contentores na Web App para contentores.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Aplicação WordPress multi-contentor](tutorial-multi-container-app.md)

> [!div class="nextstepaction"]
> [Configure um recipiente personalizado](configure-custom-container.md)

<!--Image references-->
[1]: ./media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png
