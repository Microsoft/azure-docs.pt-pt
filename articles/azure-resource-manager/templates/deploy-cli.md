---
title: Implementar recursos com CLI E modelo Azure
description: Utilize o Azure Resource Manager e o Azure CLI para mobilizar recursos para a Azure. Os recursos são definidos num modelo do Resource Manager.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: a2caea70a51a737bfa433a089c03b43f252b5d6e
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028153"
---
# <a name="deploy-resources-with-arm-templates-and-azure-cli"></a>Implementar recursos com modelos ARM e Azure CLI

Este artigo explica como usar o Azure CLI com modelos de Gestor de Recursos Azure (modelos ARM) para implantar os seus recursos no Azure. Se não está familiarizado com os conceitos de implantação e gestão das suas soluções Azure, consulte a [visão geral da implementação do modelo](overview.md).

Os comandos de implantação foram alterados na versão 2.2.0 do Azure CLI. Os exemplos deste artigo requerem a versão 2.2.0 ou posterior do Azure CLI.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Se não tiver o Azure CLI instalado, pode utilizar a Azure Cloud Shell. Para obter mais informações, consulte [os modelos ARM da Azure Cloud Shell](deploy-cloud-shell.md).

## <a name="deployment-scope"></a>Âmbito de implantação

Pode direcionar a sua implementação para um grupo de recursos, subscrição, grupo de gestão ou inquilino. Dependendo do alcance da implantação, utiliza-se diferentes comandos.

