---
title: Tutorial - Implementar grupo multi-contentores - modelo
description: Neste tutorial, você aprende a implantar um grupo de contentores com vários contentores em Instâncias de Contentores Azure usando um modelo de Gestor de Recursos Azure com o Azure CLI.
ms.topic: article
ms.date: 04/03/2019
ms.custom: mvc
ms.openlocfilehash: d2b4e20520cad28c5d62118f6c9d10fcc43ac89e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74533628"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-resource-manager-template"></a>Tutorial: Implementar um grupo multi-contentor usando um modelo de Gestor de Recursos

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)

As instâncias de contentores Azure suportam a colocação de vários contentores num único hospedeiro utilizando um grupo de [contentores](container-instances-container-groups.md). Um grupo de contentores é útil na construção de um sidecar de aplicação para a exploração madeireira, monitorização ou qualquer outra configuração em que um serviço precise de um segundo processo anexado.

Neste tutorial, siga os passos para executar uma configuração simples de dois contentores, implantando um modelo de Gestor de Recursos Azure utilizando o Azure CLI. Saiba como:

> [!div class="checklist"]
> * Configure um modelo de grupo multi-contentores
> * Implementar o grupo de contentores
> * Ver os troncos dos recipientes

Um modelo de Gestor de Recursos pode ser facilmente adaptado para cenários quando você precisa implementar recursos de serviço adicionais Azure (por exemplo, uma partilha de Ficheiros Azure ou uma rede virtual) com o grupo de contentores. 

> [!NOTE]
> Atualmente, os grupos multi-contentores estão restritos aos contentores Linux. 

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="configure-a-template"></a>Configurar um modelo

Comece por copiar o seguinte JSON `azuredeploy.json`num novo ficheiro chamado . Em Azure Cloud Shell, pode utilizar o Código do Estúdio Visual para criar o ficheiro no seu diretório de trabalho:

```
code azuredeploy.json
```

Este modelo de Gestor de Recursos define um grupo de contentores com dois contentores, um endereço IP público e duas portas expostas. O primeiro contentor do grupo executa uma aplicação web virada para a Internet. O segundo contentor, o sidecar, faz um pedido http para a aplicação web principal através da rede local do grupo.

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

Para utilizar um registo de imagem de recipiente privado, adicione um objeto ao documento JSON com o seguinte formato. Para um exemplo, implementação desta configuração, consulte a documentação de referência do [modelo ACI Resource Manager.][template-reference]

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

Desloque o modelo com a implementação do [grupo AZ criar][az-group-deployment-create] comando.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file azuredeploy.json
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

```bash
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

Como pode ver, o sidecar está periodicamente a fazer um pedido http para a aplicação web principal através da rede local do grupo para garantir que está em funcionamento. Este exemplo do sidecar poderia ser expandido para desencadear um `200 OK`alerta se recebesse um código de resposta HTTP diferente de .

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, usou um modelo de Gestor de Recursos Azure para implantar um grupo multi-contentores em Instâncias de Contentores Azure. Aprendeu a:

> [!div class="checklist"]
> * Configure um modelo de grupo multi-contentores
> * Implementar o grupo de contentores
> * Ver os troncos dos recipientes

Para amostras adicionais de modelo, consulte os modelos do [Gestor de Recursos Azure para as instâncias](container-instances-samples-rm.md)do contentor azure .

Também pode especificar um grupo multi-contentor usando um [ficheiro YAML](container-instances-multi-container-yaml.md). Devido à natureza mais concisa do formato YAML, a implantação com um ficheiro YAML é uma boa escolha quando a sua implementação inclui apenas instâncias de contentores.


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups
