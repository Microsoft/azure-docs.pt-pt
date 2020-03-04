---
title: Implementar recursos com ClI Azure e modelo
description: Utilize o Azure Resource Manager e o Azure CLI para mobilizar recursos para o Azure. Os recursos são definidos num modelo do Resource Manager.
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: 64f60a6e15a0c51e5ee506340c064804f7588693
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250668"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Implementar recursos com modelos do Resource Manager e do CLI do Azure

Este artigo explica como usar o Azure CLI com modelos de Gestor de Recursos para implantar os seus recursos para o Azure. Se não está familiarizado com os conceitos de implantação e gestão das suas soluções Azure, consulte a visão geral da [implementação](overview.md)do modelo .

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Se não tiver o Azure CLI instalado, pode utilizar a [Cloud Shell](#deploy-template-from-cloud-shell).

## <a name="deployment-scope"></a>Âmbito de implantação

Pode direcionar a sua implementação para uma subscrição Azure ou um grupo de recursos dentro de uma subscrição. Na maioria dos casos, irá direcionar a implantação para um grupo de recursos. Utilize implementações de subscrição para aplicar políticas e atribuições de papéis em toda a subscrição. Também utiliza implementações de subscrição para criar um grupo de recursos e implementar recursos para o mesmo. Dependendo do alcance da implantação, utiliza-se comandos diferentes.

Para implantar num grupo de **recursos,** utilize a criação de implementação do [grupo Az:](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)

```azurecli
az group deployment create --resource-group <resource-group-name> --template-file <path-to-template>
```

Para implementar uma **subscrição,** utilize a criação de [implementação az:](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create)

```azurecli
az deployment create --location <location> --template-file <path-to-template>
```

Para obter mais informações sobre implementações de nível de subscrição, consulte [Criar grupos de recursos e recursos ao nível da subscrição](deploy-to-subscription.md).

Atualmente, as implementações de grupos de gestão são apenas suportadas através da API REST. Para obter mais informações sobre as implementações a nível do grupo de gestão, consulte Criar recursos ao nível do grupo de [gestão.](deploy-to-management-group.md)

Os exemplos deste artigo utilizam as implantações de grupos de recursos.

## <a name="deploy-local-template"></a>Implementar modelo local

Ao distribuir recursos para o Azure, você:

1. Inicie sessão na sua conta do Azure
2. Crie um grupo de recursos que sirva de recipiente para os recursos implantados. O nome do grupo de recursos só pode incluir caracteres alfanuméricos, períodos, sublinhados, hífenes e parênteses. Pode ser até 90 caracteres. Não pode acabar num período.
3. Desloque para o grupo de recursos o modelo que define os recursos para criar

Um modelo pode incluir parâmetros que lhe permitem personalizar a implementação. Por exemplo, pode fornecer valores que são adaptados para um ambiente específico (como dev, teste e produção). O modelo de amostra define um parâmetro para a conta de armazenamento SKU.

O exemplo seguinte cria um grupo de recursos e implanta um modelo da sua máquina local:

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

A implementação pode demorar alguns minutos a concluir. Quando termina, vê-se uma mensagem que inclui o resultado:

```output
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>Implementar modelo remoto

Em vez de armazenar modelos de Gestor de Recursos na sua máquina local, pode preferir armazená-los num local externo. Pode armazenar modelos num repositório de controlo de fonte (como o GitHub). Ou pode guardá-los numa conta de armazenamento Azure para acesso partilhado na sua organização.

Para implementar um modelo externo, utilize o parâmetro **modelo-uri.** Utilize o URI no exemplo para implementar o modelo de amostra do GitHub.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

O exemplo anterior requer um URI acessível ao público para o modelo, que funciona para a maioria dos cenários porque o seu modelo não deve incluir dados sensíveis. Se precisar especificar dados sensíveis (como uma senha de administração), passe esse valor como parâmetro seguro. No entanto, se não quiser que o seu modelo seja acessível ao público, pode protegê-lo armazenando-o num recipiente de armazenamento privado. Para obter informações sobre a implementação de um modelo que requer uma assinatura de acesso partilhado (SAS) ficha, consulte [Implementar modelo privado com token SAS](secure-template-with-sas-token.md).

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../../includes/resource-manager-cloud-shell-deploy.md)]

Na Cloud Shell, utilize os seguintes comandos:

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az group deployment create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="parameters"></a>Parâmetros

Para passar os valores dos parâmetros, pode utilizar parâmetros inline ou um ficheiro de parâmetro.

### <a name="inline-parameters"></a>Parâmetros inline

Para passar os parâmetros inline, forneça os valores em `parameters`. Por exemplo, passar uma corda e uma matriz para um modelo é uma concha bash, use:

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Se estiver a utilizar o Azure CLI com O Comando Do Windows (CMD) ou PowerShell, passe a matriz no formato: `exampleArray="['value1','value2']"`.

Também pode obter o conteúdo do ficheiro e fornecer esse conteúdo como parâmetro inline.

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Obter um valor de parâmetro de um ficheiro é útil quando precisa de fornecer valores de configuração. Por exemplo, pode fornecer [valores de init em nuvem para uma máquina virtual Linux](../../virtual-machines/linux/using-cloud-init.md).

O formato arrayContent.json é:

```json
[
    "value1",
    "value2"
]
```

### <a name="parameter-files"></a>Ficheiros paraparâmetros

Em vez de passar parâmetros como valores inline no seu script, poderá ser mais fácil utilizar um ficheiro JSON que contenha os valores do parâmetro. O ficheiro do parâmetro deve ser um ficheiro local. Os ficheiros de parâmetros externos não são suportados com o Azure CLI.

Para obter mais informações sobre o ficheiro do parâmetro, consulte o [ficheiro de parâmetro sinuoso Do Gestor de Recursos](parameter-files.md).

Para passar um ficheiro de parâmetro local, use `@` para especificar um ficheiro local chamado storage.parameters.json.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="handle-extended-json-format"></a>Manuseie formato JSON estendido

Para implementar um modelo com cordas ou comentários de várias linhas, deve utilizar o interruptor `--handle-extended-json-format`.  Por exemplo:

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

## <a name="test-a-template-deployment"></a>Teste a implementação de um modelo

Para testar os valores do seu modelo e parâmetro sem realmente implementar quaisquer recursos, utilize a validação de implementação do [grupo Az](/cli/azure/group/deployment#az-group-deployment-validate).

```azurecli-interactive
az group deployment validate \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

Se não forem detetados erros, o comando devolve informações sobre a implementação do teste. Em particular, note que o valor do **erro** é nulo.

```output
{
  "error": null,
  "properties": {
      ...
```

Se for detetado um erro, o comando devolve uma mensagem de erro. Por exemplo, passar um valor incorreto para a conta de armazenamento SKU, devolve o seguinte erro:

```output
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

Se o seu modelo tiver um erro de sintaxe, o comando devolve um erro indicando que não poderia analisar o modelo. A mensagem indica o número de linha e a posição do erro de análise.

```output
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

- Para voltar a uma implementação bem sucedida quando tiver um erro, consulte [o Rollback no erro para uma implementação bem sucedida](rollback-on-error.md).
- Para especificar como lidar com os recursos que existem no grupo de recursos mas não estão definidos no modelo, consulte os modos de implementação do Gestor de [Recursos Do Azure](deployment-modes.md).
- Para entender como definir parâmetros no seu modelo, consulte [Compreender a estrutura e a sintaxe dos modelos do Gestor](template-syntax.md)de Recursos Azure .
- Para obter dicas sobre a resolução de erros comuns de implementação, consulte erros comuns de [implantação do Azure com o Azure Resource Manager](common-deployment-errors.md).
- Para obter informações sobre a implementação de um modelo que requer uma ficha SAS, consulte [Implementar modelo privado com token SAS](secure-template-with-sas-token.md).
- Para lançar com segurança o seu serviço para mais de uma região, consulte o Gestor de [Destacamento seleções do Azure.](deployment-manager-overview.md)
