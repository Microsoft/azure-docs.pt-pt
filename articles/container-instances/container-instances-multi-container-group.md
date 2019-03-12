---
title: Implementar grupos de vários contentores no Azure Container Instances
description: Saiba como implementar um grupo de contentores com vários contentores no Azure Container Instances com um modelo Azure Resource Manager.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 06/08/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 2dfe1bbf01b7e1fae8c07602ac4faa40ae74ecc9
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57729508"
---
# <a name="deploy-a-multi-container-group-with-a-resource-manager-template"></a>Implementar um grupo de vários contentor com um modelo do Resource Manager

O Azure Container Instances suporta a implementação de vários contentores num anfitrião único com uma [grupo de contentores](container-instances-container-groups.md). Isto é útil ao criar um sidecar de aplicativo para o registo, monitorização ou qualquer outra configuração em que um serviço precisa de um segundo processo anexado.

Existem dois métodos para implementar grupos de vários contentores com a CLI do Azure:

* Implementação de modelo do Resource Manager (Este artigo)
* [Implementação de ficheiro YAML](container-instances-multi-container-yaml.md)

Implementação com um modelo do Resource Manager é recomendada quando precisar de implementar recursos de serviço do Azure adicionais (por exemplo, uma partilha de ficheiros do Azure) no momento da implementação de instância do contentor. Devido à natureza de mais concisa do formato YAML, implementação com um ficheiro YAML é recomendada quando a implementação inclui *apenas* instâncias de contentor.

> [!NOTE]
> Grupos com vários contentores estão atualmente restritos para contentores do Linux. Enquanto estamos a trabalhar para colocar todas as funcionalidades de contentores do Windows, pode encontrar as diferenças da plataforma atual em [Quotas e disponibilidade das regiões do Azure Container Instances](container-instances-quotas.md).

Para exemplos de modelos adicionais, consulte [modelos do Azure Resource Manager do Azure Container Instances](container-instances-samples-rm.md). 

## <a name="configure-the-template"></a>Configurar o modelo

As secções neste artigo explicam como executar uma configuração simples de vários contentores de sidecar ao implementar um modelo Azure Resource Manager.

Comece por criar um arquivo chamado `azuredeploy.json`, em seguida, copie o JSON seguinte para o mesmo.

Este modelo do Resource Manager define um grupo de contentores com dois contentores, um endereço IP público e duas portas expostas. O primeiro contentor no grupo de executa um aplicativo de acesso à internet. O segundo contentor, o sidecar, faz uma solicitação HTTP para o aplicativo da web principal por meio de rede de local do grupo.

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
    "container1image": "microsoft/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "microsoft/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2018-04-01",
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
Name              ResourceGroup    ProvisioningState    Image                                                           IP:ports               CPU/Memory       OsType    Location
----------------  ---------------  -------------------  --------------------------------------------------------------  ---------------------  ---------------  --------  ----------
myContainerGroup  myResourceGroup  Succeeded            microsoft/aci-helloworld:latest,microsoft/aci-tutorial-sidecar  52.168.26.124:80,8080  1.0 core/1.5 gb  Linux     westus
```

## <a name="view-logs"></a>Ver registos

Ver o resultado de registo de um contentor com o [registos de contentor az] [ az-container-logs] comando. O `--container-name` argumento especifica o contentor a partir do qual pretende extrair registos. Neste exemplo, o primeiro contentor é especificado.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Saída:

```bash
listening on port 80
::1 - - [09/Jan/2018:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [09/Jan/2018:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [09/Jan/2018:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Para ver os registos do contentor do lado do carro, execute o comando especificando o segundo nome do contentor.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Saída:

```bash
Every 3s: curl -I http://localhost                          2018-01-09 23:25:11

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
Date: Tue, 09 Jan 2018 23:25:11 GMT
Connection: keep-alive
```

Como pode ver, o sidecar está fazendo periodicamente uma solicitação HTTP ao aplicativo web principal por meio de rede de local do grupo para se certificar de que está em execução. Neste exemplo de sidecar pode ser expandido para acionar um alerta se tiver recebido um código de resposta HTTP que não 200 OK.

## <a name="next-steps"></a>Passos Seguintes

Este artigo abordou os passos necessários para implementar uma instância de contentor do Azure de vários contentores. Para uma experiência do Azure Container Instances-a-ponto, veja o tutorial do Azure Container Instances.

> [!div class="nextstepaction"]
> [Tutorial do Azure Container Instances][aci-tutorial]

<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups
