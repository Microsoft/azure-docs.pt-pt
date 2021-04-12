---
título: Modelo DE REDE DE Conta de Media Services : Descrição dos Serviços Azure Media: Este artigo mostra-lhe como usar um modelo ARM para criar uma conta de serviços de comunicação social.
serviços: media-services documentationcenter: '' autor: IngridAtMicrosoft manager: femila editor: ''

ms.service: media-services ms.workload: ms.topic: quickstart ms.date: 03/23/2021 ms.author: inhenkel ms.custom: subject-armqs

---

# <a name="quickstart-media-services-account-arm-template"></a>Quickstart: Modelo ARM de conta de serviços de mídia

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este artigo mostra-lhe como usar um modelo de Gestor de Recursos Azure (modelo ARM) para criar uma conta de serviços de comunicação.

## <a name="introduction"></a>Introdução

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Os leitores experientes com modelos ARM podem continuar a [secção de implantação](#deploy-the-template).

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-media-services-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Se nunca implementou um modelo ARM antes, é útil ler sobre [os modelos Azure ARM](../../azure-resource-manager/templates/index.yml) e passar pelo [tutorial](../../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-powershell).

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-media-services-create/).

A sintaxe para a cerca de código JSON é:

:::code language="json" source="~/quickstart-templates/101-media-services-create/azuredeploy.json":::

Três tipos de recursos Azure são definidos no modelo:

- [Microsoft.Storage/storageAstas:](/azure/templates/microsoft.storage/storageaccounts)criar uma conta de armazenamento
- [Microsoft.Media/mediaservices](/azure/templates/microsoft.media/mediaservices): criar uma conta de Media Services

## <a name="set-the-account"></a>Definir a conta

```azurecli-interactive

az account set --subscription {your subscription name or id}

```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

```azurecli-interactive

az group create --name {the name you want to give your resource group} --location "{pick a location}"

```

## <a name="assign-a-variable-to-your-deployment-file"></a>Atribua uma variável ao seu ficheiro de implantação

Por conveniência, crie uma variável que armazene o caminho para o ficheiro de modelo. Esta variável torna mais fácil executar os comandos de implementação, porque não tem de reescrever o caminho sempre que implementa.

```azurecli-interactive

templateFile="{provide the path to the template file}"

```

## <a name="deploy-the-template"></a>Implementar o modelo

Será solicitado que insira o nome da conta dos serviços de comunicação social.

```azurecli-interactive

az deployment group create --name {the name you want to give to your deployment} --resource-group {the name of resource group you created} --template-file $templateFile

```

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

Deve ver uma resposta JSON semelhante à seguinte:

```json
{
  "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Resources/deployments/amsarmquickstartdeploy",
  "location": null,
  "name": "amsarmquickstartdeploy",
  "properties": {
    "correlationId": "{correlationid}",
    "debugSetting": null,
    "dependencies": [
      {
        "dependsOn": [
          {
            "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Storage/storageAccounts/storagey44cfdmliwatk",
            "resourceGroup": "amsarmquickstartrg",
            "resourceName": "storagey44cfdmliwatk",
            "resourceType": "Microsoft.Storage/storageAccounts"
          }
        ],
        "id": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/amsarmquickstartrg/providers/Microsoft.Media/mediaServices/{accountname}",
        "resourceGroup": "amsarmquickstartrg",
        "resourceName": "{accountname}",
        "resourceType": "Microsoft.Media/mediaServices"
      }
    ],
    "duration": "PT1M10.8615001S",
    "error": null,
    "mode": "Incremental",
    "onErrorDeployment": null,
    "outputResources": [
      {
        "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Media/mediaServices/{accountname}",
        "resourceGroup": "amsarmquickstartrg"
      },
      {
        "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Storage/storageAccounts/storagey44cfdmliwatk",
        "resourceGroup": "amsarmquickstartrg"
      }
    ],
    "outputs": null,
    "parameters": {
      "mediaServiceName": {
        "type": "String",
        "value": "{accountname}"
      }
    },
    "parametersLink": null,
    "providers": [
      {
        "id": null,
        "namespace": "Microsoft.Media",
        "registrationPolicy": null,
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "capabilities": null,
            "locations": [
              "eastus"
            ],
            "properties": null,
            "resourceType": "mediaServices"
          }
        ]
      },
      {
        "id": null,
        "namespace": "Microsoft.Storage",
        "registrationPolicy": null,
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "capabilities": null,
            "locations": [
              "eastus"
            ],
            "properties": null,
            "resourceType": "storageAccounts"
          }
        ]
      }
    ],
    "provisioningState": "Succeeded",
    "templateHash": "{templatehash}",
    "templateLink": null,
    "timestamp": "2020-11-24T23:25:52.598184+00:00",
    "validatedResources": null
  },
  "resourceGroup": "amsarmquickstartrg",
  "tags": null,
  "type": "Microsoft.Resources/deployments"
}

```

No portal Azure, confirme que os seus recursos foram criados.

![recursos quickstart criados](./media/media-services-arm-template-quickstart/quickstart-arm-template-resources.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Se não está a planear utilizar os recursos que acabou de criar, pode eliminar o grupo de recursos.

```azurecli-interactive

az group delete --name {name of the resource group}

```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a utilização de um modelo ARM seguindo o processo de criação de um modelo com parâmetros, variáveis e muito mais, tente

> [!div class="nextstepaction"]
> [Tutorial: Crie e implemente o seu primeiro modelo ARM](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)