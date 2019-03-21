---
title: Tutorial – Implementar uma aplicação no Azure Service Fabric Mesh | Microsoft Docs
description: Neste tutorial, saiba como implementar uma aplicação no Service Fabric Mesh com um modelo.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/11/2019
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 773a466f9619f162fe3f7cbeeab0b766d3ca32ae
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57864869"
---
# <a name="tutorial-deploy-an-application-to-service-fabric-mesh-using-a-template"></a>Tutorial: Implementar uma aplicação no Service Fabric Mesh com um modelo

Este tutorial é a primeira parte de uma série. Vai aprender a implementar uma aplicação do Azure Service Fabric Mesh com um modelo.  A aplicação é composta por um serviço de front-end da Web ASP.NET e um serviço de back-end de API Web ASP.NET Core, que se encontram no Hub do Docker.  Extraia as duas imagens do contentor do Hub do Docker e, em seguida, envie-as para o seu registo privado. Depois, crie um modelo do Azure RM para a aplicação e implemente a aplicação do seu registo de contentor no Service Fabric Mesh. Quando tiver terminado, terá uma simples aplicação de Lista de Tarefas em execução no Service Fabric Mesh.

Na primeira parte da série, saiba como:

> [!div class="checklist"]
> * Criar uma instância do Azure Container Registry privada
> * Enviar uma imagem de contentor para o registo
> * Criar ficheiros de parâmetros e de modelo do RM
> * Implementar uma aplicação no Service Fabric Mesh

Nesta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * Implementar uma aplicação no Service Fabric Mesh com um modelo
> * [Dimensionar serviços numa aplicação em execução no Service Fabric Mesh](service-fabric-mesh-tutorial-template-scale-services.md)
> * [Atualizar uma aplicação em execução no Service Fabric Mesh](service-fabric-mesh-tutorial-template-upgrade-app.md)
> * [Remover uma aplicação](service-fabric-mesh-tutorial-template-remove-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* [Instalar o Docker](service-fabric-mesh-howto-setup-developer-environment-sdk.md#install-docker)

* [Instale a CLI do Azure e a CLI do Service Fabric Mesh localmente](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli).

## <a name="create-a-container-registry"></a>Criar um registo de contentores

As imagens de contentor associadas aos serviços na sua aplicação Service Fabric Mesh têm de ser armazenadas num registo de contentor.  Este tutorial utiliza uma instância privada do Azure Container Registry (ACR). 

Utilize os seguintes passos para criar uma instância do ACR.  Se já tiver uma configuração de uma instância do ACR, pode avançar.

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Azure e defina a subscrição ativa.

```azurecli
az login
az account set --subscription "<subscriptionName>"
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Utilize o comando seguinte para criar um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

### <a name="create-the-container-registry"></a>Criar o registo de contentor

Crie uma instância do ACR com o comando `az acr create`. O nome do registo tem de ser exclusivo no Azure e conter de 5 a 50 carateres alfanuméricos. No exemplo seguinte, é utilizado o nome *myContainerRegistry*. Se obtiver um erro a indicar que o nome do registo já está em utilização, escolha um nome diferente.

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry --sku Basic
```

Quando o registo é criado, o resultado é semelhante ao seguinte:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2018-09-13T19:43:57.388203+00:00",
  "id": "/subscriptions/<subscription>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry",
  "location": "eastus",
  "loginServer": "mycontainerregistry.azurecr.io",
  "name": "myContainerRegistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

## <a name="push-the-images-to-azure-container-registry"></a>Enviar as imagens para o Azure Container Registry

Este tutorial utiliza a aplicação de Lista de Tarefas como um exemplo.  O contentor de imagens para os serviços [WebFrontEnd](https://hub.docker.com/r/seabreeze/azure-mesh-todo-webfrontend/) e [ToDoService](https://hub.docker.com/r/seabreeze/azure-mesh-todo-service/) podem ser encontrados no Hub do Docker. Ver [criar uma aplicação de web Service Fabric em malha](service-fabric-mesh-tutorial-create-dotnetcore.md) para obter informações sobre como criar a aplicação no Visual Studio. O Service Fabric Mesh pode executar contentores do Docker do Windows ou Linux.  Se estiver a trabalhar com contentores do Linux, selecione **Mudar para contentores do Linux** no Docker.  Se estiver a trabalhar com contentores do Windows, selecione **Mudar para contentores do Windows** no Docker.

Para enviar uma imagem para uma instância do ACR, primeiro tem de ter uma imagem de contentor. Se ainda não tiver quaisquer imagens de contentor locais, utilize o comando [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) para extrair as imagens do [WebFrontEnd](https://hub.docker.com/r/seabreeze/azure-mesh-todo-webfrontend/) e [ToDoService](https://hub.docker.com/r/seabreeze/azure-mesh-todo-service/) do Hub do Docker.

Extraia as imagens do Windows:

```bash
docker pull seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709
docker pull seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709
```

Antes de emitir uma imagem para o registo, tem de o etiquetar com o nome completamente qualificado do seu servidor de início de sessão ACR.

Execute o seguinte comando para obter o nome do servidor de início de sessão completa da instância do ACR.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table

AcrLoginServer
---------------------------------
mycontainerregistry.azurecr.io
```

Agora, coloque uma etiqueta na imagem do Docker com o comando [etiqueta do docker](https://docs.docker.com/engine/reference/commandline/tag/). O exemplo a seguir coloca as etiquetas nas imagens seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709 e seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709. Se estiver a utilizar imagens diferentes, substitua os nomes dessas imagens no comando seguinte.

```bash
docker tag seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709 mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709
docker tag seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709 mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709
```

Inicie sessão no Azure Container Registry.

```azurecli
az acr login -n myContainerRegistry
```

Utilize o comando [docker push](https://docs.docker.com/engine/reference/commandline/push/) para enviar a imagem para a instância do ACR:

```bash
docker push mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709
docker push mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709
```

### <a name="list-container-images"></a>Listar imagens de contentor

Para ver os repositórios na sua instância do ACR, execute o seguinte:

```azurecli
az acr repository list --name myContainerRegistry --output table

Result
-------------------------------
seabreeze/azure-mesh-todo-webfrontend
seabreeze/azure-mesh-todo-service
```

O exemplo seguinte lista as etiquetas no repositório **azure-mesh-todo-service**.

```azurecli
az acr repository show-tags --name myContainerRegistry --repository seabreeze/azure-mesh-todo-service --output table

Result
--------
1.0-nanoserver-1709
```

O resultado anterior confirma a presença de `azure-mesh-todo-service:1.0-nanoserver-1709` no registo de contentor privado.  Verifique também a presença de `azure-mesh-todo-webfrontend:1.0-nanoserver-1709`.

## <a name="retrieve-credentials-for-the-registry"></a>Obter credenciais para o registo

> [!IMPORTANT]
> Ativar o utilizador administrador numa instância do ACR não é recomendado para cenários de produção. Essa ação é realizada aqui para sua comodidade. Para cenários de produção, utilize um [principal de serviço](https://docs.microsoft.com/azure/container-registry/container-registry-auth-service-principal) para autenticação do utilizador e do sistema em cenários de produção.

Para implementar uma instância de contentor do registo que criou com um modelo, tem de indicar as credenciais de registo durante a implementação. Em primeiro lugar, ative o utilizador administrador no seu registo com o seguinte comando:

```azurecli
az acr update --name myContainerRegistry --admin-enabled true
```

Em seguida, obtenha o nome do servidor de início de sessão do registo, o nome de utilizador e a palavra-passe com os seguintes comandos:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
az acr credential show --name myContainerRegistry --query username
az acr credential show --name myContainerRegistry --query "passwords[0].value"
```

Utilize os valores do nome do servidor de início de sessão do ACR devolvido, do nome de utilizador e da palavra-passe quando criar os ficheiros de parâmetros e de modelo do RM na secção seguinte.

## <a name="download-and-explore-the-template-and-parameters-files"></a>Transferir e explorar os ficheiros de parâmetros e de modelo

Uma aplicação do Service Fabric Mesh é um recurso do Azure que pode implementar e gerir através de modelos do Azure Resource Manager (RM). Se não estiver familiarizado com os conceitos de implementar e gerir as suas soluções do Azure, veja [Descrição geral do Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) e [Understand the structure and syntax of RM Templates](/azure/azure-resource-manager/resource-group-authoring-templates) (Compreender a estrutura e a sintaxe dos Modelos do RM).

Este tutorial utiliza a aplicação de Lista de Tarefas como um exemplo.  Em vez de criar novos ficheiros de parâmetros e de modelo, transfira os ficheiros [de modelo de implementação de mesh_rp.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) e [de parâmetros de mesh_rp.windows.parameter.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json).

### <a name="parameters"></a>Parâmetros
Quando tiver valores no seu modelo que prevê alterar assim que a aplicação for implementada ou se gostaria de ter a opção para alterar numa base por implementação (se planear reutilizar este modelo para outras implementações), a prática recomendada é parametrizar os valores. A maneira certa de o fazer é criar uma secção "parâmetros" na parte superior do seu modelo de implementação, onde especifica os nomes e propriedades de parâmetros, que são referenciados mais tarde no modelo de implementação. Cada definição de parâmetro inclui *tipo*, *defaultValue* e uma secção opcional *metadados* com uma *descrição*.

A secção de parâmetros é definida na parte superior do seu modelo de implementação, mesmo antes da secção *recursos*:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      ...
    },
    "resources": [
```

Na [modelo de implementação de mesh_rp.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json), os seguintes parâmetros são declarados: localização, registryPassword, registryUserName, registryServer, frontEndImage, serviceImage, frontEndCpu, serviceCpu, frontEndMemory, serviceMemory, frontEndReplicaCount, serviceReplicaCount.  As descrições para cada parâmetro podem ser encontradas no ficheiro de modelo de implementação.

Esses parâmetros são utilizados no ficheiro de [parâmetros de mesh_rp.windows.parameter.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json).  A utilização de um ficheiro de parâmetros separado dá-lhe a capacidade de alterar os valores de parâmetros de implementação em implementação sem atualizar o próprio modelo de implementação.

### <a name="overview-of-the-application-and-services"></a>Descrição geral da aplicação e dos serviços

Os serviços são especificados no modelo como propriedades do recurso de aplicação.  As aplicações são implementadas numa rede privada, que é declarada como um recurso no modelo.  Os serviços podem utilizar volumes para manter os dados, que são declarados como recursos no modelo.  Para cada serviço, o tipo de SO, os pacotes do código, o número de réplicas e a rede são especificados como propriedades do serviço.  Para cada pacote do código, especifique a imagem de contentor, os pontos finais, os recursos de memória e CPU e as credenciais do repositório de imagens. Num alto nível, o modelo para uma aplicação Service Fabric Mesh com vários serviços é semelhante a:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
  },
  "resources": [
    {
      "apiVersion": "2018-09-01-preview",
      "name": "MyMeshApplication",
      "type": "Microsoft.ServiceFabricMesh/applications",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/networks/MeshAppNetwork",
        "Microsoft.ServiceFabricMesh/volumes/ServiceAVolume"
      ],
      "properties": {
        "services": [
          {
            "name": "ServiceA",
            "properties": {
              "description": "ServiceA description.",
              "osType": "Linux",
              "codePackages": [
                {
                  "name": "ServiceA",
                  "image": "[parameters('frontEndImage')]",
                  "volumeRefs": [
                    {
                      "name": "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'ServiceAVolume')]",
                      "destinationPath": "/app/data"
                    }
                  ],
                  "endpoints": [
                    {
                      "name": "ServiceAListener",
                      "port": 20001
                    }
                  ],
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
              "replicaCount": "[parameters('frontEndReplicaCount')]",
              "networkRefs": [
                {
                  "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'MeshAppNetwork')]"
                }
              ]
            }
          },
          {
            "name": "ServiceB",
            ...
          }
        ],
        "description": "Application description."
      }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "MeshAppNetwork",
      "type": "Microsoft.ServiceFabricMesh/networks",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "MeshAppNetwork description.",
        "addressPrefix": "10.0.0.4/22",
        "ingressConfig": {
          "layer4": [
            {
              "name": "ServiceAIngress",
              "publicPort": "20001",
              "applicationName": "MyMeshApplication",
              "serviceName": "ServiceA",
              "endpointName": "ServiceAListener"
            }
          ]
        }
      }
    },
    {
      "apiVersion": "2018-09-01-preview",
      "name": "ServiceAVolume",
      "type": "Microsoft.ServiceFabricMesh/volumes",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "Azure Files storage volume for counter App.",
        "provider": "SFAzureFile",
        "azureFileParameters": {
          "shareName": "[parameters('fileShareName')]",
          "accountName": "[parameters('storageAccountName')]",
          "accountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
  ]
}
```

Veja o ficheiro [de modelo de implementação de mesh_rp.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) para obter as especificações da aplicação de Lista de Tarefas.

## <a name="deploy-the-application-to-service-fabric-mesh"></a>Implementar a aplicação no Service Fabric Mesh
Crie a aplicação e os recursos relacionados com o seguinte comando e indique as credenciais do passo anterior [Obter credenciais para o registo](#retrieve-credentials-for-the-registry).

No ficheiro de parâmetros, atualize os valores dos parâmetros seguintes:

|Parâmetro|Value|
|---|---|
|localização|A região na qual quer implementar a aplicação.  Por exemplo, "eualeste".|
|registryPassword|A palavra-passe que obteve anteriormente em [Obter credenciais para o registo](#retrieve-credentials-for-the-registry). Este parâmetro no modelo é uma cadeia segura e não será apresentado no estado de implementação nem nos comandos `az mesh service show`.|
|registryUserName|O nome de utilizador que obteve em [Obter credenciais para o registo](#retrieve-credentials-for-the-registry).|
|registryServer|O nome do servidor do registo que obteve em [Obter credenciais para o registo](#retrieve-credentials-for-the-registry).|
|frontEndImage|A imagem de contentor para o serviço de front-end.  Por exemplo, "<myregistry>.azurecr.io/seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709".|
|serviceImage|A imagem de contentor para o serviço de back-end.  Por exemplo, "<myregistry>.azurecr.io/seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709".|

Para implementar a aplicação, execute o seguinte:

```azurecli
az mesh deployment create --resource-group myResourceGroup --template-file c:\temp\mesh_rp.windows.json --parameters c:\temp\mesh_rp.windows.parameters.json
```

Este comando irá produzir um fragmento JSON, que é mostrado abaixo. Sob o ```outputs``` seção da saída do JSON, copiar o ```publicIPAddress``` propriedade.

```json
"outputs": {
    "publicIPAddress": {
    "type": "String",
    "value": "40.83.78.216"
    }
}
```

Estas informações provêm do ```outputs``` secção no modelo de ARM. Conforme mostrado abaixo, esta secção referencia o recurso de Gateway para obter o endereço IP público. 

```json
  "outputs": {
    "publicIPAddress": {
      "value": "[reference('todolistappGateway').ipAddress]",
      "type": "string"
    }
  }
```

## <a name="open-the-application"></a>Abrir a aplicação

Assim que a aplicação for implementada com êxito, obtenha o endereço IP público para o ponto final de serviço. O comando de implementação devolve o endereço IP público do ponto final de serviço. Opcionalmente, também pode consultar o recurso de rede para encontrar o endereço IP público do ponto final de serviço. O nome do recurso de rede para esta aplicação é `todolistappNetwork`. Obtenha informações sobre o mesmo usando o seguinte comando. 

```azurecli
az mesh gateway show --resource-group myResourceGroup --name todolistappGateway
```

Navegue para o endereço IP num browser.

## <a name="check-application-status"></a>Verificar o estado da aplicação

Pode verificar o estado da aplicação com o comando app show. O nome da aplicação para a aplicação implementada é "todolistapp", por isso, obtenha os respetivos detalhes.

```azurecli
az mesh app show --resource-group myResourceGroup --name todolistapp
```

Examine os registos da aplicação implementada com o comando `az mesh code-package-log get`:
```azurecli
az mesh code-package-log get --resource-group myResourceGroup --application-name todolistapp --service-name WebFrontEnd --replica-name 0 --code-package-name WebFrontEnd
```

## <a name="next-steps"></a>Passos Seguintes

Nesta parte do tutorial, ficou a saber como:

> [!div class="checklist"]
> * Crie um registo de contentor privado
> * Enviar uma imagem de contentor para o registo
> * Criar ficheiros de parâmetros e de modelo
> * Implementar uma aplicação no Service Fabric Mesh

Avance para o tutorial seguinte:
> [!div class="nextstepaction"]
> [Dimensionar uma aplicação em execução no Service Fabric Mesh](service-fabric-mesh-tutorial-template-scale-services.md)
