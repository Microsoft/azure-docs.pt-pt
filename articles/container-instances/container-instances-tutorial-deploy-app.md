---
title: Tutorial – implantar aplicativo de contêiner em instâncias de contêiner do Azure
description: Tutorial de instâncias de contêiner do Azure, parte 3 de 3-implantar aplicativo de contêiner em instâncias de contêiner do Azure
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: tutorial
ms.date: 03/21/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: f2890948dd15fa972104e4ef11001e83a2abd4f8
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73846585"
---
# <a name="tutorial-deploy-a-container-application-to-azure-container-instances"></a>Tutorial: implantar um aplicativo de contêiner em instâncias de contêiner do Azure

Este é o último tutorial de uma série de três partes. Nas duas primeiras partes da série, [foi criada uma imagem de contentor](container-instances-tutorial-prepare-app.md) e [enviada para o Azure Container Registry](container-instances-tutorial-prepare-acr.md). Este artigo termina a série ao implementar o contentor no Azure Container Instances.

Neste tutorial:

> [!div class="checklist"]
> * Vai implementar o contentor a partir do Azure Container Registry para o Azure Container Instances
> * Vai ver a aplicação em execução no browser
> * Vai ver os registos do contentor

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="deploy-the-container-using-the-azure-cli"></a>Implementar o contentor através da CLI do Azure

Nesta secção, vai utilizar a CLI do Azure para implementar a imagem criada no [primeiro tutorial](container-instances-tutorial-prepare-app.md) e enviada para o Azure Container Registry no [segundo tutorial](container-instances-tutorial-prepare-acr.md). Certifique-se de que concluiu esses tutoriais antes de continuar.

### <a name="get-registry-credentials"></a>Obter as credenciais do registo

Quando você implanta uma imagem hospedada em um registro de contêiner do Azure privado como aquele criado no [segundo tutorial](container-instances-tutorial-prepare-acr.md), você deve fornecer credenciais para acessar o registro. 

Uma prática recomendada para muitos cenários é criar e configurar uma entidade de serviço Azure Active Directory com permissões de *pull* para o registro. Confira [autenticar com o registro de contêiner do Azure de instâncias de contêiner do Azure](../container-registry/container-registry-auth-aci.md) para scripts de exemplo para criar uma entidade de serviço com as permissões necessárias. Anote a ID da *entidade de serviço* e a *senha da entidade de serviço*. Você usa essas credenciais para acessar o registro ao implantar o contêiner.

Você também precisa do nome completo do servidor de logon do registro de contêiner (substitua `<acrName>` pelo nome do registro):

```azurecli
az acr show --name <acrName> --query loginServer
```

### <a name="deploy-container"></a>Implementar o contentor

Agora, use o comando [AZ container Create][az-container-create] para implantar o contêiner. Substitua `<acrLoginServer>` pelo valor obtido do comando anterior. Substitua `<service-principal-ID>` e `<service-principal-password>` com a ID da entidade de serviço e a senha que você criou para acessar o registro. Substitua `<aciDnsLabel>` por um nome DNS desejado.

```azurecli
az container create --resource-group myResourceGroup --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-login-server <acrLoginServer> --registry-username <service-principal-ID> --registry-password <service-principal-password> --dns-name-label <aciDnsLabel> --ports 80
```

Dentro de alguns segundos, deverá receber uma resposta inicial do Azure. O valor `--dns-name-label` tem de ser exclusivo na região do Azure em que cria a instância de contentor. Modifique o valor no comando anterior se receber uma mensagem de erro de **Etiqueta de nome DNS** quando executar o comando.

### <a name="verify-deployment-progress"></a>Verificar o progresso da implementação

Para exibir o estado da implantação, use [AZ container show][az-container-show]:

```azurecli
az container show --resource-group myResourceGroup --name aci-tutorial-app --query instanceView.state
```

Repita o comando [AZ container show][az-container-show] até que o estado mude de *pendente* para *em execução*, o que deve levar menos de um minuto. Quando o contentor estiver *Em execução*, avance para o passo seguinte.

## <a name="view-the-application-and-container-logs"></a>Ver os registos de aplicações e do contentor

Depois que a implantação for realizada com sucesso, exiba o FQDN (nome de domínio totalmente qualificado) do contêiner com o comando [AZ container show][az-container-show] :

```bash
az container show --resource-group myResourceGroup --name aci-tutorial-app --query ipAddress.fqdn
```

Por exemplo:
```console
$ az container show --resource-group myResourceGroup --name aci-tutorial-app --query ipAddress.fqdn
"aci-demo.eastus.azurecontainer.io"
```

Para ver a aplicação em execução, navegue até ao nome DNS apresentado no seu browser favorito:

![Aplicação Hello World no browser][aci-app-browser]

Também pode ver a saída de registos do contentor:

```azurecli
az container logs --resource-group myResourceGroup --name aci-tutorial-app
```

Exemplo de saída:

```bash
$ az container logs --resource-group myResourceGroup --name aci-tutorial-app
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você não precisar mais de nenhum dos recursos criados nesta série de tutoriais, poderá executar o comando [AZ Group Delete][az-group-delete] para remover o grupo de recursos e todos os recursos que ele contém. Este comando elimina o registo de contentor criado, bem como o contentor em execução, e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, concluiu o processo de implementação do contentor no Azure Container Instances. Foram efetuados os seguintes passos:

> [!div class="checklist"]
> * Foi implementado o contentor do Azure Container Registry através da CLI do Azure
> * Foi visualizada a aplicação no browser
> * Foram visualizados os registos de contentor

Agora que já tem as noções básicas, avance para saber mais sobre o Azure Container Instances, como, por exemplo, como funcionam os grupos de contentores:

> [!div class="nextstepaction"]
> [Grupos de contentores no Azure Container Instances](container-instances-container-groups.md)

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[prepare-app]: ./container-instances-tutorial-prepare-app.md
