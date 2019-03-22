---
title: Tutorial – dimensionar uma aplicação em execução no Azure Service Fabric Mesh | Microsoft Docs
description: Neste tutorial, vai ficar a saber como dimensionar os serviços numa aplicação em execução no Service Fabric Mesh.
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
ms.openlocfilehash: 31b1e7a777c65a270b7b08673fcd73a5ab63f11f
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337770"
---
# <a name="tutorial-scale-an-application-running-in-service-fabric-mesh"></a>Tutorial: Dimensionar uma aplicação em execução no Service Fabric Mesh

Este tutorial é a segunda parte de uma série. Saiba como dimensionar manualmente o número de instâncias de serviço de uma aplicação que foi [anteriormente implementada no Service Fabric Mesh](service-fabric-mesh-tutorial-template-deploy-app.md). Quando tiver terminado, terá um serviço de front-end a executar três instâncias e um serviço de dados a executar duas instâncias.

Na segunda parte da série, saiba como:

> [!div class="checklist"]
> * Configurar o número pretendido de instâncias de serviço
> * Efetuar uma atualização

Nesta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * [Implementar uma aplicação no Service Fabric Mesh com um modelo](service-fabric-mesh-tutorial-template-deploy-app.md)
> * Dimensionar uma aplicação em execução no Service Fabric Mesh
> * [Atualizar uma aplicação em execução no Service Fabric Mesh](service-fabric-mesh-tutorial-template-upgrade-app.md)
> * [Remover uma aplicação](service-fabric-mesh-tutorial-template-remove-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* [Instale a CLI do Azure e a CLI do Service Fabric Mesh localmente](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli).

## <a name="manually-scale-your-services-in-or-out"></a>Reduzir ou aumentar horizontalmente os seus serviços de forma manual

Uma das principais vantagens da implementação de aplicações no Service Fabric Mesh é a capacidade de reduzir ou aumentar horizontalmente os seus serviços. Deve utilizar a redução ou o aumento para processar quantidades diferentes de carga nos serviços ou melhorar a disponibilidade.

Este tutorial utiliza o exemplo de Lista de Tarefas, que foi [implementado anteriormente](service-fabric-mesh-tutorial-template-deploy-app.md) e deve estar agora em execução. O aplicativo tem dois serviços: WebFrontEnd e ToDoService. Cada serviço foi inicialmente implementado com uma contagem de réplicas de 1.  Para ver o número de réplicas em execução para o serviço WebFrontEnd, execute o seguinte:

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp --query "replicaCount"
```

Para ver o número de réplicas em execução para o serviço ToDoService, execute o seguinte:

```azurecli
az mesh service show --resource-group myResourceGroup --name ToDoService --app-name todolistapp --query "replicaCount"
```

No modelo de implementação do recurso da aplicação, cada serviço tem uma propriedade *replicaCount* que pode ser utilizada para definir o número de vezes que pretende que o serviço seja implementado. Uma aplicação pode consistir em vários serviços, cada serviço com um número de *replicaCount* exclusivo, que são implementados e geridos em conjunto. Para dimensionar o número de réplicas do serviço, modifique o valor de *replicaCount* para cada serviço que pretende reduzir horizontalmente no modelo de implementação ou ficheiro de parâmetros.  Em seguida, atualize a aplicação.

### <a name="modify-the-deployment-template-parameters"></a>Modificar os parâmetros do modelo de implementação

Quando tiver valores no seu modelo que prevê alterar assim que a aplicação for implementada ou se gostaria de ter a opção para alterar numa base por implementação (se planear reutilizar este modelo para outras implementações), a prática recomendada é parametrizar os valores.

Anteriormente, a aplicação foi implementada com os ficheiros [mesh_rp.windows.json deployment template](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) e [mesh_rp.windows.parameter.json parameters](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json).

Abra o ficheiro [mesh_rp.windows.parameter.json parameters](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json) localmente e defina o valor *frontEndReplicaCount* como 3 e o valor *serviceReplicaCount* como 2:

```json
      "frontEndReplicaCount":{
        "value": "3"
      },
      "serviceReplicaCount":{
        "value": "2"
      }
```

Guarde as alterações ao ficheiro de parâmetros.  Os parâmetros *frontEndReplicaCount* e *serviceReplicaCount* são declarados na secção *parâmetros* do [mesh_rp.windows.json deployment template](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json):

```json
"frontEndReplicaCount":{
      "defaultValue": "1",
      "type": "string"
    },
    "serviceReplicaCount":{
      "defaultValue": "1",
      "type": "string"
    }
```

A propriedade *replicaCount* do serviço WebFrontEnd faz referência ao parâmetro *frontEndReplicaCount* e a propriedade *replicaCount* do serviço ToDoService faz referência ao parâmetro *serviceReplicaCount*:

```json
    "services": [
    {
    "name": "WebFrontEnd",
    "properties": {
        "description": "WebFrontEnd description.",
        "osType": "Windows",
        "codePackages": [
        {
            ...
        }
        ],
        "replicaCount": "[parameters('frontEndReplicaCount')]",
        "networkRefs": [
        {
            "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'todolistappNetwork')]"
        }
        ]
    }
    },
    {
    "name": "ToDoService",
    "properties": {
        "description": "ToDoService description.",
        "osType": "Windows",
        "codePackages": [
        {
            ...
        }
        ],
        "replicaCount": "[parameters('serviceReplicaCount')]",
        "networkRefs": [
        {
            "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'todolistappNetwork')]"
        }
        ]
    }
    }
],
```

Assim que o modelo tiver sido modificado, atualize a aplicação.

### <a name="upgrade-your-application"></a>Atualizar a sua aplicação

Enquanto a aplicação estiver em execução, pode atualizá-la ao reimplementar o modelo e o ficheiro de parâmetros atualizado:

```azurecli
az mesh deployment create --resource-group myResourceGroup --template-file c:\temp\mesh_rp.windows.json --parameters c:\temp\mesh_rp.windows.parameters.json
```

Isto irá iniciar uma atualização sem interrupção para a aplicação e deverá ver os serviços de instâncias aumentarem dentro de alguns minutos.  Para ver o número de réplicas em execução para o serviço WebFrontEnd, execute o seguinte:

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp --query "replicaCount"
```

Para ver o número de réplicas em execução para o serviço ToDoService, execute o seguinte:

```azurecli
az mesh service show --resource-group myResourceGroup --name ToDoService --app-name todolistapp --query "replicaCount"
```

## <a name="next-steps"></a>Passos Seguintes

Nesta parte do tutorial, ficou a saber como:

> [!div class="checklist"]
> * Configurar o número pretendido de instâncias de serviço
> * Efetuar uma atualização

Avance para o tutorial seguinte:
> [!div class="nextstepaction"]
> [Atualizar uma aplicação em execução no Service Fabric Mesh](service-fabric-mesh-tutorial-template-upgrade-app.md)
