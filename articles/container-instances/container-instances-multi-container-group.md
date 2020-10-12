---
title: Tutorial - Implementar grupo multi-contentores - modelo
description: Neste tutorial, você aprende a implantar um grupo de contentores com vários recipientes em Instâncias de Contentores Azure usando um modelo de Gestor de Recursos Azure com o CLI Azure.
ms.topic: article
ms.date: 07/02/2020
ms.custom: mvc
ms.openlocfilehash: cb085112c6e6458d897f52f19988e6301d4ae6e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86259568"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-resource-manager-template"></a>Tutorial: Implementar um grupo multi-contentores usando um modelo de Gestor de Recursos

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)

Azure Container Instances suporta a colocação de múltiplos contentores num único hospedeiro utilizando um [grupo de contentores](container-instances-container-groups.md). Um grupo de contentores é útil quando se constrói um sidecar de aplicação para registo, monitorização ou qualquer outra configuração em que um serviço precise de um segundo processo anexado.

Neste tutorial, segue os passos para executar uma configuração simples de dois contentores sidecar, implantando um modelo de Gestor de Recursos Azure utilizando o CLI Azure. Saiba como:

> [!div class="checklist"]
> * Configure um modelo de grupo multi-contentores
> * Desdobre o grupo de contentores
> * Ver os registos dos contentores

Um modelo de Gestor de Recursos pode ser facilmente adaptado para cenários quando é necessário implementar recursos adicionais de serviço Azure (por exemplo, uma partilha de Ficheiros Azure ou uma rede virtual) com o grupo de contentores. 

> [!NOTE]
> Atualmente, os grupos multi-contentores estão restritos aos contentores Linux. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="configure-a-template"></a>Configure um modelo

Comece por copiar o seguinte JSON num novo ficheiro chamado `azuredeploy.json` . Em Azure Cloud Shell, pode utilizar o Código do Estúdio Visual para criar o ficheiro no seu diretório de trabalho:

```
code azuredeploy.json
```

Este modelo de Gestor de Recursos define um grupo de contentores com dois contentores, um endereço IP público e duas portas expostas. O primeiro recipiente do grupo executa uma aplicação web virada para a Internet. O segundo contentor, o sidecar, faz um pedido HTTP à aplicação web principal através da rede local do grupo.

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
      "apiVersion": "2019-12-01",
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
              "port": 80
            },
            {
                "protocol": "tcp",
                "port": 8080
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

Para utilizar um registo de imagem de contentor privado, adicione um objeto ao documento JSON com o seguinte formato. Para uma implementação por exemplo desta configuração, consulte a documentação de referência do [modelo do Gestor de Recursos ACI.][template-reference]

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

Implementar o modelo com o [grupo de implementação az criar][az-deployment-group-create] comando.

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file azuredeploy.json
```

Dentro de alguns segundos, deverá receber uma resposta inicial do Azure.

## <a name="view-deployment-state"></a>Ver estado de implantação

Para visualizar o estado da implantação, utilize o seguinte comando [de demonstração de contentores az:][az-container-show]

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Se você quiser ver a aplicação de execução, navegue para o seu endereço IP no seu navegador. Por exemplo, o IP está `52.168.26.124` nesta saída de exemplo:

```bash
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

```bash
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

```bash
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

Neste tutorial, você usou um modelo de Gestor de Recursos Azure para implantar um grupo multi-contentor em Instâncias de Contentores Azure. Aprendeu a:

> [!div class="checklist"]
> * Configure um modelo de grupo multi-contentores
> * Desdobre o grupo de contentores
> * Ver os registos dos contentores

Para amostras adicionais do modelo, consulte [os modelos do Gestor de Recursos Azure para instâncias de contentores Azure](container-instances-samples-rm.md).

Também pode especificar um grupo multi-contentores utilizando um [ficheiro YAML](container-instances-multi-container-yaml.md). Devido à natureza mais concisa do formato YAML, a implantação com um ficheiro YAML é uma boa escolha quando a sua implantação inclui apenas instâncias de contentores.


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[template-reference]: /azure/templates/microsoft.containerinstance/containergroups
