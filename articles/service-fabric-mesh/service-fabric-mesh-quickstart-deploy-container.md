---
title: Quickstart - Implante Hello World para malha de tecido de serviço azure
description: Este início rápido mostra como implementar uma aplicação do Service Fabric Mesh no Azure Service Fabric Mesh.
author: dkkapur
ms.author: dekapur
ms.date: 11/27/2018
ms.topic: quickstart
ms.openlocfilehash: ce897b6e0e9d6a0b9b672907a64f4683f907b677
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "75458971"
---
# <a name="quickstart-deploy-hello-world-to-service-fabric-mesh"></a>Início Rápido: Implementar o Hello World no Service Fabric Mesh

O [Service Fabric Mesh](service-fabric-mesh-overview.md) facilita a criação e a gestão de aplicações de microsserviços no Azure, sem ter de aprovisionar máquinas virtuais. Neste início rápido, vai criar uma aplicação Hello World no Azure e expô-la na internet. Esta operação é concluída com um único comando. Em apenas alguns minutos, verá esta vista no seu browser:

![Aplicação Hello World no browser][sfm-app-browser]

[!INCLUDE [preview note](./includes/include-preview-note.md)]

Se ainda não tiver uma conta do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="set-up-service-fabric-mesh-cli"></a>Configurar a CLI do Service Fabric mesh 
Pode utilizar o Azure Cloud Shell ou uma instalação local da CLI do Azure para concluir este início rápido. Instale o módulo de extensão da CLI do Azure Service Fabric Mesh através destas [instruções](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no Azure e defina a sua subscrição.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-resource-group"></a>Criar grupo de recursos
Crie um grupo de recursos no qual quer implementar a aplicação. Pode utilizar um grupo de recursos existente e ignorar este passo. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-application"></a>Implementar a aplicação
Crie a aplicação no grupo de recursos com o comando `az mesh deployment create`.  Execute o seguinte:

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/helloworld/helloworld.linux.json --parameters "{'location': {'value': 'eastus'}}" 
```

O comando anterior implementa uma aplicação Linux utilizando o [modelo linux.json](https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/helloworld/helloworld.linux.json). Se pretender implementar uma aplicação Windows, utilize o [modelo windows.json](https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/helloworld/helloworld.windows.json). As imagens de contentor do Windows são maiores do que as imagens de contentor do Linux e podem demorar mais tempo a implementar.

Este comando produzirá um corte JSON que é mostrado abaixo. Sob ```outputs``` a secção da saída JSON, copie a ```publicIPAddress``` propriedade.

```json
"outputs": {
    "publicIPAddress": {
    "type": "String",
    "value": "40.83.78.216"
    }
}
```

Esta informação ```outputs``` vem da secção do modelo ARM. Como mostrado abaixo, esta secção faz referência ao recurso Gateway para obter o endereço IP público. 

```json
  "outputs": {
    "publicIPAddress": {
      "value": "[reference('helloWorldGateway').ipAddress]",
      "type": "string"
    }
  }
```

## <a name="open-the-application"></a>Abrir a aplicação
Assim que a aplicação for implementada com êxito, copie o endereço IP público para o ponto final de serviço a partir da saída da CLI. Abra o endereço IP num browser. É apresentada uma página Web com o logótipo do Azure Service Fabric Mesh.

## <a name="check-the-application-details"></a>Verificar os detalhes da aplicação
Pode verificar o estado da aplicação com o comando `az mesh app show`. Este comando fornece informações úteis que pode acompanhar.

O nome da aplicação neste início rápido é `helloWorldApp`. Para recolher os detalhes da aplicação, execute o seguinte comando:

```azurecli-interactive
az mesh app show --resource-group myResourceGroup --name helloWorldApp
```

## <a name="see-the-application-logs"></a>Ver os registos da aplicação.

Examine os registos da aplicação implementada com o comando `az mesh code-package-log get`:

```azurecli-interactive
az mesh code-package-log get --resource-group myResourceGroup --application-name helloWorldApp --service-name helloWorldService --replica-name 0 --code-package-name helloWorldCode
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando estiver pronto para eliminar a aplicação, execute o comando [az group delete][az-group-delete] para remover o grupo de recursos, a aplicação e os recursos de rede que contém.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como criar e implementar aplicações do Service Fabric Mesh, avance para o tutorial.
> [!div class="nextstepaction"]
> [Criar e implementar uma aplicação Web de vários serviços](service-fabric-mesh-tutorial-create-dotnetcore.md)

<!-- Images -->
[sfm-app-browser]: ./media/service-fabric-mesh-quickstart-deploy-container/HelloWorld.png

<!-- Links / Internal -->
[az-group-delete]: /cli/azure/group
[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
