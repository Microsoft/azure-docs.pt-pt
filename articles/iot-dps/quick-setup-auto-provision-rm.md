---
title: Quickstart - Configurar o Provisionamento do Dispositivo Azure IoT Hub usando o modelo do Gestor de Recursos Azure
description: Azure quickstart - Configurar o Serviço de Provisionamento de Dispositivos Azure IoT Hub (DPS) usando um modelo
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 91e4e7de73d820c345b2973896d07d3479e49f9e
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92748081"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-an-azure-resource-manager-template"></a>Quickstart: Configurar o serviço de fornecimento de dispositivos IoT Hub com um modelo de Gestor de Recursos Azure

Pode utilizar o [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) para configurar programaticamente os recursos de cloud do Azure necessários para aprovisionar os seus dispositivos. Estes passos mostram como criar um hub IoT e um novo serviço de provisionamento de dispositivos IoT Hub, e ligar os dois serviços em conjunto usando um modelo de Gestor de Recursos Azure. Este quickstart utiliza [o Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli) para executar os passos programáticos necessários para criar um grupo de recursos e implementar o modelo, mas pode facilmente utilizar o [portal Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal), [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy), .NET, Ruby ou outras linguagens de programação para executar estes passos e implementar o seu modelo. 


## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Este quickstart requer que você executar o Azure CLI localmente. Tem de ter instalada a versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar a CLI, veja [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).


## <a name="sign-in-to-azure-and-create-a-resource-group"></a>Iniciar sessão e criar um grupo de recursos

Inicie sessão na sua conta do Azure e selecione a sua subscrição.

1. Na linha de comandos, execute o [comando login][lnk-login-command]:
    
    ```azurecli
    az login
    ```

    Siga as instruções para se autenticar com o código e inicie sessão na sua conta do Azure através de um browser.

2. Se tiver várias subscrições do Azure, iniciar sessão no Azure dá-lhe acesso a todas as contas do Azure associadas às suas credenciais. Utilize o comando [ para listar as contas do Azure][lnk-az-account-command] disponíveis e que pode utilizar:
    
    ```azurecli
    az account list 
    ```

    Utilize o comando seguinte para selecionar a subscrição que pretende utilizar para executar os comandos para criar o seu hub IoT. Pode utilizar o nome ou o ID da subscrição da saída do comando anterior:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

3. Quando cria recursos de cloud do Azure, como os hubs IoT e os serviços de aprovisionamento, cria-os em grupos de recursos separados. Utilize um grupo de recursos existente ou execute o comando [ para criar um grupo de recursos][lnk-az-resource-command]:
    
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

2. Substitua a secção **parameters** pelo conteúdo seguinte. A secção de parâmetros define parâmetros cujos valores podem ser transmitidos a partir de outro ficheiro. Esta secção define o nome do hub IoT e serviço de fornecimento para criar. Também define a localização tanto para o hub IoT como para o serviço de fornecimento. Os valores serão restringidos às regiões de Azure que apoiam os centros de IoT e os serviços de prestação de serviços. Para obter uma lista das localizações suportadas para o Serviço Aprovisionamento de Dispositivos, pode executar o comando `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` ou aceder à página [Estado do Azure](https://azure.microsoft.com/status/) e procure “Serviço Aprovisionamento de Dispositivos”.

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

4. Para criar um hub IoT, adicione as seguintes linhas à coleção **resources** . O JSON especifica as propriedades mínimas necessárias para criar um hub IoT. Os **valores de nome** e **localização** serão passados como parâmetros de outro ficheiro. Para saber mais sobre as propriedades que pode especificar para um hub IoT num modelo, consulte [a referência do modelo Microsoft.Devices/IotHubs](https://docs.microsoft.com/azure/templates/microsoft.devices/iothubs).

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

5. Para criar o serviço de aprovisionamento, adicione as seguintes linhas a seguir à especificação do hub IoT na coleção **resources** . O **nome** e **a localização** do serviço de prestação serão passados como parâmetros. A coleção **iotHubs** especifica os hubs IoT para ligar ao serviço de fornecimento. No mínimo, tem de especificar as propriedades **connectionString** e **location** de cada hub IoT associado. Também pode definir outras propriedades, como **allocationWeight** e **applyAllocationPolicy** em cada hub IoT ou **allocationPolicy** e **authorizationPolicies** no serviço de aprovisionamento. Para saber mais, veja [Microsoft.Devices/provisioningServices template reference](https://docs.microsoft.com/azure/templates/microsoft.devices/provisioningservices) (Referência ao modelo Microsoft.Devices/provisioningServices).

   A propriedade **dependsOn** é utilizada para garantir que o Resource Manager cria o hub IoT antes de criar o serviço de aprovisionamento. O modelo requer que a cadeia de ligação do hub IoT especifique a associação ao serviço de aprovisionamento, pelo que o hub e as chaves do mesmo têm de ser criados antes. O modelo utiliza funções como **concat** e **listKeys** para criar a cadeia de ligação a partir de variáveis parametrizadas. Para saber mais, veja [Azure Resource Manager template functions](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions) (Funções de modelos do Azure Resource Manager).

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

O modelo que definiu no último passo utiliza parâmetros para especificar o nome do hub IoT, o nome do serviço de fornecimento e a localização (região de Azure) para criá-los. Você transmite estes parâmetros para o modelo a partir de um ficheiro separado. Desta forma, pode utilizar o mesmo modelo para várias implementações. Para criar o ficheiro de parâmetros, siga estes passos:

1. Utilize um editor de texto para criar um ficheiro de parâmetros do Azure Resource Manager com o nome **parameters.json** e o conteúdo de estrutura abaixo: 

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {}
       }
   }
   ```

2. Adicione o valor **iotHubName** à secção “parameters”.  Um nome de hub IoT deve ser globalmente único em Azure, por isso você pode querer adicionar um prefixo ou sufixo único ao nome de exemplo, ou escolher um novo nome completamente. Certifique-se de que o seu nome segue as convenções de nomeação adequadas para um hub IoT: deve ter 3-50 caracteres de comprimento e pode conter apenas caracteres alfanuméricos ou hífenes de caixa superior ou inferior ('-'). 

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
    }
   
   ```

3. Adicione o valor **provisioningServiceName** à secção “parameters”. Também terá de escolher um nome globalmente único para o seu serviço de fornecimento. Certifique-se de que segue as convenções de nomeação adequadas para um serviço de fornecimento de dispositivos IoT Hub: deve ter 3-64 caracteres de comprimento e pode conter apenas caracteres alfanuméricos ou hífenes maiúsitos superiores ou inferiores ('-').

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

4. Adicione o valor **hubLocation** à secção “parameters”. Este valor especifica a localização do hub IoT e do serviço de aprovisionamento. O valor tem de corresponder a uma das localizações especificadas na coleção **allowedValues** na definição de parâmetros no ficheiro do modelo. Esta coleção limita os valores a localizações do Azure que suportem, quer os hubs IoT, quer os serviços de aprovisionamento. Para obter uma lista de locais suportados para o Serviço de Provisionamento de Dispositivos, pode executar o `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` comando, ou ir à página [do Estado do Azure](https://azure.microsoft.com/status/) e pesquisar em "Serviço de Provisionamento de Dispositivos".

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

1. Para implementar o seu modelo, navegue na pasta que contém os ficheiros do modelo e dos parâmetros e execute o seguinte [comando para iniciar uma implementação](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create&preserve-view=true):
    
    ```azurecli
     az group deployment create -g {your resource group name} --template-file template.json --parameters @parameters.json
    ```

   Esta operação pode demorar alguns minutos a ser concluída. Uma vez feito, procure a propriedade do **Estado de provisionamento** que mostra "Bem sucedido" na saída. 

   ![Saída do aprovisionamento](./media/quick-setup-auto-provision-rm/output.png) 


2. Para verificar a implementação, execute o comando [ para listar os recursos ](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-list&preserve-view=true) e procure o serviço de aprovisionamento e o hub IoT novos na saída:

    ```azurecli
     az resource list -g {your resource group name}
    ```


## <a name="clean-up-resources"></a>Limpar os recursos

Outros guias de introdução desta coleção têm por base este guia de introdução. Se pretender continuar a trabalhar com guias de introdução subsequentes ou com os tutoriais, não limpe os recursos criados neste guia de introdução. Se não pretender continuar, pode utilizar o CLI do Azure para [eliminar um recurso individual][lnk-az-resource-command], como um hub IoT ou um serviço de fornecimento, ou para eliminar um grupo de recursos e todos os seus recursos.

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

Também pode eliminar grupos de recursos e recursos individuais utilizando o portal Azure, PowerShell ou REST APIs, bem como com SDKs de plataforma suportada publicados para O Azure Resource Manager ou IoT Hub Device Provisioning Service.

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, você implantou um hub IoT e uma instância de Serviço de Provisionamento de Dispositivos, e ligou os dois recursos. Para aprender a usar esta configuração para forrá um dispositivo simulado, continue até ao início rápido para criar um dispositivo simulado.

> [!div class="nextstepaction"]
> [Quickstart para criar um dispositivo simulado](./quick-create-simulated-device.md)


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
