---
title: Implementar grupos de vários contentores no Azure Container Instances com a CLI do Azure e YAML
description: Saiba como implementar um grupo de contentores com vários contentores no Azure Container Instances com a CLI do Azure e um ficheiro YAML.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 03/21/2019
ms.author: danlep
ms.openlocfilehash: 10f2340bd85da3dabcd50d51a4dd56d58d31675b
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372442"
---
# <a name="deploy-a-multi-container-container-group-with-yaml"></a>Implementar um grupo de contentores de vários contentores com YAML

O Azure Container Instances suporta a implementação de vários contentores num anfitrião único através de um [grupo de contentores](container-instances-container-groups.md). Grupos de contentores de vários contentores são úteis durante a criação de um sidecar de aplicativo para o registo, monitorização ou qualquer outra configuração em que um serviço precisa de um segundo processo anexado.

Existem dois métodos para implementar grupos de vários contentores com a CLI do Azure:

* Implementação de ficheiro YAML (Este artigo)
* [Implementação de modelo do Resource Manager](container-instances-multi-container-group.md)

Devido à natureza de mais concisa do formato YAML, implementação com um ficheiro YAML é recomendada quando a implementação inclui *apenas* instâncias de contentor. Se precisar de implementar recursos de serviço do Azure adicionais (por exemplo, uma partilha de ficheiros do Azure) no momento da implementação de instância de contentor, recomenda-se a implementação de modelo do Resource Manager.

> [!NOTE]
> Grupos com vários contentores estão atualmente restritos para contentores do Linux. Enquanto estamos a trabalhar para colocar todas as funcionalidades para os contentores do Windows, pode encontrar as diferenças de plataforma atual em [Quotas e disponibilidade das regiões do Azure Container Instances](container-instances-quotas.md).

## <a name="configure-the-yaml-file"></a>Configurar o ficheiro YAML

Para implementar um grupo de contentores de vários contentores com o [criar contentor de az] [ az-container-create] comando na CLI do Azure, tem de especificar a configuração do grupo de contentor num ficheiro YAML e passar o ficheiro YAML como um parâmetro para o comando.

Comece por copiar o YAML seguinte num novo ficheiro designado **aci.yaml implementar**.

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

## <a name="view-logs"></a>Ver registos

Ver o resultado de registo de um contentor com o [registos de contentor az] [ az-container-logs] comando. O `--container-name` argumento especifica o contentor a partir do qual pretende extrair registos. Neste exemplo, o primeiro contentor é especificado.

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

Para ver os registos do contentor do lado do carro, execute o comando especificando o segundo nome do contentor.

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

Como pode ver, o sidecar está fazendo periodicamente uma solicitação HTTP ao aplicativo web principal por meio de rede de local do grupo para se certificar de que está em execução. Neste exemplo de sidecar pode ser expandido para acionar um alerta se tiver recebido um código de resposta HTTP que não 200 OK.

## <a name="deploy-from-private-registry"></a>Implemente a partir do registo privado

Para utilizar um registo de imagem de contentor privado, incluem o seguinte YAML com valores modificados para o seu ambiente:

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

Por exemplo, o YAML seguinte implementa um grupo de contentores com um único contentor cuja imagem é obtida a partir de um registo privado de contentor do Azure com o nome "myregistry":

```YAML
apiVersion: 2018-10-01
location: eastus
name: myContainerGroup2
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: myregistry.azurecr.io/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
  imageRegistryCredentials:
  - server: myregistry.azurecr.io
    username: myregistry
    password: REGISTRY_PASSWORD
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

## <a name="export-container-group-to-yaml"></a>Exportar grupo de contentores para YAML

Pode exportar a configuração de um grupo de contentor existente para um ficheiro YAML, utilizando o comando da CLI do Azure [exportação do contentor de az][az-container-export].

Útil para preservar a configuração de um grupo de contentores, exportação permite-lhe armazenar suas configurações de grupo do contentor no controle de versão para "configuração como código." Em alternativa, utilize o ficheiro exportado como um ponto de partida ao desenvolver uma configuração de novo no YAML.

Exportar a configuração para o grupo de contentor que criou anteriormente emitindo o seguinte procedimento [exportação do contentor de az] [ az-container-export] comando:

```azurecli-interactive
az container export --resource-group myResourceGroup --name myContainerGroup --file deployed-aci.yaml
```

Se o comando é efetuada com êxito, mas pode ver o conteúdo do ficheiro para ver o resultado, é apresentada nenhuma saída. Por exemplo, as primeiras linhas com `head`:

```console
$ head deployed-aci.yaml
additional_properties: {}
apiVersion: '2018-06-01'
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
```

## <a name="next-steps"></a>Passos Seguintes

Este artigo abordou os passos necessários para implementar uma instância de contentor do Azure de vários contentores. Para uma experiência de Azure Container Instances-a-ponto, incluindo a utilização de um registo de contentores do Azure privado, consulte o tutorial do Azure Container Instances.

> [!div class="nextstepaction"]
> [Tutorial do Azure Container Instances][aci-tutorial]

<!-- LINKS - External -->
[cli-issue-6525]: https://github.com/Azure/azure-cli/issues/6525

<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az-container-create
[az-container-export]: /cli/azure/container#az-container-export
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups
