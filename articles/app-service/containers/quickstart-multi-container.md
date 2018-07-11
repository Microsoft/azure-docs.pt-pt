---
title: Criar uma aplicação com vários contentores (pré-visualização) na Aplicação Web para Contentores do Azure com uma configuração do Docker Compose
description: Implemente a sua primeira aplicação com vários contentores na Aplicação Web para Contentores do Azure em minutos
keywords: serviço de aplicações do azure, aplicação web, linux, docker, compor, vários contentores, vários contentores, aplicação web para contentores, vários contentores, contentor, kubernetes, wordpress, bd do azure para mysql, base de dados de produção com contentores
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/22/2018
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: bf567402a66f9152c7eb9b97925fec2a159ffe56
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/29/2018
ms.locfileid: "37127425"
---
# <a name="create-a-multi-container-preview-app-using-web-app-for-containers"></a>Criar uma aplicação com vários contentores (pré-visualização) ao utilizar a Aplicação Web para Contentores

A [Aplicação Web para Contentores](app-service-linux-intro.md) proporciona uma forma flexível de utilizar imagens do Docker. Este início rápido mostra como implementar uma aplicação com vários contentores na Aplicação Web para Contentores no [Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview) com uma configuração do Docker Compose. Para o Kubernetes e uma solução completa ponto a ponto com a BD do Azure para MySQL, siga o [tutorial de vários contentores](tutorial-multi-container-app.md).

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

cd quickstart
```

Em seguida, execute o seguinte comando para clonar o repositório da aplicação de exemplo para o seu diretório de início rápido. Em seguida, mude para o diretório `multicontainerwordpress`.

```bash
git clone https://github.com/Azure-Samples/multicontainerwordpress

cd multicontainerwordpress
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

No Cloud Shell, crie um grupo de recursos com o comando [`az group create`](/cli/azure/group?view=azure-cli-latest#az_group_create). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *E.U.A. Centro-Sul*. Para ver todas as localizações suportadas para o Serviço de Aplicações no Linux no escalão **Standard**, execute o comando [`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az_appservice_list_locations).

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

Geralmente, o grupo de recursos e os recursos são criados numa região perto de si.

Quando o comando for concluído, uma saída JSON mostra as propriedades do grupo de recursos.

## <a name="create-an-azure-app-service-plan"></a>Criar um plano do Serviço de Aplicações do Azure

No Cloud Shell, crie um plano do Serviço de Aplicações no grupo de recursos com o comando [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az_appservice_plan_create).

O exemplo seguinte cria um Plano do Serviço de Aplicações com o nome `myAppServicePlan`, no escalão de preços **Standard** (`--sku S1`) e num contentor do Linux (`--is-linux`).

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

Quando o plano do Serviço de Aplicações tiver sido criado, a CLI do Azure mostra informações semelhantes ao seguinte exemplo:

```json
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
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
```

## <a name="create-a-docker-compose-app"></a>Criar uma aplicação Docker Compose

No seu terminal do Cloud Shell, crie uma [aplicação Web](app-service-linux-intro.md) com vários contentores no plano do Serviço de Aplicações `myAppServicePlan` com o comando [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). Não se esqueça de substituir _\<app_name>_ por um nome de aplicação único.

```bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --multicontainer-config-type compose --multicontainer-config-file compose-wordpress.yml
```

Quando a aplicação Web tiver sido criada, a CLI do Azure mostra informações semelhantes ao seguinte exemplo:

```json
{
  "additionalProperties": {},
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="browse-to-the-app"></a>Navegar para a aplicação

Navegue para a aplicação implementada em (`http://<app_name>.azurewebsites.net`). A aplicação pode demorar alguns minutos a carregar. Se receber uma mensagem de erro, aguarde mais alguns minutos e, em seguida, atualize o browser.

![Aplicação com vários contentores de exemplo na Aplicação Web para Contentores][1]

**Parabéns**, criou uma aplicação com vários contentores na Aplicação Web para Contentores.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação do WordPress com vários contentores na Aplicação Web para Contentores](tutorial-multi-container-app.md)

<!--Image references-->
[1]: ./media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png