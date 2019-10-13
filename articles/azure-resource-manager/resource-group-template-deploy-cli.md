---
title: Implantar recursos com CLI do Azure e modelo | Microsoft Docs
description: Use Azure Resource Manager e CLI do Azure para implantar recursos no Azure. Os recursos são definidos num modelo do Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: tomfitz
ms.openlocfilehash: c5a07d8b52e83215b2fdc220d76557ca45e1eae9
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286027"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Implementar recursos com modelos do Resource Manager e do CLI do Azure

Este artigo explica como usar CLI do Azure com modelos do Resource Manager para implantar seus recursos no Azure. Se você não estiver familiarizado com os conceitos de implantação e gerenciamento de suas soluções do Azure, confira [Azure Resource Manager visão geral](resource-group-overview.md).

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

Se você não tiver CLI do Azure instalado, poderá usar o [Cloud Shell](#deploy-template-from-cloud-shell).

## <a name="deployment-scope"></a>Escopo da implantação

Você pode direcionar sua implantação para uma assinatura do Azure ou um grupo de recursos em uma assinatura. Na maioria dos casos, você direcionará a implantação para um grupo de recursos. Use implantações de assinatura para aplicar políticas e atribuições de função na assinatura. Você também usa implantações de assinatura para criar um grupo de recursos e implantar recursos nele. Dependendo do escopo da implantação, você usará comandos diferentes.

Para implantar em um **grupo de recursos**, use [AZ Group Deployment Create](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create):

```azurecli
az group deployment create --resource-group <resource-group-name> --template-file <path-to-template>
```

Para implantar em uma **assinatura**, use [AZ Deployment Create](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create):

```azurecli
az deployment create --location <location> --template-file <path-to-template>
```

Atualmente, as implantações de grupo de gerenciamento só têm suporte por meio da API REST. Consulte [implantar recursos com modelos do Resource Manager e a API REST do Gerenciador de recursos](resource-group-template-deploy-rest.md).

Os exemplos neste artigo usam implantações de grupo de recursos. Para obter mais informações sobre implantações de assinatura, consulte [criar grupos de recursos e recursos no nível da assinatura](deploy-to-subscription.md).

## <a name="deploy-local-template"></a>Implantar modelo local

Ao implantar recursos no Azure, você:

1. Inicie sessão na sua conta do Azure
2. Crie um grupo de recursos que serve como o contêiner para os recursos implantados. O nome do grupo de recursos pode incluir apenas caracteres alfanuméricos, pontos, sublinhados, hifens e parênteses. Pode ter até 90 caracteres. Ele não pode terminar em um ponto.
3. Implantar no grupo de recursos o modelo que define os recursos a serem criados

Um modelo pode incluir parâmetros que permitem que você personalize a implantação. Por exemplo, você pode fornecer valores personalizados para um determinado ambiente (como desenvolvimento, teste e produção). O modelo de exemplo define um parâmetro para a SKU da conta de armazenamento.

O exemplo a seguir cria um grupo de recursos e implanta um modelo de seu computador local:

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

A implementação pode demorar alguns minutos a concluir. Quando ele for concluído, você verá uma mensagem que inclui o resultado:

```azurecli
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>Implantar modelo remoto

Em vez de armazenar modelos do Resource Manager em seu computador local, você pode preferir armazená-los em um local externo. Você pode armazenar modelos em um repositório de controle do código-fonte (como o GitHub). Ou você pode armazená-los em uma conta de armazenamento do Azure para acesso compartilhado em sua organização.

Para implantar um modelo externo, use o parâmetro **Template-URI** . Use o URI no exemplo para implantar o modelo de exemplo do GitHub.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

O exemplo anterior requer um URI acessível publicamente para o modelo, que funciona para a maioria dos cenários porque o modelo não deve incluir dados confidenciais. Se você precisar especificar dados confidenciais (como uma senha de administrador), passe esse valor como um parâmetro seguro. No entanto, se você não quiser que seu modelo seja acessível publicamente, você pode protegê-lo armazenando-o em um contêiner de armazenamento privado. Para obter informações sobre como implantar um modelo que requer um token SAS (assinatura de acesso compartilhado), consulte [implantar modelo privado com o token SAS](resource-manager-cli-sas-token.md).

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

No Cloud Shell, use os seguintes comandos:

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az group deployment create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="parameters"></a>Parâmetros

Para passar valores de parâmetro, você pode usar parâmetros embutidos ou um arquivo de parâmetro.

### <a name="inline-parameters"></a>Parâmetros embutidos

Para passar parâmetros embutidos, forneça os valores em `parameters`. Por exemplo, para passar uma cadeia de caracteres e uma matriz para um modelo é um shell bash, use:

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Se você estiver usando CLI do Azure com prompt de comando do Windows (CMD) ou PowerShell, passe a matriz no formato: `exampleArray="['value1','value2']"`.

Você também pode obter o conteúdo do arquivo e fornecer esse conteúdo como um parâmetro embutido.

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Obter um valor de parâmetro de um arquivo é útil quando você precisa fornecer valores de configuração. Por exemplo, você pode fornecer [valores de Cloud-init para uma máquina virtual do Linux](../virtual-machines/linux/using-cloud-init.md).

O formato arrayContent. JSON é:

```json
[
    "value1",
    "value2"
]
```

### <a name="parameter-files"></a>Arquivos de parâmetro

Em vez de passar parâmetros como valores embutidos em seu script, você pode achar mais fácil usar um arquivo JSON que contenha os valores de parâmetro. O arquivo de parâmetro deve ser um arquivo local. Não há suporte para arquivos de parâmetros externos com CLI do Azure.

Para obter mais informações sobre o arquivo de parâmetro, consulte [criar arquivo de parâmetro do Resource Manager](resource-manager-parameter-files.md).

Para passar um arquivo de parâmetro local, use `@` para especificar um arquivo local chamado Storage. Parameters. JSON.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="handle-extended-json-format"></a>Manipular o formato JSON estendido

Para implantar um modelo com cadeias de caracteres de várias linhas ou comentários, você deve usar a opção `--handle-extended-json-format`.  Por exemplo:

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  "apiVersion": "2018-10-01",
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

## <a name="test-a-template-deployment"></a>Testar uma implantação de modelo

Para testar seus valores de modelo e parâmetro sem realmente implantar nenhum recurso, use [AZ Group Deployment Validate](/cli/azure/group/deployment#az-group-deployment-validate).

```azurecli-interactive
az group deployment validate \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

Se nenhum erro for detectado, o comando retornará informações sobre a implantação de teste. Em particular, observe que o valor de **erro** é NULL.

```azurecli
{
  "error": null,
  "properties": {
      ...
```

Se um erro for detectado, o comando retornará uma mensagem de erro. Por exemplo, a passagem de um valor incorreto para o SKU da conta de armazenamento retorna o seguinte erro:

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template validation failed: 'The provided value 'badSKU' for the template parameter
      'storageAccountType' at line '13' and column '20' is not valid. The parameter value is not part of the allowed
      value(s): 'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.",
    "target": null
  },
  "properties": null
}
```

Se o modelo tiver um erro de sintaxe, o comando retornará um erro indicando que ele não pôde analisar o modelo. A mensagem indica o número de linha e a posição do erro de análise.

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template parse failed: 'After parsing a value an unexpected character was encountered:
      \". Path 'variables', line 31, position 3.'.",
    "target": null
  },
  "properties": null
}
```

## <a name="next-steps"></a>Passos seguintes

- Para reverter para uma implantação bem-sucedida quando você receber um erro, consulte [reverter em caso de erro para a implantação bem-sucedida](rollback-on-error.md).
- Para especificar como lidar com recursos que existem no grupo de recursos, mas que não estão definidos no modelo, consulte [Azure Resource Manager modos de implantação](deployment-modes.md).
- Para entender como definir parâmetros em seu modelo, consulte [entender a estrutura e a sintaxe de modelos de Azure Resource Manager](resource-group-authoring-templates.md).
- Para obter dicas sobre como resolver erros comuns de implantação, consulte [solucionar erros comuns de implantação do Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md).
- Para obter informações sobre como implantar um modelo que requer um token SAS, consulte [implantar o modelo privado com o token SAS](resource-manager-cli-sas-token.md).
- Para distribuir com segurança seu serviço para mais de uma região, consulte [Deployment Manager do Azure](deployment-manager-overview.md).
