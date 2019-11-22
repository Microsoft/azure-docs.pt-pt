---
title: Configurar o provisionamento de dispositivos no Hub IoT do Azure usando o modelo Azure Resource Manager
description: Início rápido do Azure-configurar o serviço de provisionamento de dispositivos no Hub IoT do Azure usando um modelo
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: b40e126ca23190fbe50a717016b18719be6950e2
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276387"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-an-azure-resource-manager-template"></a>Início rápido: configurar o serviço de provisionamento de dispositivos no Hub IoT com um modelo de Azure Resource Manager

Pode utilizar o [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) para configurar programaticamente os recursos de cloud do Azure necessários para aprovisionar os seus dispositivos. Estas etapas mostram como criar um hub IoT e um novo serviço de provisionamento de dispositivos no Hub IoT e vincular os dois serviços usando um modelo de Azure Resource Manager. Este guia de início rápido usa [CLI do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli) para executar as etapas programáticas necessárias para criar um grupo de recursos e implantar o modelo, mas você pode usar facilmente o [portal do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal), o [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy), o .net, o Ruby ou outras linguagens de programação para executar essas etapas e implantar o modelo. 


## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Este guia de início rápido requer que você execute o CLI do Azure localmente. Tem de ter instalada a versão 2.0 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar a CLI, veja [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).


## <a name="sign-in-to-azure-and-create-a-resource-group"></a>Iniciar sessão e criar um grupo de recursos

Inicie sessão na sua conta do Azure e selecione a sua subscrição.

1. No prompt de comando, execute o [comando de logon][lnk-login-command]:
    
    ```azurecli
    az login
    ```

    Siga as instruções para se autenticar com o código e inicie sessão na sua conta do Azure através de um browser.

2. Se tiver várias subscrições do Azure, iniciar sessão no Azure dá-lhe acesso a todas as contas do Azure associadas às suas credenciais. Use o seguinte [comando para listar as contas do Azure][lnk-az-account-command] disponíveis para uso:
    
    ```azurecli
    az account list 
    ```

    Utilize o comando seguinte para selecionar a subscrição que pretende utilizar para executar os comandos para criar o seu hub IoT. Pode utilizar o nome ou o ID da subscrição da saída do comando anterior:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

3. Quando cria recursos de cloud do Azure, como os hubs IoT e os serviços de aprovisionamento, cria-os em grupos de recursos separados. Use um grupo de recursos existente ou execute o seguinte [comando para criar um grupo de recursos][lnk-az-resource-command]:
    
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

2. Substitua a secção **parameters** pelo conteúdo seguinte. A seção de parâmetros define os parâmetros cujos valores podem ser passados de outro arquivo. Esta seção define o nome do Hub IoT e do serviço de provisionamento a ser criado. Ele também define o local para o Hub IoT e o serviço de provisionamento. Os valores serão restritos a regiões do Azure que dão suporte a hubs IoT e serviços de provisionamento. Para obter uma lista das localizações suportadas para o Serviço Aprovisionamento de Dispositivos, pode executar o comando `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` ou aceder à página [Estado do Azure](https://azure.microsoft.com/status/) e procure “Serviço Aprovisionamento de Dispositivos”.

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

4. Para criar um hub IoT, adicione as seguintes linhas à coleção **resources**. O JSON especifica as propriedades mínimas necessárias para criar um hub IoT. Os valores de **nome** e **local** serão passados como parâmetros de outro arquivo. Para saber mais sobre as propriedades que você pode especificar para um hub IoT em um modelo, consulte [referência de modelo Microsoft. Devices/IotHubs](https://docs.microsoft.com/azure/templates/microsoft.devices/iothubs).

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

5. Para criar o serviço de aprovisionamento, adicione as seguintes linhas a seguir à especificação do hub IoT na coleção **resources**. O **nome** e o **local** do serviço de provisionamento serão passados como parâmetros. A coleção **iotHubs** especifica os hubs IOT a serem vinculados ao serviço de provisionamento. No mínimo, tem de especificar as propriedades **connectionString** e **location** de cada hub IoT associado. Também pode definir outras propriedades, como **allocationWeight** e **applyAllocationPolicy** em cada hub IoT ou **allocationPolicy** e  **authorizationPolicies** no serviço de aprovisionamento. Para saber mais, veja [Microsoft.Devices/provisioningServices template reference](https://docs.microsoft.com/azure/templates/microsoft.devices/provisioningservices) (Referência ao modelo Microsoft.Devices/provisioningServices).

   A propriedade **dependsOn** é utilizada para garantir que o Resource Manager cria o hub IoT antes de criar o serviço de aprovisionamento. O modelo requer que a cadeia de ligação do hub IoT especifique a associação ao serviço de aprovisionamento, pelo que o hub e as chaves do mesmo têm de ser criados antes. O modelo usa funções como **concat** e **listKeys** para criar a cadeia de conexão de variáveis parametrizadas. Para saber mais, veja [Azure Resource Manager template functions](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions) (Funções de modelos do Azure Resource Manager).

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

O modelo que você definiu na última etapa usa parâmetros para especificar o nome do Hub IoT, o nome do serviço de provisionamento e o local (região do Azure) para criá-los. Você passa esses parâmetros para o modelo a partir de um arquivo separado. Desta forma, pode utilizar o mesmo modelo para várias implementações. Para criar o ficheiro de parâmetros, siga estes passos:

1. Utilize um editor de texto para criar um ficheiro de parâmetros do Azure Resource Manager com o nome **parameters.json** e o conteúdo de estrutura abaixo: 

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {}
       }
   }
   ```

2. Adicione o valor **iotHubName** à secção “parameters”.  Um nome de Hub IoT deve ser globalmente exclusivo no Azure, portanto, talvez você queira adicionar um prefixo ou sufixo exclusivo ao nome de exemplo ou escolher um novo nome totalmente. Verifique se seu nome segue as convenções de nomenclatura adequadas para um hub IoT: deve ter de 3-50 caracteres de comprimento e pode conter apenas caracteres alfanuméricos ou hifens ('-'). 

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
    }
   
   ```

3. Adicione o valor **provisioningServiceName** à secção “parameters”. Você também precisará escolher um nome globalmente exclusivo para seu serviço de provisionamento. Verifique se ele segue as convenções de nomenclatura adequadas para um serviço de provisionamento de dispositivos no Hub IoT: deve ter de 3-64 caracteres de comprimento e pode conter apenas caracteres alfanuméricos ou hifens ('-').

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

4. Adicione o valor **hubLocation** à secção “parameters”. Este valor especifica a localização do hub IoT e do serviço de aprovisionamento. O valor tem de corresponder a uma das localizações especificadas na coleção **allowedValues** na definição de parâmetros no ficheiro do modelo. Esta coleção limita os valores a localizações do Azure que suportem, quer os hubs IoT, quer os serviços de aprovisionamento. Para obter uma lista de locais com suporte para o serviço de provisionamento de dispositivos, você pode executar o comando `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table`ou ir para a página de [status do Azure](https://azure.microsoft.com/status/) e Pesquisar "serviço de provisionamento de dispositivos".

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

1. Para implantar seu modelo, navegue até a pasta que contém os arquivos de modelo e parâmetro e execute o seguinte [comando para iniciar uma implantação](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create):
    
    ```azurecli
     az group deployment create -g {your resource group name} --template-file template.json --parameters @parameters.json
    ```

   Esta operação pode levar alguns minutos para ser concluída. Quando terminar, procure a propriedade **provisioningState** mostrando "êxito" na saída. 

   ![Saída do aprovisionamento](./media/quick-setup-auto-provision-rm/output.png) 


2. Para verificar a implementação, execute o comando [ para listar os recursos ](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-list) e procure o serviço de aprovisionamento e o hub IoT novos na saída:

    ```azurecli
     az resource list -g {your resource group name}
    ```


## <a name="clean-up-resources"></a>Limpar recursos

Outros guias de introdução desta coleção têm por base este guia de introdução. Se pretender continuar a trabalhar com guias de introdução subsequentes ou com os tutoriais, não limpe os recursos criados neste guia de introdução. Se você não planeja continuar, pode usar o CLI do Azure para [excluir um recurso individual][lnk-az-resource-command], como um hub IOT ou um serviço de provisionamento, ou para excluir um grupo de recursos e todos os seus recursos.

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

Você também pode excluir grupos de recursos e recursos individuais usando as APIs portal do Azure, PowerShell ou REST, bem como com SDKs de plataforma com suporte publicados para Azure Resource Manager ou serviço de provisionamento de dispositivos no Hub IoT.

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você implantou um hub IoT e uma instância do serviço de provisionamento de dispositivos e vinculou os dois recursos. Para saber como usar essa configuração para provisionar um dispositivo simulado, continue no início rápido para criar um dispositivo simulado.

> [!div class="nextstepaction"]
> [Início rápido para criar um dispositivo simulado](./quick-create-simulated-device.md)


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