* Para implementar num **grupo de recursos,** utilize [o grupo de implantação az create](/cli/azure/deployment/group#az-deployment-group-create):

  ```azurecli-interactive
  az deployment group create --resource-group <resource-group-name> --template-file <path-to-template>
  ```

* Para implementar uma **subscrição,** utilize [o sub-utilização az create](/cli/azure/deployment/sub#az-deployment-sub-create):

  ```azurecli-interactive
  az deployment sub create --location <location> --template-file <path-to-template>
  ```

  Para obter mais informações sobre as implementações do nível de subscrição, consulte [Criar grupos de recursos e recursos ao nível da subscrição.](deploy-to-subscription.md)

* Para implantar num **grupo de gestão,** utilize [a az deployment mg create](/cli/azure/deployment/mg#az-deployment-mg-create):

  ```azurecli-interactive
  az deployment mg create --location <location> --template-file <path-to-template>
  ```

  Para obter mais informações sobre as implementações de nível de grupo de gestão, consulte [Criar recursos ao nível do grupo de gestão.](deploy-to-management-group.md)

* Para implantar para um **inquilino,** use [az inusitário de implantação criar:](/cli/azure/deployment/tenant#az-deployment-tenant-create)

  ```azurecli-interactive
  az deployment tenant create --location <location> --template-file <path-to-template>
  ```

  Para obter mais informações sobre as implementações de nível de inquilino, consulte [Criar recursos ao nível do arrendatário.](deploy-to-tenant.md)

Para cada âmbito, o utilizador que implementa o modelo deve ter as permissões necessárias para criar recursos.

## <a name="deploy-local-template"></a>Implementar um modelo local

Pode implementar um modelo a partir da sua máquina local ou um que seja armazenado externamente. Esta secção descreve a implementação de um modelo local.

Se estiver a implantar para um grupo de recursos que não existe, crie o grupo de recursos. O nome do grupo de recursos só pode incluir caracteres alfanuméricos, períodos, sublinhados, hífens e parênteses. Pode chegar a 90 caracteres. O nome não pode acabar num período.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
```

Para implementar um modelo local, utilize o `--template-file` parâmetro no comando de implantação. O exemplo a seguir também mostra como definir um valor de parâmetro que vem do modelo.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file azuredeploy.json \
  --parameters storageAccountType=Standard_GRS
```

A implementação pode demorar alguns minutos a concluir. Quando terminar, vê-se uma mensagem que inclui o resultado:

```output
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>Implementar o modelo remoto

Em vez de armazenar modelos ARM na sua máquina local, pode preferir guardá-los num local externo. Pode armazenar modelos num repositório de controlo de código fonte (como o GitHub). Em alternativa, pode armazená-los numa conta de armazenamento do Azure para acesso partilhado na sua organização.

Se estiver a implantar para um grupo de recursos que não existe, crie o grupo de recursos. O nome do grupo de recursos só pode incluir caracteres alfanuméricos, períodos, sublinhados, hífens e parênteses. Pode chegar a 90 caracteres. O nome não pode acabar num período.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
```

Para implementar um modelo externo, utilize o parâmetro `template-uri`.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

O exemplo anterior requer um URI acessível ao público para o modelo, que funciona para a maioria dos cenários porque o seu modelo não deve incluir dados sensíveis. Se precisar de especificar dados sensíveis (como uma palavra-passe de administração), passe esse valor como parâmetro seguro. No entanto, se quiser gerir o acesso ao modelo, considere usar [as especificações do modelo.](#deploy-template-spec)

## <a name="deployment-name"></a>Nome de implantação

Ao colocar um modelo ARM, pode dar um nome à implantação. Este nome pode ajudá-lo a recuperar a implantação do histórico de implantação. Se não fornecer um nome para a implementação, o nome do ficheiro de modelo é usado. Por exemplo, se implementar um modelo nomeado `azuredeploy.json` e não especificar um nome de implantação, a implementação é nomeada `azuredeploy` .

Sempre que executa uma implantação, uma entrada é adicionada ao histórico de implantação do grupo de recursos com o nome de implantação. Se executar outra implantação e lhe der o mesmo nome, a entrada anterior é substituída pela implementação atual. Se pretender manter entradas únicas no histórico de implantação, dê a cada implementação um nome único.

Para criar um nome único, pode atribuir um número aleatório.

```azurecli-interactive
deploymentName='ExampleDeployment'$RANDOM
```

Ou adicionar um valor de data.

```azurecli-interactive
deploymentName='ExampleDeployment'$(date +"%d-%b-%Y")
```

Se executar implementações simultâneas para o mesmo grupo de recursos com o mesmo nome de implantação, apenas a última implementação é concluída. Quaisquer implementações com o mesmo nome que não tenham terminado são substituídas pela última implantação. Por exemplo, se executar uma implantação com o nome `newStorage` de uma conta de armazenamento chamada , e ao mesmo tempo executar outra `storage1` implantação com o nome de uma `newStorage` conta de armazenamento chamada , implementa `storage2` apenas uma conta de armazenamento. A conta de armazenamento resultante é `storage2` nomeada.

No entanto, se executar uma implantação com o nome `newStorage` de uma conta de armazenamento chamada , e imediatamente após a sua `storage1` conclusão, executar outra implantação com o nome `newStorage` de uma conta de armazenamento chamada , `storage2` então tem duas contas de armazenamento. Um tem o nome `storage1` , e o outro chama-se `storage2` . Mas só tens uma entrada na história da implantação.

Quando especificar um nome único para cada implantação, pode executá-los simultaneamente sem conflitos. Se executar uma implantação com o nome `newStorage1` de uma conta de armazenamento chamada , e ao mesmo tempo executar outra `storage1` implantação com o nome de uma `newStorage2` conta de armazenamento , `storage2` então tem duas contas de armazenamento e duas entradas no histórico de implantação.

Para evitar conflitos com implementações simultâneas e para garantir entradas únicas no histórico de implantação, dê a cada implementação um nome único.

## <a name="deploy-template-spec"></a>Implementar especificação de modelo

Em vez de implementar um modelo local ou remoto, pode criar uma [especificação de modelo](template-specs.md). A especificação do modelo é um recurso na sua subscrição Azure que contém um modelo ARM. Torna-se fácil partilhar o modelo de forma segura com os utilizadores da sua organização. Você usa o controlo de acesso baseado em funções Azure (Azure RBAC) para conceder acesso à especificação do modelo. Esta funcionalidade encontra-se atualmente em pré-visualização.

Os exemplos a seguir mostram como criar e implementar uma especificação de modelo. Estes comandos só estão disponíveis se se [ter inscrito para a pré-visualização](https://aka.ms/templateSpecOnboarding).

Em primeiro lugar, crie a especificação do modelo fornecendo o modelo ARM.

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json"
```

Em seguida, obtenha o ID para especificação de modelo e implemente-o.

```azurecli
id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

Para obter mais informações, consulte [as especificações do modelo do Gestor de Recursos Azure (Preview)](template-specs.md).

## <a name="preview-changes"></a>Pré-visualizar alterações

Antes de implementar o seu modelo, pode visualizar as alterações que o modelo irá fazer para o seu ambiente. Utilize a [operação "e se"](template-deploy-what-if.md) para verificar se o modelo faz as alterações que espera. E se também valida o modelo para erros.

## <a name="parameters"></a>Parâmetros

Para passar valores de parâmetros, pode utilizar parâmetros inline ou um ficheiro de parâmetros.

### <a name="inline-parameters"></a>Parâmetros inline

Para passar parâmetros inline, forneça os valores em `parameters` . Por exemplo, para passar uma corda e matriz para um modelo numa concha bash, use:

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Se estiver a utilizar o Azure CLI com o Windows Command Prompt (CMD) ou o PowerShell, passe a matriz no formato: `exampleArray="['value1','value2']"` .

Também pode obter o conteúdo do ficheiro e fornecer esse conteúdo como um parâmetro inline.

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Obter um valor de parâmetro de um ficheiro é útil quando é necessário fornecer valores de configuração. Por exemplo, pode fornecer [valores de ineção de nuvem para uma máquina virtual Linux](../../virtual-machines/linux/using-cloud-init.md).

A _arrayContent.jsno_ formato é:

```json
[
    "value1",
    "value2"
]
```

Para passar num objeto, por exemplo, para definir tags, use JSON. Por exemplo, o seu modelo pode incluir um parâmetro como este:

```json
    "resourceTags": {
      "type": "object",
      "defaultValue": {
        "Cost Center": "IT Department"
      }
    }
```

Neste caso, pode passar numa cadeia JSON para definir o parâmetro como mostrado no seguinte script Bash:

```bash
tags='{"Owner":"Contoso","Cost Center":"2345-324"}'
az deployment group create --name addstorage  --resource-group myResourceGroup \
--template-file $templateFile \
--parameters resourceName=abcdef4556 resourceTags="$tags"
```

Use citações duplas em torno do JSON que pretende passar para o objeto.

### <a name="parameter-files"></a>Ficheiros de parâmetros

Em vez de transmitir parâmetros como valores inline no seu script, poderá considerar mais fácil utilizar um ficheiro JSON que contenha os valores dos parâmetros. O ficheiro do parâmetro deve ser um ficheiro local. Os ficheiros de parâmetros externos não são suportados com O Azure CLI.

Para obter mais informações sobre o ficheiro de parâmetros, veja [Criar ficheiro de parâmetros do Resource Manager](parameter-files.md).

Para passar um ficheiro de parâmetro local, utilize `@` para especificar um ficheiro local nomeado _storage.parameters.jsem_.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="handle-extended-json-format"></a>Lidar com o formato JSON estendido

Para implementar um modelo com cordas ou comentários multi-linhas utilizando o Azure CLI com a versão 2.3.0 ou mais antiga, tem de utilizar o `--handle-extended-json-format` interruptor.  Por exemplo:

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

## <a name="next-steps"></a>Passos seguintes

- Para voltar a uma implementação bem sucedida quando tiver um erro, consulte [o Reversão do erro para uma implementação bem sucedida](rollback-on-error.md).
- Para especificar como lidar com os recursos que existem no grupo de recursos mas não estão definidos no modelo, consulte os [modos de implementação do Gestor de Recursos Azure](deployment-modes.md).
- Para entender como definir parâmetros no seu modelo, consulte [a estrutura e sintaxe dos modelos ARM](template-syntax.md).
- Para obter dicas sobre a resolução de erros comuns de implementação, consulte [os erros comuns de implementação do Azure com o Azure Resource Manager](common-deployment-errors.md).
