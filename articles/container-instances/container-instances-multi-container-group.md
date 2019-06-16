---
title: Tutorial - implementar um grupo de vários contentor no Azure Container Instances - modelo
description: Neste tutorial, saiba como implementar um grupo de contentores com vários contentores no Azure Container Instances com um modelo Azure Resource Manager com a CLI do Azure.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/03/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: f769beda1654dc9f58ecff733741fb1ab9118031
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66152290"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-resource-manager-template"></a>Tutorial: Implementar um grupo de vários contentor com um modelo do Resource Manager

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)

O Azure Container Instances suporta a implementação de vários contentores num anfitrião único com uma [grupo de contentores](container-instances-container-groups.md). Um grupo de contentores é útil ao criar um sidecar de aplicativo para o registo, monitorização ou qualquer outra configuração em que um serviço precisa de um segundo processo anexado.

Neste tutorial, siga os passos para executar uma configuração simples de duas contentor de sidecar ao implementar um modelo do Azure Resource Manager com a CLI do Azure. Saiba como:

> [!div class="checklist"]
> * Configurar um modelo de grupo de vários contentores
> * Implementar o grupo de contentores
> * Ver os registos dos contentores

Um modelo do Resource Manager pode ser adaptado prontamente para cenários quando precisar de implementar recursos de serviço do Azure adicionais (por exemplo, uma partilha de ficheiros do Azure ou uma rede virtual) com o grupo de contentores. 

> [!NOTE]
> Grupos com vários contentores estão atualmente restritos para contentores do Linux. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="configure-a-template"></a>Configurar um modelo

Comece por copiar o JSON seguinte num novo arquivo chamado `azuredeploy.json`. No Azure Cloud Shell, pode usar o Visual Studio Code para criar o ficheiro no diretório de trabalho:

```
code azuredeploy.json
```

Este modelo do Resource Manager define um grupo de contentores com dois contentores, um endereço IP público e duas portas expostas. O primeiro contentor no grupo de executa um aplicativo de web de acesso à internet. O segundo contentor, o sidecar, faz uma solicitação HTTP para o aplicativo da web principal por meio de rede de local do grupo.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerGroupName": {
      "type": "string",
      "defaultValue": "myContainerGroup",
      "metadata": {
        "description": "Container Group name."
      }
    }
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "mcr.microsoft.com/azuredocs/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "mcr.microsoft.com/azuredocs/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2018-10-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            },
            {
                "protocol": "tcp",
                "port": "8080"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

Para utilizar um registo de imagem de contentor privado, adicione um objeto para o documento JSON com o seguinte formato. Para uma implementação de exemplo desta configuração, consulte a [referência de modelo do Gestor de recursos do ACI] [ template-reference] documentação.

```JSON
"imageRegistryCredentials": [
  {
    "server": "[parameters('imageRegistryLoginServer')]",
    "username": "[parameters('imageRegistryUsername')]",
    "password": "[parameters('imageRegistryPassword')]"
  }
]
```

## <a name="deploy-the-template"></a>Implementar o modelo

Crie um grupo de recursos com o comando [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Implementar o modelo com o [criar a implementação do grupo az] [ az-group-deployment-create] comando.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file azuredeploy.json
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

```bash
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

```bash
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

Neste tutorial, utilizou um modelo Azure Resource Manager para implementar um grupo de vários contentor no Azure Container Instances. Aprendeu a:

> [!div class="checklist"]
> * Configurar um modelo de grupo de vários contentores
> * Implementar o grupo de contentores
> * Ver os registos dos contentores

Para exemplos de modelos adicionais, consulte [modelos do Azure Resource Manager do Azure Container Instances](container-instances-samples-rm.md).

Também pode especificar um grupo de vários contentor utilizando um [ficheiro YAML](container-instances-multi-container-yaml.md). Devido à natureza de mais concisa do formato YAML, a implementação com um ficheiro YAML é uma boa opção quando a implementação inclui apenas as instâncias de contentor.


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups
