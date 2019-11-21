---
title: Set up Azure IoT Hub Device Provisioning using Azure Resource Manager template
description: Azure quickstart - Set up the Azure IoT Hub Device Provisioning Service using a template
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 9a763a492496cef0f01468dc4b655a61e91a2a53
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229476"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-an-azure-resource-manager-template"></a>Quickstart: Set up the IoT Hub Device Provisioning Service with an Azure Resource Manager template

Pode utilizar o [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) para configurar programaticamente os recursos de cloud do Azure necessários para aprovisionar os seus dispositivos. These steps show how to create an IoT hub and a new IoT Hub Device Provisioning Service, and link the two services together using an Azure Resource Manager template. This quickstart uses [Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli) to perform the programmatic steps necessary to create a resource group and deploy the template, but you can easily use the [Azure portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal), [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy), .NET, Ruby, or other programming languages to perform these steps and deploy your template. 


## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- This quickstart requires that you run the Azure CLI locally. Tem de ter instalada a versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar a CLI, veja [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).


## <a name="sign-in-to-azure-and-create-a-resource-group"></a>Iniciar sessão e criar um grupo de recursos

Inicie sessão na sua conta do Azure e selecione a sua subscrição.

1. At the command prompt, run the [login command][lnk-login-command]:
    
    ```azurecli
    az login
    ```

    Siga as instruções para se autenticar com o código e inicie sessão na sua conta do Azure através de um browser.

2. Se tiver várias subscrições do Azure, iniciar sessão no Azure dá-lhe acesso a todas as contas do Azure associadas às suas credenciais. Use the following [command to list the Azure accounts][lnk-az-account-command] available for you to use:
    
    ```azurecli
    az account list 
    ```

    Utilize o comando seguinte para selecionar a subscrição que pretende utilizar para executar os comandos para criar o seu hub IoT. Pode utilizar o nome ou o ID da subscrição da saída do comando anterior:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

3. Quando cria recursos de cloud do Azure, como os hubs IoT e os serviços de aprovisionamento, cria-os em grupos de recursos separados. Either use an existing resource group, or run the following [command to create a resource group][lnk-az-resource-command]:
    
    ```azurecli
     az group create --name {your resource group name} --location westus
    ```

    > [!TIP]
    > O exemplo anterior cria o grupo de recursos na localização E.U.A. Oeste. Pode executar o comando `az account list-locations -o table` para ver uma lista das localizações disponíveis.
    >
    >

## <a name="create-a-resource-manager-template"></a>Criar um modelo do Resource Manager

Utilize um modelo JSON para criar um serviço de aprovisionamento e um hub IoT associado no seu grupo de recursos. Também pode utilizar um modelo do Azure Resource Manager para fazer alterações a um serviço de aprovisionamento ou a um hub IoT já existentes.

1. Utilize um editor de texto para criar um modelo do Azure Resource Manager com o nome **template.json** e o conteúdo de estrutura abaixo: 

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "variables": {},
       "resources": []
   }
   ```

2. Substitua a secção **parameters** pelo conteúdo seguinte. A secção “parameters” especifica os parâmetros que podem ser transmitidos a partir de outro ficheiro. Esta secção especifica o nome do hub IoT e do serviço de aprovisionamento que vão ser criados. Também especifica a localização dos mesmos. Os valores estão limitados a regiões do Azure que suportam os hubs IoT e os serviços de aprovisionamento. Para obter uma lista das localizações suportadas para o Serviço Aprovisionamento de Dispositivos, pode executar o comando `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` ou aceder à página [Estado do Azure](https://azure.microsoft.com/status/) e procure “Serviço Aprovisionamento de Dispositivos”.

   ```json
    "parameters": {
        "iotHubName": {
            "type": "string"
        },
        "provisioningServiceName": {
            "type": "string"
        },
        "hubLocation": {
            "type": "string",
            "allowedValues": [
                "eastus",
                "westus",
                "westeurope",
                "northeurope",
                "southeastasia",
                "eastasia"
            ]
        }
    },

   ```

3. Substitua a secção **variables** pelo conteúdo seguinte. Esta secção define os valores que vão ser utilizados mais tarde para construir a cadeia de ligação do hub IoT, que é necessária para associá-lo ao serviço de aprovisionamento. 
 
   ```json
    "variables": {        
        "iotHubResourceId": "[resourceId('Microsoft.Devices/Iothubs', parameters('iotHubName'))]",
        "iotHubKeyName": "iothubowner",
        "iotHubKeyResource": "[resourceId('Microsoft.Devices/Iothubs/Iothubkeys', parameters('iotHubName'), variables('iotHubKeyName'))]"
    },

   ```

4. Para criar um hub IoT, adicione as seguintes linhas à coleção **resources**. The JSON specifies the minimum properties required to create an IoT hub. As propriedades **name** e **location** são transmitidas como parâmetros. To learn more about the properties you can specify for an IoT hub in a template, see [Microsoft.Devices/IotHubs template reference](https://docs.microsoft.com/azure/templates/microsoft.devices/iothubs).

   ```json
        {
            "apiVersion": "2017-07-01",
            "type": "Microsoft.Devices/IotHubs",
            "name": "[parameters('iotHubName')]",
            "location": "[parameters('hubLocation')]",
            "sku": {
                "name": "S1",
                "capacity": 1
            },
            "tags": {
            },
            "properties": {
            }            
        },

   ``` 

5. Para criar o serviço de aprovisionamento, adicione as seguintes linhas a seguir à especificação do hub IoT na coleção **resources**. As propriedades **name** e **location** do serviço de aprovisionamento são transmitidas como parâmetros. Especifique os hubs IoT que vão ser associados ao serviço de aprovisionamento na coleção **iotHubs**. No mínimo, tem de especificar as propriedades **connectionString** e **location** de cada hub IoT associado. Também pode definir outras propriedades, como **allocationWeight** e **applyAllocationPolicy** em cada hub IoT ou **allocationPolicy** e  **authorizationPolicies** no serviço de aprovisionamento. Para saber mais, veja [Microsoft.Devices/provisioningServices template reference](https://docs.microsoft.com/azure/templates/microsoft.devices/provisioningservices) (Referência ao modelo Microsoft.Devices/provisioningServices).

   A propriedade **dependsOn** é utilizada para garantir que o Resource Manager cria o hub IoT antes de criar o serviço de aprovisionamento. O modelo requer que a cadeia de ligação do hub IoT especifique a associação ao serviço de aprovisionamento, pelo que o hub e as chaves do mesmo têm de ser criados antes. O modelo utiliza funções como **concat** e **listKeys** para criar a cadeia de ligação. Para saber mais, veja [Azure Resource Manager template functions](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions) (Funções de modelos do Azure Resource Manager).

   ```json
        {
            "type": "Microsoft.Devices/provisioningServices",
            "sku": {
                "name": "S1",
                "capacity": 1
            },
            "name": "[parameters('provisioningServiceName')]",
            "apiVersion": "2017-11-15",
            "location": "[parameters('hubLocation')]",
            "tags": {},
            "properties": {
                "iotHubs": [
                    {
                        "connectionString": "[concat('HostName=', reference(variables('iotHubResourceId')).hostName, ';SharedAccessKeyName=', variables('iotHubKeyName'), ';SharedAccessKey=', listkeys(variables('iotHubKeyResource'), '2017-07-01').primaryKey)]",
                        "location": "[parameters('hubLocation')]",
                        "name": "[concat(parameters('iotHubName'),'.azure-devices.net')]"
                    }
                ]
            },
            "dependsOn": ["[parameters('iotHubName')]"]
        }

   ```

6. Guarde o ficheiro do modelo. O modelo concluído deve ter o seguinte aspeto:

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "iotHubName": {
               "type": "string"
           },
           "provisioningServiceName": {
               "type": "string"
           },
           "hubLocation": {
               "type": "string",
               "allowedValues": [
                   "eastus",
                   "westus",
                   "westeurope",
                   "northeurope",
                   "southeastasia",
                   "eastasia"
               ]
           }
       },
       "variables": {        
           "iotHubResourceId": "[resourceId('Microsoft.Devices/Iothubs', parameters('iotHubName'))]",
           "iotHubKeyName": "iothubowner",
           "iotHubKeyResource": "[resourceId('Microsoft.Devices/Iothubs/Iothubkeys', parameters('iotHubName'), variables('iotHubKeyName'))]"
       },
       "resources": [
           {
               "apiVersion": "2017-07-01",
               "type": "Microsoft.Devices/IotHubs",
               "name": "[parameters('iotHubName')]",
               "location": "[parameters('hubLocation')]",
               "sku": {
                   "name": "S1",
                   "capacity": 1
               },
               "tags": {
               },
               "properties": {
               }            
           },
           {
               "type": "Microsoft.Devices/provisioningServices",
               "sku": {
                   "name": "S1",
                   "capacity": 1
               },
               "name": "[parameters('provisioningServiceName')]",
               "apiVersion": "2017-11-15",
               "location": "[parameters('hubLocation')]",
               "tags": {},
               "properties": {
                   "iotHubs": [
                       {
                           "connectionString": "[concat('HostName=', reference(variables('iotHubResourceId')).hostName, ';SharedAccessKeyName=', variables('iotHubKeyName'), ';SharedAccessKey=', listkeys(variables('iotHubKeyResource'), '2017-07-01').primaryKey)]",
                           "location": "[parameters('hubLocation')]",
                           "name": "[concat(parameters('iotHubName'),'.azure-devices.net')]"
                       }
                   ]
               },
               "dependsOn": ["[parameters('iotHubName')]"]
           }
       ]
   }
   ```

## <a name="create-a-resource-manager-parameter-file"></a>Criar um ficheiro de parâmetros do Resource Manager

The template that you defined in the last step uses parameters to specify the name of the IoT hub, the name of the provisioning service, and the location (Azure region) to create them. Estes parâmetros são transmitidos num ficheiro à parte. Desta forma, pode utilizar o mesmo modelo para várias implementações. Para criar o ficheiro de parâmetros, siga estes passos:

