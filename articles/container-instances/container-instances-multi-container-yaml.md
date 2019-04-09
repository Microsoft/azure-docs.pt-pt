---
title: Tutorial - implementar um grupo de vários contentor no Azure Container Instances - YAML
description: Neste tutorial, saiba como implementar um grupo de contentores com vários contentores no Azure Container Instances, utilizando um ficheiro YAML com a CLI do Azure.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/03/2019
ms.author: danlep
ms.openlocfilehash: a0a91ece4f219cf822673cd457c064c326b89478
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/04/2019
ms.locfileid: "59006180"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-yaml-file"></a>Tutorial: Implementar um grupo de vários contentor com um ficheiro YAML

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)
>

O Azure Container Instances suporta a implementação de vários contentores num anfitrião único com uma [grupo de contentores](container-instances-container-groups.md). Um grupo de contentores é útil ao criar um sidecar de aplicativo para o registo, monitorização ou qualquer outra configuração em que um serviço precisa de um segundo processo anexado.

Neste tutorial, siga os passos para executar uma configuração simples de duas contentor de sidecar mediante a implementação de um ficheiro YAML com a CLI do Azure. Um ficheiro YAML fornece um formato conciso para especificar as definições de instância. Saiba como:

> [!div class="checklist"]
> * Configurar um ficheiro YAML
> * Implementar o grupo de contentores
> * Ver os registos dos contentores

> [!NOTE]
> Grupos com vários contentores estão atualmente restritos para contentores do Linux.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="configure-a-yaml-file"></a>Configurar um ficheiro YAML

Para implementar um grupo de vários contentor com o [criar contentor de az] [ az-container-create] comando na CLI do Azure, tem de especificar a configuração do grupo de contentor num ficheiro YAML. Em seguida, passe o ficheiro YAML como um parâmetro para o comando.

Comece por copiar o YAML seguinte num novo ficheiro designado **aci.yaml implementar**. No Azure Cloud Shell, pode usar o Visual Studio Code para criar o ficheiro no diretório de trabalho:

```
code deploy-aci.yaml
```

Este ficheiro YAML define um grupo de contentores com o nome "myContainerGroup", com dois contentores, um endereço IP público e duas portas expostas. Os contentores são implementados a partir de imagens públicas do Microsoft. O primeiro contentor no grupo de executa um aplicativo de web de acesso à internet. O segundo contentor, o sidecar, faz periodicamente pedidos HTTP para a aplicação web em execução no primeiro contentor através da rede local do grupo de contentores.

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

Para utilizar um registo de imagem de contentor privado, adicione o `imageRegistryCredentials` propriedade para o grupo de contentores, com valores modificados para o seu ambiente:

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

## <a name="deploy-the-container-group"></a>Implementar o grupo de contentores

Criar um grupo de recursos com o [criar grupo az] [ az-group-create] comando:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Implementar o grupo de contentores com o [criar contentor de az] [ az-container-create] comando, passando o ficheiro YAML como um argumento:

```azurecli-interactive
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

Dentro de alguns segundos, deverá receber uma resposta inicial do Azure.

## <a name="view-deployment-state"></a>Estado de implementação de exibição

Para ver o estado da implementação, utilize o seguinte procedimento [show de contentor az] [ az-container-show] comando:

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Se gostaria de ver a aplicação em execução, navegue para o respetivo endereço IP no seu browser. Por exemplo, o IP é `52.168.26.124` na saída deste exemplo:

```bash
Name              ResourceGroup    Status    Image                                                                                               IP:ports              Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  --------------------------------------------------------------------------------------------------  --------------------  ---------  ---------------  --------  ----------
myContainerGroup  danlep0318r      Running   mcr.microsoft.com/azuredocs/aci-tutorial-sidecar,mcr.microsoft.com/azuredocs/aci-helloworld:latest  20.42.26.114:80,8080  Public     1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-container-logs"></a>Ver registos de contentor

Ver o resultado de registo de um contentor com o [registos de contentor az] [ az-container-logs] comando. O `--container-name` argumento especifica o contentor a partir do qual pretende extrair registos. Neste exemplo, o `aci-tutorial-app` contentor é especificado.

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

Para ver os registos do contentor de sidecar, execute uma especificação de comando semelhante a `aci-tutorial-sidecar` contentor.

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

Como pode ver, o sidecar está fazendo periodicamente uma solicitação HTTP ao aplicativo web principal por meio de rede de local do grupo para se certificar de que está em execução. Neste exemplo de sidecar pode ser expandido para acionar um alerta se tiver recebido um código de resposta HTTP diferente de `200 OK`.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, utilizou um ficheiro YAML para implementar um grupo de vários contentor no Azure Container Instances. Aprendeu a:

> [!div class="checklist"]
> * Configurar um ficheiro YAML para um grupo de vários contentor
> * Implementar o grupo de contentores
> * Ver os registos dos contentores

Também pode especificar um grupo de vários contentor utilizando um [modelo do Resource Manager](container-instances-multi-container-group.md). Um modelo do Resource Manager pode ser adaptado prontamente para cenários quando precisar de implementar recursos adicionais de serviço do Azure com o grupo de contentores.

<!-- LINKS - External -->


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups
