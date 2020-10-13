---
title: Implementar recursos com CLI E modelo Azure
description: Utilize o Azure Resource Manager e o Azure CLI para mobilizar recursos para a Azure. Os recursos são definidos num modelo do Resource Manager.
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: 8d033bb9ad1c841614ee1e48aa7edc6b8fe18550
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91372175"
---
# <a name="deploy-resources-with-arm-templates-and-azure-cli"></a>Implementar recursos com modelos ARM e Azure CLI

Este artigo explica como usar o Azure CLI com modelos de Gestor de Recursos Azure (modelos ARM) para implantar os seus recursos no Azure. Se não está familiarizado com os conceitos de implantação e gestão das suas soluções Azure, consulte a [visão geral da implementação do modelo](overview.md).

Os comandos de implantação foram alterados na versão 2.2.0 do Azure CLI. Os exemplos deste artigo requerem a versão 2.2.0 ou posterior do Azure CLI.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Se não tiver o Azure CLI instalado, pode utilizar o [Cloud Shell](#deploy-template-from-cloud-shell).

## <a name="deployment-scope"></a>Âmbito de implantação

Pode direcionar a sua implementação para um grupo de recursos, subscrição, grupo de gestão ou inquilino. Na maioria dos casos, terás como alvo a implantação de um grupo de recursos. Para aplicar políticas e atribuições de papéis em um âmbito maior, use subscrição, grupo de gestão ou implantações de inquilinos. Ao implementar uma subscrição, pode criar um grupo de recursos e implementar recursos para o mesmo.

Dependendo do alcance da implantação, utiliza-se diferentes comandos.

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

Os exemplos deste artigo utilizam implementações de grupos de recursos.

## <a name="deploy-local-template"></a>Implementar modelo local

Ao mobilizar recursos para Azure, você:

1. Inicie sessão na sua conta do Azure
2. Criar um grupo de recursos que sirva de recipiente para os recursos implantados. O nome do grupo de recursos só pode incluir caracteres alfanuméricos, períodos, sublinhados, hífens e parênteses. Pode chegar a 90 caracteres. Não pode acabar num período.
3. Implemente para o grupo de recursos o modelo que define os recursos para criar.

Um modelo pode incluir parâmetros que lhe permitem personalizar a implementação. Por exemplo, pode fornecer valores adaptados para um ambiente específico (como dev, teste e produção). O modelo de amostra define um parâmetro para a conta de armazenamento SKU.

O exemplo a seguir cria um grupo de recursos e implementa um modelo a partir da sua máquina local:

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

A implementação pode demorar alguns minutos a concluir. Quando terminar, vê-se uma mensagem que inclui o resultado:

```output
"provisioningState": "Succeeded",
```

## <a name="deployment-name"></a>Nome de implantação

No exemplo anterior, nomeou o `ExampleDeployment` destacamento. Se não fornecer um nome para a implementação, o nome do ficheiro de modelo é usado. Por exemplo, se implementar um modelo nomeado `azuredeploy.json` e não especificar um nome de implantação, a implementação é nomeada `azuredeploy` .

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

## <a name="deploy-remote-template"></a>Implementar o modelo remoto

Em vez de armazenar modelos ARM na sua máquina local, pode preferir guardá-los num local externo. Pode armazenar modelos num repositório de controlo de origem (como o GitHub). Ou pode armazená-los numa conta de armazenamento Azure para acesso partilhado na sua organização.

Para implementar um modelo externo, utilize o parâmetro **modelo-uri.** Utilize o URI no exemplo para implementar o modelo de amostra do GitHub.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

O exemplo anterior requer um URI acessível ao público para o modelo, que funciona para a maioria dos cenários porque o seu modelo não deve incluir dados sensíveis. Se precisar de especificar dados sensíveis (como uma palavra-passe de administração), passe esse valor como parâmetro seguro. No entanto, se não quiser que o seu modelo seja acessível ao público, pode protegê-lo armazenando-o num recipiente de armazenamento privado. Para obter informações sobre a implementação de um modelo que requer um token de assinatura de acesso partilhado (SAS), consulte [implementar o modelo privado com o token SAS](secure-template-with-sas-token.md).

## <a name="deploy-template-spec"></a>Implementar especificação de modelo

Em vez de implementar um modelo local ou remoto, pode criar uma [especificação de modelo](template-specs.md). A especificação do modelo é um recurso na sua subscrição Azure que contém um modelo ARM. Torna-se fácil partilhar o modelo de forma segura com os utilizadores da sua organização. Você usa o controlo de acesso baseado em funções Azure (Azure RBAC) para conceder acesso à especificação do modelo. Esta funcionalidade encontra-se atualmente em pré-visualização.

Os exemplos a seguir mostram como criar e implementar uma especificação de modelo. Estes comandos só estão disponíveis se se [ter inscrito para a pré-visualização](https://aka.ms/templateSpecOnboarding).

Em primeiro lugar, cria-se a especificação do modelo fornecendo o modelo ARM.

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json"
```

Em seguida, obtém-se o ID para especificação de modelo e implemente-o.

```azurecli
id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

Para obter mais informações, consulte [as especificações do modelo do Gestor de Recursos Azure (Preview)](template-specs.md).

## <a name="preview-changes"></a>Pré-visualizar alterações

Antes de implementar o seu modelo, pode visualizar as alterações que o modelo irá fazer para o seu ambiente. Utilize a [operação "e se"](template-deploy-what-if.md) para verificar se o modelo faz as alterações que espera. E se também valida o modelo para erros.

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../../includes/resource-manager-cloud-shell-deploy.md)]

Na Cloud Shell, utilize os seguintes comandos:

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az deployment group create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="parameters"></a>Parâmetros

Para passar valores de parâmetros, pode utilizar parâmetros inline ou um ficheiro de parâmetros.

### <a name="inline-parameters"></a>Parâmetros inline

Para passar parâmetros inline, forneça os valores em `parameters` . Por exemplo, para passar uma corda e matriz para um modelo é uma concha bash, use:

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

A arrayContent.jsno formato é:

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

Em vez de passar parâmetros como valores inline no seu script, pode ser mais fácil usar um ficheiro JSON que contenha os valores dos parâmetros. O ficheiro do parâmetro deve ser um ficheiro local. Os ficheiros de parâmetros externos não são suportados com O Azure CLI.

Para obter mais informações sobre o ficheiro parâmetro, consulte o [ficheiro de parâmetros Do Gestor de Recursos](parameter-files.md).

Para passar um ficheiro de parâmetro local, use `@` para especificar um ficheiro local chamado storage.parameters.jsligado.

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
- Para obter informações sobre a implementação de um modelo que requer um token SAS, consulte [implementar o modelo privado com o token SAS](secure-template-with-sas-token.md).
