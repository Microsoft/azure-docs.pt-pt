---
title: Guia de início rápido – implantar o contêiner do Docker em instâncias de contêiner do Azure-CLI
description: Neste guia de início rápido, você usa o CLI do Azure para implantar rapidamente um aplicativo Web em contêineres que é executado em uma instância de contêiner do Azure isolada
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: quickstart
ms.date: 03/21/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 7a4a1c24211e644a796b4e60537978c327501383
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325775"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-cli"></a>Início rápido: Implantar uma instância de contêiner no Azure usando o CLI do Azure

Use as instâncias de contêiner do Azure para executar contêineres do Docker sem servidor no Azure com simplicidade e velocidade. Implante um aplicativo em uma instância de contêiner sob demanda quando você não precisar de uma plataforma de orquestração de contêiner completa, como o serviço kubernetes do Azure.

Neste guia de início rápido, você usa o CLI do Azure para implantar um contêiner do Docker isolado e tornar seu aplicativo disponível com um FQDN (nome de domínio totalmente qualificado). Alguns segundos após a execução de um único comando de implantação, você pode navegar até o aplicativo em execução no contêiner:

![Aplicação implementada com o Azure Container Instances vista no browser][aci-app-browser]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita][azure-account] antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pode utilizar o Azure Cloud Shell ou uma instalação local da CLI do Azure para concluir este início rápido. Se você quiser usá-lo localmente, a versão 2.0.55 ou posterior é recomendada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli-install].

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

O Azure Container Instances, como todos os recursos do Azure, tem de ser implementados num grupo de recursos. Os grupos de recursos permitem organizar e gerir recursos relacionados do Azure.

Primeiro, crie um grupo de recursos  chamado MyResource Group no  local eastus com o seguinte comando [AZ Group Create][az-group-create] :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Criar um contentor

Agora que tem um grupo de recursos, pode executar um contentor no Azure. Para criar uma instância de contêiner com o CLI do Azure, forneça um nome de grupo de recursos, o nome da instância de contêiner e a imagem de contêiner do Docker para o comando [AZ container Create][az-container-create] . Neste guia de início rápido, você usa `mcr.microsoft.com/azuredocs/aci-helloworld` a imagem pública. Esta imagem empacota um pequeno aplicativo Web escrito em node. js que serve a uma página HTML estática.

Pode expor os seus contentores à Internet, especificando uma ou mais portas a abrir, uma etiqueta de nome DNS ou ambos. Neste guia de início rápido, você implanta um contêiner com um rótulo de nome DNS para que o aplicativo Web possa ser acessado publicamente.

Execute um comando semelhante ao seguinte para iniciar uma instância de contêiner. Defina um `--dns-name-label` valor que seja exclusivo na região do Azure onde você cria a instância. Se receber uma mensagem de erro "A etiqueta de nome DNS não está disponível ", experimente uma etiqueta de nome DNS diferente.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image mcr.microsoft.com/azuredocs/aci-helloworld --dns-name-label aci-demo --ports 80
```

Dentro de alguns segundos, deverá receber uma resposta da CLI do Azure que indica que a implementação foi concluída. Verifique seu status com o comando [AZ container show][az-container-show] :

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
```

Ao executar o comando, são apresentados o nome de domínio completamente qualificado (FQDN) e o estado de aprovisionamento do contentor.

```console
$ az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
FQDN                               ProvisioningState
---------------------------------  -------------------
aci-demo.eastus.azurecontainer.io  Succeeded
```

Se o estado do contentor `ProvisioningState` passar a **Com êxito**, navegue para o respetivo FQDN no seu browser. Se lhe for apresentada uma página Web semelhante à seguinte, parabéns! Implementou com êxito uma aplicação em execução num contentor de Docker para Azure.

![Captura de ecrã do browser a mostrar a aplicação em execução numa instância do contentor do Azure][aci-app-browser]

Se, inicialmente, a aplicação não for apresentada, poderá ter de aguardar alguns segundos enquanto o DNS propaga e, em seguida, tente atualizar o browser.

## <a name="pull-the-container-logs"></a>Extrair os registos de contentor

Quando precisar de resolver problemas de um contentor ou da aplicação nele executada (ou apenas ver o respetivo resultado), comece por visualizar os registos da instância de contentor.

Efetuar pull dos logs da instância de contêiner com o comando [AZ container logs][az-container-logs] :

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

A saída apresenta os registos para o contentor e deve mostrar os pedidos GET de HTTP gerados quando visualizou a aplicação no seu browser.

```console
$ az container logs --resource-group myResourceGroup --name mycontainer
listening on port 80
::ffff:10.240.255.55 - - [21/Mar/2019:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```

## <a name="attach-output-streams"></a>Anexar fluxos de saída

Além de visualizar os registos, pode anexar o padrão local e os fluxos de erro padrão para o do contentor.

Primeiro, execute o comando [AZ container Attach][az-container-attach] para anexar o console local aos fluxos de saída do contêiner:

```azurecli-interactive
az container attach --resource-group myResourceGroup --name mycontainer
```

Depois de anexada, atualize o browser algumas vezes para gerar algumas saídas adicionais. Quando tiver terminado, desligue a consola com `Control+C`. Deverá ver um resultado semelhante ao seguinte:

```console
$ az container attach --resource-group myResourceGroup --name mycontainer
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 17:27:20+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-helloworld"
(count: 1) (last timestamp: 2019-03-21 17:27:24+00:00) Successfully pulled image "mcr.microsoft.com/azuredocs/aci-helloworld"
(count: 1) (last timestamp: 2019-03-21 17:27:27+00:00) Created container
(count: 1) (last timestamp: 2019-03-21 17:27:27+00:00) Started container

Start streaming logs:
listening on port 80

::ffff:10.240.255.55 - - [21/Mar/2019:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:47:01 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.56 - - [21/Mar/2019:17:47:12 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando você terminar o contêiner, remova-o usando o comando [AZ container Delete][az-container-delete] :

```azurecli-interactive
az container delete --resource-group myResourceGroup --name mycontainer
```

Para verificar se o contentor foi eliminado, execute o comando [az container list](/cli/azure/container#az-container-list):

```azurecli-interactive
az container list --resource-group myResourceGroup --output table
```

O contentor **mycontainer** não deve aparecer na saída do comando. Não se tiver outros contentores no grupo de recursos, não é apresentada nenhuma saída.

Se você tiver feito com o  grupo de recursos MyResource Group e todos os recursos que ele contém, exclua-o com o comando [AZ Group Delete][az-group-delete] :

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você criou uma instância de contêiner do Azure usando uma imagem pública da Microsoft. Se deseja criar uma imagem do contentor e implementá-la partir de um registo de contentor privado do Azure, prossiga para o tutorial do Azure Container Instances.

> [!div class="nextstepaction"]
> [Tutorial do Azure Container Instances](./container-instances-tutorial-prepare-app.md)

Para testar as opções de execução de contêineres em um sistema de orquestração no Azure, consulte os guias de início rápido do [AKs (serviço kubernetes do Azure)][container-service] .

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - External -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[azure-account]: https://azure.microsoft.com/free/
[node-js]: https://nodejs.org

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-container-list]: /cli/azure/container#az-container-list
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[container-service]: ../aks/kubernetes-walkthrough.md
