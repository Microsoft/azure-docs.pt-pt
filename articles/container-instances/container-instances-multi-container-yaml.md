---
title: Tutorial - Implementar grupo multi-contentores - YAML
description: Neste tutorial, aprende-se a implantar um grupo de contentores com vários contentores em Instâncias de Contentores Azure utilizando um ficheiro YAML com o Azure CLI.
ms.topic: article
ms.date: 07/01/2020
ms.openlocfilehash: 74269440357ee2d7ae36661618a31293346fa712
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771268"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-yaml-file"></a>Tutorial: Implementar um grupo multi-contentores usando um ficheiro YAML

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)
>

Azure Container Instances suporta a colocação de múltiplos contentores num único hospedeiro utilizando um [grupo de contentores](container-instances-container-groups.md). Um grupo de contentores é útil quando se constrói um sidecar de aplicação para registo, monitorização ou qualquer outra configuração em que um serviço precise de um segundo processo anexado.

Neste tutorial, segue os passos para executar uma configuração simples do sidecar de dois contentores, implantando um [ficheiro YAML](container-instances-reference-yaml.md) utilizando o Azure CLI. Um ficheiro YAML fornece um formato conciso para especificar as definições de instância. Saiba como:

> [!div class="checklist"]
> * Configure um ficheiro YAML
> * Desdobre o grupo de contentores
> * Ver os registos dos contentores

> [!NOTE]
> Atualmente, os grupos multi-contentores estão restritos aos contentores Linux.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="configure-a-yaml-file"></a>Configure um ficheiro YAML

Para implantar um grupo multi-contentor com o [recipiente az criar][az-container-create] comando no CLI Azure, deve especificar a configuração do grupo de contentores num ficheiro YAML. Em seguida, passe o ficheiro YAML como parâmetro para o comando.

Comece por copiar o seguinte YAML num novo ficheiro chamado **deploy-aci.yaml**. Em Azure Cloud Shell, pode utilizar o Código do Estúdio Visual para criar o ficheiro no seu diretório de trabalho:

```
code deploy-aci.yaml
```

Este ficheiro YAML define um grupo de contentores chamado "myContainerGroup" com dois contentores, um endereço IP público e duas portas expostas. Os contentores são implantados a partir de imagens públicas da Microsoft. O primeiro recipiente do grupo executa uma aplicação web virada para a Internet. O segundo recipiente, o sidecar, faz periodicamente pedidos HTTP à aplicação web que funciona no primeiro contentor através da rede local do grupo de contentores.

```YAML
apiVersion: 2019-12-01
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
      port: 80
    - protocol: tcp
      port: 8080
tags: {exampleTag: tutorial}
type: Microsoft.ContainerInstance/containerGroups
```

Para utilizar um registo de imagem de contentor privado, adicione a `imageRegistryCredentials` propriedade ao grupo de contentores, com valores modificados para o seu ambiente:

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

## <a name="deploy-the-container-group"></a>Desdobre o grupo de contentores

Criar um grupo de recursos com o [grupo az criar][az-group-create] comando:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Desdobre o grupo de contentores com o [contentor az criar][az-container-create] comando, passando o ficheiro YAML como argumento:

```azurecli-interactive
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

Dentro de alguns segundos, deverá receber uma resposta inicial do Azure.

## <a name="view-deployment-state"></a>Ver estado de implantação

Para visualizar o estado da implantação, utilize o seguinte comando [de demonstração de contentores az:][az-container-show]

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Se você quiser ver a aplicação de execução, navegue para o seu endereço IP no seu navegador. Por exemplo, o IP está `52.168.26.124` nesta saída de exemplo:

```console
Name              ResourceGroup    Status    Image                                                                                               IP:ports              Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  --------------------------------------------------------------------------------------------------  --------------------  ---------  ---------------  --------  ----------
myContainerGroup  danlep0318r      Running   mcr.microsoft.com/azuredocs/aci-tutorial-sidecar,mcr.microsoft.com/azuredocs/aci-helloworld:latest  20.42.26.114:80,8080  Public     1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-container-logs"></a>Ver registos de contentor

Ver a saída de registo de um recipiente utilizando o comando [de registos de contentores az.][az-container-logs] O `--container-name` argumento especifica o recipiente a partir do qual puxar os troncos. Neste exemplo, `aci-tutorial-app` o recipiente é especificado.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Resultado:

```console
listening on port 80
::1 - - [02/Jul/2020:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [02/Jul/2020:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [02/Jul/2020:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Para ver os registos do contentor do sidecar, executar um comando semelhante especificando o `aci-tutorial-sidecar` recipiente.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Resultado:

```console
Every 3s: curl -I http://localhost                          2020-07-02 20:36:41

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
Date: Thu, 02 Jul 2020 20:36:41 GMT
Connection: keep-alive
```

Como pode ver, o sidecar está periodicamente a fazer um pedido HTTP à aplicação web principal através da rede local do grupo para garantir que está em funcionamento. Este exemplo sidecar pode ser expandido para desencadear um alerta se receber um código de resposta HTTP diferente de `200 OK` .

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, utilizou um ficheiro YAML para implantar um grupo multi-contentor em Instâncias de Contentores Azure. Aprendeu a:

> [!div class="checklist"]
> * Configure um ficheiro YAML para um grupo multi-contentores
> * Desdobre o grupo de contentores
> * Ver os registos dos contentores

Também pode especificar um grupo multi-contentores utilizando um [modelo de Gestor de Recursos.](container-instances-multi-container-group.md) Um modelo de Gestor de Recursos pode ser facilmente adaptado para cenários quando você precisa implementar recursos adicionais de serviço Azure com o grupo de contentores.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az_container_create
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[az-group-create]: /cli/azure/group#az_group_create
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
[template-reference]: /azure/templates/microsoft.containerinstance/containergroups
