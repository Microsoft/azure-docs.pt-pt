---
title: Quickstart - Desloque o contentor Docker para a instância do contentor - Azure CLI
description: Neste arranque rápido, você usa o Azure CLI para implementar rapidamente uma aplicação web contentorizada que funciona em um caso isolado de contentores Azure
ms.topic: quickstart
ms.date: 03/21/2019
ms.custom:
- seo-python-october2019
- seodec18
- mvc
ms.openlocfilehash: e5cad7d9141963e5062423545f7e5b94f0575152
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "78252194"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-cli"></a>Quickstart: Implementar uma instância de contentores em Azure utilizando o Azure CLI

Utilize instâncias de contentores Azure para executar recipientes Docker sem servidor em Azure com simplicidade e velocidade. Implemente uma aplicação para um contentor a pedido quando não precisar de uma plataforma de orquestração de contentores completa como o Serviço Azure Kubernetes.

Neste arranque rápido, utiliza o Azure CLI para implantar um recipiente Docker isolado e disponibilizar a sua aplicação com um nome de domínio totalmente qualificado (FQDN). Alguns segundos depois de executar um único comando de implantação, pode navegar para a aplicação em execução no recipiente:

![Ver uma aplicação implementada para O Taminado Instâncias no navegador][aci-app-browser]

Se não tiver uma subscrição Azure, crie uma [conta gratuita][azure-account] antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pode utilizar o Azure Cloud Shell ou uma instalação local da CLI do Azure para concluir este início rápido. Se quiser usá-lo localmente, recomenda-se a versão 2.0.55 ou mais tarde. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

O Azure Container Instances, como todos os recursos do Azure, tem de ser implementados num grupo de recursos. Os grupos de recursos permitem organizar e gerir recursos relacionados do Azure.

Primeiro, crie um grupo de recursos com o nome *myResourceGroup* na localização *eualeste* através do comando seguinte [az group create][az-group-create]:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Criar um contentor

Agora que tem um grupo de recursos, pode executar um contentor no Azure. Para criar uma instância de contentor com a CLI do Azure, forneça um nome de um grupo de recursos, o nome da instância de contentor e a imagem do contentor de Docker para o cmdlet [az container create][az-container-create]. Neste arranque rápido, usa-se a imagem pública. `mcr.microsoft.com/azuredocs/aci-helloworld` Esta imagem embala uma pequena aplicação web escrita no Node.js que serve uma página html estática.

Pode expor os seus contentores à Internet, especificando uma ou mais portas a abrir, uma etiqueta de nome DNS ou ambos. Neste arranque rápido, você implementa um recipiente com uma etiqueta de nome DNS para que a aplicação web seja publicamente acessível.

Execute um comando semelhante ao seguinte para iniciar uma instância de contentor. Desempõe um `--dns-name-label` valor único dentro da região de Azure onde cria si a instância. Se receber uma mensagem de erro "A etiqueta de nome DNS não está disponível ", experimente uma etiqueta de nome DNS diferente.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image mcr.microsoft.com/azuredocs/aci-helloworld --dns-name-label aci-demo --ports 80
```

Dentro de alguns segundos, deverá receber uma resposta da CLI do Azure que indica que a implementação foi concluída. Verifique o respetivo estado com o comando [az container show][az-container-show]:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
```

Ao executar o comando, são apresentados o nome de domínio completamente qualificado (FQDN) e o estado de aprovisionamento do contentor.

```output
FQDN                               ProvisioningState
---------------------------------  -------------------
aci-demo.eastus.azurecontainer.io  Succeeded
```

Se o contentor `ProvisioningState` for **bem sucedido,** vá ao seu FQDN no seu navegador. Se lhe for apresentada uma página Web semelhante à seguinte, parabéns! Implementou com êxito uma aplicação em execução num contentor de Docker para Azure.

![Ver uma aplicação implementada para O Taminado Instâncias no navegador][aci-app-browser]

Se, inicialmente, a aplicação não for apresentada, poderá ter de aguardar alguns segundos enquanto o DNS propaga e, em seguida, tente atualizar o browser.

## <a name="pull-the-container-logs"></a>Extrair os registos de contentor

Quando precisar de resolver problemas de um contentor ou da aplicação nele executada (ou apenas ver o respetivo resultado), comece por visualizar os registos da instância de contentor.

Extraia os registos da instância de contentor com o comando [az container logs][az-container-logs]:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

A saída apresenta os registos para o contentor e deve mostrar os pedidos GET de HTTP gerados quando visualizou a aplicação no seu browser.

```output
listening on port 80
::ffff:10.240.255.55 - - [21/Mar/2019:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```

## <a name="attach-output-streams"></a>Anexar fluxos de saída

Além de visualizar os registos, pode anexar o padrão local e os fluxos de erro padrão para o do contentor.

Em primeiro lugar, execute o comando de fixação do [recipiente AZ][az-container-attach] para fixar a sua consola local aos fluxos de saída do contentor:

```azurecli-interactive
az container attach --resource-group myResourceGroup --name mycontainer
```

Depois de anexada, atualize o browser algumas vezes para gerar algumas saídas adicionais. Quando tiver terminado, desligue a consola com `Control+C`. Deverá ver um resultado semelhante ao seguinte:

```output
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

Quando já não precisar do contentor, remova-o com o comando [az container delete][az-container-delete]:

```azurecli-interactive
az container delete --resource-group myResourceGroup --name mycontainer
```

Para verificar se o contentor foi eliminado, execute o comando [az container list](/cli/azure/container#az-container-list):

```azurecli-interactive
az container list --resource-group myResourceGroup --output table
```

O contentor **mycontainer** não deve aparecer na saída do comando. Não se tiver outros contentores no grupo de recursos, não é apresentada nenhuma saída.

Se tiver concluído a utilização do grupo de recursos *myResourceGroup* e de todos os recursos contidos no mesmo, elimine-o com o comando [az group delete][az-group-delete]:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um caso de contentor Estoque Azure utilizando uma imagem pública da Microsoft. Se quiser criar uma imagem de contentor e implementá-la partir de um registo de contentor privado do Azure, prossiga para o tutorial do Azure Container Instances.

> [!div class="nextstepaction"]
> [Tutorial do Azure Container Instances](./container-instances-tutorial-prepare-app.md)

Para experimentar opções para executar contentores num sistema de orquestração em Azure, consulte os quickstarts do [Serviço Azure Kubernetes (AKS).][container-service]

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/view-an-application-running-in-an-azure-container-instance.png

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
