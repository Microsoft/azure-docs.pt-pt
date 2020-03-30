---
title: Tutorial - Implementar grupo multi-contentores - YAML
description: Neste tutorial, aprende-se a implantar um grupo de contentores com vários contentores em Instâncias de Contentores Azure utilizando um ficheiro YAML com o Azure CLI.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: cce98ec56ee1d84c087150ba486b9482515b46f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533595"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-yaml-file"></a>Tutorial: Implementar um grupo multi-contentor usando um ficheiro YAML

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)
>

As instâncias de contentores Azure suportam a colocação de vários contentores num único hospedeiro utilizando um grupo de [contentores](container-instances-container-groups.md). Um grupo de contentores é útil na construção de um sidecar de aplicação para a exploração madeireira, monitorização ou qualquer outra configuração em que um serviço precise de um segundo processo anexado.

Neste tutorial, siga os passos para executar uma configuração simples de dois contentores, implantando um [ficheiro YAML](container-instances-reference-yaml.md) utilizando o Azure CLI. Um ficheiro YAML fornece um formato conciso para especificar as definições da instância. Saiba como:

> [!div class="checklist"]
> * Configure um ficheiro YAML
> * Implementar o grupo de contentores
> * Ver os troncos dos recipientes

> [!NOTE]
> Atualmente, os grupos multi-contentores estão restritos aos contentores Linux.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="configure-a-yaml-file"></a>Configure um ficheiro YAML

Para implantar um grupo multi-contentores com o [recipiente Az criar][az-container-create] comando no Azure CLI, deve especificar a configuração do grupo de contentores num ficheiro YAML. Em seguida, passe o ficheiro YAML como parâmetro para o comando.

Comece por copiar o seguinte YAML num novo ficheiro chamado **deploy-aci.yaml**. Em Azure Cloud Shell, pode utilizar o Código do Estúdio Visual para criar o ficheiro no seu diretório de trabalho:

```
code deploy-aci.yaml
```

Este ficheiro YAML define um grupo de contentores chamado "myContainerGroup" com dois contentores, um endereço IP público e duas portas expostas. Os contentores são implantados a partir de imagens públicas da Microsoft. O primeiro contentor do grupo executa uma aplicação web virada para a Internet. O segundo recipiente, o sidecar, faz periodicamente pedidos http para a aplicação web que funciona no primeiro contentor através da rede local do grupo de contentores.

```YAML
apiVersion: 2018-10-01
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
      - port: 8080
  - name: aci-tutorial-sidecar
    properties:
      image: mcr.microsoft.com/azuredocs/aci-tutorial-sidecar
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
    - protocol: tcp
      port: '8080'
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Para utilizar um registo de imagem `imageRegistryCredentials` de contentor privado, adicione a propriedade ao grupo de contentores, com valores modificados para o seu ambiente:

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

## <a name="deploy-the-container-group"></a>Implementar o grupo de contentores

Criar um grupo de recursos com o [grupo AZ criar][az-group-create] comando:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Desloque o grupo de contentores com o [recipiente az criar][az-container-create] comando, passando o ficheiro YAML como argumento:

```azurecli-interactive
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

Dentro de alguns segundos, deverá receber uma resposta inicial do Azure.

## <a name="view-deployment-state"></a>Ver estado de implantação

Para ver o estado da implantação, utilize o seguinte comando de demonstração de [contentores az:][az-container-show]

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Se quiser ver a aplicação em execução, navegue para o seu endereço IP no seu browser. Por exemplo, o `52.168.26.124` IP está neste exemplo de saída:

```bash
Name              ResourceGroup    Status    Image                                                                                               IP:ports              Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  --------------------------------------------------------------------------------------------------  --------------------  ---------  ---------------  --------  ----------
myContainerGroup  danlep0318r      Running   mcr.microsoft.com/azuredocs/aci-tutorial-sidecar,mcr.microsoft.com/azuredocs/aci-helloworld:latest  20.42.26.114:80,8080  Public     1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-container-logs"></a>Ver registos de contentor

Ver a saída de registo de um recipiente utilizando o comando de troncos de [recipiente az.][az-container-logs] O `--container-name` argumento especifica o recipiente a partir do qual puxar troncos. Neste exemplo, `aci-tutorial-app` o recipiente é especificado.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Saída:

```console
listening on port 80
::1 - - [21/Mar/2019:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [21/Mar/2019:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [21/Mar/2019:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Para ver os troncos do recipiente do sidecar, `aci-tutorial-sidecar` faça um comando semelhante especificando o recipiente.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Saída:

```console
Every 3s: curl -I http://localhost                          2019-03-21 20:36:41

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
X-Powered-By: Express
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Wed, 29 Nov 2017 06:40:40 GMT
ETag: W/"67f-16006818640"
Content-Type: text/html; charset=UTF-8
Content-Length: 1663
Date: Thu, 21 Mar 2019 20:36:41 GMT
Connection: keep-alive
```

Como pode ver, o sidecar está periodicamente a fazer um pedido http para a aplicação web principal através da rede local do grupo para garantir que está em funcionamento. Este exemplo do sidecar poderia ser expandido para desencadear um `200 OK`alerta se recebesse um código de resposta HTTP diferente de .

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, usou um ficheiro YAML para implantar um grupo multi-contentores em Instâncias de Contentores Azure. Aprendeu a:

> [!div class="checklist"]
> * Configure um ficheiro YAML para um grupo multi-contentores
> * Implementar o grupo de contentores
> * Ver os troncos dos recipientes

Também pode especificar um grupo multi-contentor usando um modelo de Gestor de [Recursos](container-instances-multi-container-group.md). Um modelo de Gestor de Recursos pode ser facilmente adaptado para cenários quando você precisa implementar recursos adicionais de serviço Azure com o grupo de contentores.

<!-- LINKS - External -->


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups
