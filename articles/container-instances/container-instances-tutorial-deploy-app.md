---
title: Tutorial - Implementar app de contentores para instância de contentores
description: Instâncias de contentores azure parte 3 de 3 - Implemente aplicação de contentores para instâncias de contentores azure
ms.topic: tutorial
ms.date: 03/21/2018
ms.custom: seodec18, mvc
ms.openlocfilehash: 757b41bd69d69deb901e3b5b9a633dce3b9e133a
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78249963"
---
# <a name="tutorial-deploy-a-container-application-to-azure-container-instances"></a>Tutorial: Implementar uma aplicação de contentores para instâncias de contentores azure

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

Quando você implanta uma imagem que está hospedada num registo privado de contentores Azure como a criada no [segundo tutorial,](container-instances-tutorial-prepare-acr.md)você deve fornecer credenciais para aceder ao registo. 

A melhor prática para muitos cenários é criar e configurar um diretor de serviço de Diretório Ativo Azure com permissões de *puxar* para o seu registo. Consulte [o Authenticate com o Registo de Contentores Azure a partir de Instâncias de Contentores Azure](../container-registry/container-registry-auth-aci.md) para obter scripts de amostra para criar um diretor de serviço com as permissões necessárias. Tome nota do *ID principal* do serviço e da senha principal do *serviço*. Usa estas credenciais para aceder ao registo quando implanta o contentor.

Também precisa do nome completo do servidor de login do registo de contentores (substitua `<acrName>` com o nome do seu registo):

```azurecli
az acr show --name <acrName> --query loginServer
```

### <a name="deploy-container"></a>Implementar o contentor

Agora, use o [recipiente az criar][az-container-create] comando para implantar o recipiente. Substitua `<acrLoginServer>` pelo valor obtido do comando anterior. Substitua `<service-principal-ID>` e `<service-principal-password>` com o ID principal do serviço e senha que criou para aceder ao registo. Substitua `<aciDnsLabel>` por um nome DNS desejado.

```azurecli
az container create --resource-group myResourceGroup --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-login-server <acrLoginServer> --registry-username <service-principal-ID> --registry-password <service-principal-password> --dns-name-label <aciDnsLabel> --ports 80
```

Dentro de alguns segundos, deverá receber uma resposta inicial do Azure. O valor `--dns-name-label` tem de ser exclusivo na região do Azure em que cria a instância de contentor. Modifique o valor no comando anterior se receber uma mensagem de erro de **Etiqueta de nome DNS** quando executar o comando.

### <a name="verify-deployment-progress"></a>Verificar o progresso da implementação

Para ver o estado da implantação, utilize um [contentor az mostrar:][az-container-show]

```azurecli
az container show --resource-group myResourceGroup --name aci-tutorial-app --query instanceView.state
```

Repita o comando do [contentor az][az-container-show] até que o estado mude de *Pendente* para *Execução*, que deve demorar menos de um minuto. Quando o contentor estiver *Em execução*, avance para o passo seguinte.

## <a name="view-the-application-and-container-logs"></a>Ver os registos de aplicações e do contentor

Uma vez que a implementação seja bem sucedida, exiba o nome de domínio totalmente qualificado do recipiente (FQDN) com o comando de demonstração de [contentores az:][az-container-show]

```azurecli
az container show --resource-group myResourceGroup --name aci-tutorial-app --query ipAddress.fqdn
```

Por exemplo:
```output
"aci-demo.eastus.azurecontainer.io"
```

Para ver a aplicação em execução, navegue até ao nome DNS apresentado no seu browser favorito:

![Aplicação Hello World no browser][aci-app-browser]

Também pode ver a saída de registos do contentor:

```azurecli
az container logs --resource-group myResourceGroup --name aci-tutorial-app
```

Exemplo de saída:

```output
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="clean-up-resources"></a>Limpar recursos

Se já não precisar de nenhum dos recursos que criou nesta série tutorial, pode executar o comando de eliminação do [grupo AZ][az-group-delete] para remover o grupo de recursos e todos os recursos que contém. Este comando elimina o registo de contentor criado, bem como o contentor em execução, e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

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
