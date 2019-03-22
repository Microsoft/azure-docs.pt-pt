---
title: Tutorial – Atualizar uma aplicação em execução no Azure Service Fabric Mesh | Microsoft Docs
description: Neste tutorial, vai ficar a saber como atualizar uma aplicação do Service Fabric em execução no Service Fabric Mesh.
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/11/2019
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 3567ede82f2eebf602e95dcd012f5c88a40af796
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337634"
---
# <a name="tutorial-upgrade-a-service-fabric-application-running-in-service-fabric-mesh"></a>Tutorial: Atualizar uma aplicação do Service Fabric em execução na malha de recursos de infraestrutura do serviço

Este tutorial é a terceira parte de uma série. Vai ficar a saber como atualizar uma aplicação do Service Fabric que foi [implementada anteriormente no Service Fabric Mesh](service-fabric-mesh-tutorial-template-deploy-app.md) ao aumentar o número de recursos alocados da CPU.  Quando tiver terminado, terá um serviço de front-end da web em execução com recursos de CPU superior.

Na terceira parte da série, ficará a saber como:

> [!div class="checklist"]
> * Alterar as configurações da aplicação
> * Atualizar uma aplicação em execução no Service Fabric Mesh

Nesta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * [Implementar uma aplicação no Service Fabric Mesh com um modelo](service-fabric-mesh-tutorial-template-deploy-app.md)
> * [Dimensionar uma aplicação em execução no Service Fabric Mesh](service-fabric-mesh-tutorial-template-scale-services.md)
> * Atualizar uma aplicação em execução no Service Fabric Mesh
> * [Remover uma aplicação](service-fabric-mesh-tutorial-template-remove-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Abra o [Azure Cloud Shell](service-fabric-mesh-howto-setup-cli.md) ou [instale a CLI do Azure e a CLI do Service Fabric Mesh localmente](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli).

## <a name="upgrade-application-configurations"></a>Atualizar as configurações da aplicação

Uma das principais vantagens da implementação de aplicações no Service Fabric Mesh é a capacidade de atualizar facilmente a configuração da sua aplicação.  Por exemplo, os recursos de CPU ou de memória dos seus serviços.

Este tutorial utiliza o exemplo de Lista de Tarefas, que foi [implementado anteriormente](service-fabric-mesh-tutorial-template-deploy-app.md) e deve estar agora em execução. O aplicativo tem dois serviços: WebFrontEnd e ToDoService. Cada serviço foi inicialmente implementado com recursos de CPU de 0,5.  Para ver os recursos de CPU do serviço WebFrontEnd, execute o seguinte:

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp
```

No modelo de implementação do recurso da aplicação, cada serviço tem uma propriedade *cpu* que pode ser utilizada para definir os recursos da CPU pedidos. Uma aplicação pode consistir em vários serviços, cada um com uma definição de *cpu* exclusiva, que são implementados e geridos em conjunto. Para aumentar os recursos de CPU do serviço web do front-end, modificar os *cpue* valor no ficheiro de modelo ou parâmetros de implementação.  Em seguida, atualize a aplicação.

### <a name="modify-the-deployment-template-parameters"></a>Modificar os parâmetros do modelo de implementação

Quando tiver valores no seu modelo que prevê alterar assim que a aplicação for implementada ou se gostaria de ter a opção para alterar numa base por implementação (se planear reutilizar este modelo para outras implementações), a prática recomendada é parametrizar os valores.

Anteriormente, a aplicação foi implementada com os ficheiros [mesh_rp.windows.json deployment template](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) e [mesh_rp.windows.parameter.json parameters](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json).

Abra o ficheiro [mesh_rp.windows.parameter.json parameters](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json) localmente e defina o valor *frontEndCpu* como 1:

```json
      "frontEndCpu":{
        "value": "1"
      }
```

Guarde as alterações ao ficheiro de parâmetros.  

O parâmetro *frontEndCpu* é declarado na secção *parâmetros* do [modelo de implementação mesh_rp.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json):

```json
"frontEndCpu": {
    "defaultValue": "0.5",
    "type": "string",
    "metadata": {
        "description": "The CPU resources for the front end web service."
    }
}
```

A propriedade do serviço WebFrontEnd *codePackages->recursos->pedidos->cpu* referencia o parâmetro *frontEndCpu*:

```json
    "services": [
          {
            "name": "WebFrontEnd",
            "properties": {
              "description": "WebFrontEnd description.",
              "osType": "Windows",
              "codePackages": [
                {
                  "name": "WebFrontEnd",
                  "image": "[parameters('frontEndImage')]",
                  ...
                  "resources": {
                    "requests": {
                      "cpu": "[parameters('frontEndCpu')]",
                      "memoryInGB": "[parameters('frontEndMemory')]"
                    }
                  },
                  "imageRegistryCredential": {
                    "server": "[parameters('registryServer')]",
                    "username": "[parameters('registryUserName')]",
                    "password": "[parameters('registryPassword')]"
                  }
                }
              ],
              ...
```

### <a name="upgrade-your-application"></a>Atualizar a sua aplicação

Enquanto a aplicação estiver em execução, pode atualizá-la ao reimplementar o modelo e o ficheiro de parâmetros atualizado:

```azurecli
az mesh deployment create --resource-group myResourceGroup --template-file c:\temp\mesh_rp.windows.json --parameters c:\temp\mesh_rp.windows.parameters.json
```

Tal vai iniciar uma atualização sem interrupção para a aplicação e deve ver os recursos da CPU a aumentarem dentro de alguns minutos.  Para ver os recursos de CPU do serviço WebFrontEnd, execute o seguinte:

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp
```

## <a name="next-steps"></a>Passos Seguintes

Nesta parte do tutorial, ficou a saber como:

> [!div class="checklist"]
> * Alterar as configurações da aplicação
> * Atualizar uma aplicação em execução no Service Fabric Mesh

Avance para o tutorial seguinte:
> [!div class="nextstepaction"]
> [Remover uma aplicação do Service Fabric Mesh](service-fabric-mesh-tutorial-template-remove-app.md)