1. Utilize um editor de texto para criar um ficheiro de parâmetros do Azure Resource Manager com o nome **parameters.json** e o conteúdo de estrutura abaixo: 

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {}
       }
   }
   ```

2. Adicione o valor **iotHubName** à secção “parameters”.  An IoT hub name must be globally unique in Azure, so you may want to add a unique prefix or suffix to the example name, or choose a new name altogether. Make sure your name follows proper naming conventions for an IoT hub: it should be 3-50 characters in length, and can contain only upper or lower case alphanumeric characters or hyphens ('-'). 

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
    }
   
   ```

3. Adicione o valor **provisioningServiceName** à secção “parameters”. You will also need to choose a globally unique name for your provisioning service. Make sure it follows proper naming conventions for an IoT Hub Device Provisioning Service: it should be 3-64 characters in length and can contain only upper or lower case alphanumeric characters or hyphens ('-').

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
        "provisioningServiceName": {
            "value": "my-sample-provisioning-service"
        },
    }

   ```

4. Adicione o valor **hubLocation** à secção “parameters”. Este valor especifica a localização do hub IoT e do serviço de aprovisionamento. O valor tem de corresponder a uma das localizações especificadas na coleção **allowedValues** na definição de parâmetros no ficheiro do modelo. Esta coleção limita os valores a localizações do Azure que suportem, quer os hubs IoT, quer os serviços de aprovisionamento. Para obter uma lista das localizações suportadas para o Serviço Aprovisionamento de Dispositivos, pode executar o comando `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` ou aceder à página [Estado do Azure](https://azure.microsoft.com/status/) e procure “Serviço Aprovisionamento de Dispositivos”.

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
        "provisioningServiceName": {
            "value": "my-sample-provisioning-service"
        },
        "hubLocation": {
            "value": "westus"
        }
    }

   ```

5. Guarde o ficheiro. 


> [!IMPORTANT]
> Tanto o hub IoT, como o serviço de aprovisionamento, serão detetáveis publicamente como pontos finais de DNS, pelo que deve evitar informações confidenciais quando lhes der um nome.
>

## <a name="deploy-the-template"></a>Implementar o modelo

Utilize os seguintes comandos da CLI do Azure para implementar os modelos e verificar a implementação.

1. Para implementar o modelo, execute o comando [ para iniciar uma implementação](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create):
    
    ```azurecli
     az group deployment create -g {your resource group name} --template-file template.json --parameters @parameters.json
    ```

   Procure a propriedade **provisioningState** definida como "Succeeded" (“Êxito”) na saída. 

   ![Saída do aprovisionamento](./media/quick-setup-auto-provision-rm/output.png) 


2. Para verificar a implementação, execute o comando [ para listar os recursos ](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-list) e procure o serviço de aprovisionamento e o hub IoT novos na saída:

    ```azurecli
     az resource list -g {your resource group name}
    ```


## <a name="clean-up-resources"></a>Limpar recursos

Outros guias de introdução desta coleção têm por base este guia de introdução. Se pretender continuar a trabalhar com guias de introdução subsequentes ou com os tutoriais, não limpe os recursos criados neste guia de introdução. If you do not plan to continue, you can use the Azure CLI to [delete an individual resource][lnk-az-resource-command], such as an IoT hub or a provisioning service, or to delete a resource group and all of its resources.

Para eliminar o serviço de aprovisionamento, execute o seguinte comando:

```azurecli
az iot hub delete --name {your provisioning service name} --resource-group {your resource group name}
```
Para eliminar um hub IoT, execute o seguinte comando:

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

Para eliminar um grupo de recursos e todos os respetivos recursos, execute o seguinte comando:

```azurecli
az group delete --name {your resource group name}
```

Também pode eliminar grupos de recursos e recursos individuais através do portal do Azure, do PowerShell, das APIs REST ou dos SDKs de plataforma suportados e publicados para o Azure Resource Manager ou o Serviço de Aprovisionamento de Dispositivos no Hub IoT.

## <a name="next-steps"></a>Passos seguintes

In this quickstart, you’ve deployed an IoT hub and a Device Provisioning Service instance, and linked the two resources. To learn how to use this setup to provision a simulated device, continue to the quickstart for creating a simulated device.

> [!div class="nextstepaction"]
> [Quickstart to create a simulated device](./quick-create-simulated-device.md)


<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-CLI-install]: https://docs.microsoft.com/cli/azure/install-az-cli2
[lnk-login-command]: https://docs.microsoft.com/cli/azure/get-started-with-az-cli2
[lnk-az-account-command]: https://docs.microsoft.com/cli/azure/account
[lnk-az-register-command]: https://docs.microsoft.com/cli/azure/provider
[lnk-az-addcomponent-command]: https://docs.microsoft.com/cli/azure/component
[lnk-az-resource-command]: https://docs.microsoft.com/cli/azure/resource
[lnk-az-iot-command]: https://docs.microsoft.com/cli/azure/iot
[lnk-iot-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-devguide]: iot-hub-devguide.md
[lnk-portal]: iot-hub-create-through-portal.md 
