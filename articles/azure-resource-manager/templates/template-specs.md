---
title: Criar e implementar especificações de modelo
description: Descreve como criar especificações de modelo e partilhá-las com outros utilizadores na sua organização.
ms.topic: conceptual
ms.date: 03/26/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 2f4aec6d9fa07edf36dea68a23ba12eb5f72d308
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626089"
---
# <a name="azure-resource-manager-template-specs-preview"></a>Especificações do modelo do Gestor de Recursos Azure (Visualização)

Uma especificação de modelo é um tipo de recurso para armazenar um modelo de gestor de recursos Azure (modelo ARM) em Azure para posterior implantação. Este tipo de recurso permite-lhe partilhar modelos ARM com outros utilizadores na sua organização. Tal como qualquer outro recurso Azure, pode utilizar o controlo de acesso baseado em funções (Azure RBAC) para partilhar a especificação do modelo.

**Microsoft.Resources/templateSpecs** é o tipo de recurso para especificações de modelo. Consiste num modelo principal e em qualquer número de modelos ligados. O Azure armazena de forma segura as especificações do modelo em grupos de recursos. Versão de suporte de especificações [de modelo.](#versioning)

Para implementar a especificação do modelo, utiliza ferramentas Azure padrão como PowerShell, Azure CLI, portal Azure, REST e outros SDKs e clientes suportados. Usa os mesmos comandos que o modelo.

> [!NOTE]
> As Especificações do Modelo estão atualmente em pré-visualização. Para usá-lo com a Azure PowerShell, tem de instalar [a versão 5.0.0 ou posterior](/powershell/azure/install-az-ps). Para usá-lo com Azure CLI, utilize [a versão 2.14.2 ou posterior](/cli/azure/install-azure-cli).

## <a name="why-use-template-specs"></a>Por que usar especificações de modelo?

As especificações do modelo fornecem os seguintes benefícios:

* Utilize modelos ARM padrão para a especificação do seu modelo.
* Você gere o acesso através do Azure RBAC, em vez de fichas SAS.
* Os utilizadores podem implementar a especificação do modelo sem terem acesso ao modelo.
* Pode integrar a especificação do modelo no processo de implementação existente, como o script PowerShell ou o pipeline DevOps.

As especificações do modelo permitem-lhe criar modelos canónicos e partilhá-los com equipas da sua organização. As especificações do modelo são seguras porque estão disponíveis para implementação do Azure Resource Manager, mas não acessíveis aos utilizadores sem a permissão correta. Os utilizadores apenas precisam de ler o acesso à especificação do modelo para implementar o seu modelo, para que possa partilhar o modelo sem permitir que outros o modifiquem.

Se atualmente tem os seus modelos numa conta de repo ou armazenamento gitHub, encontra-se em vários desafios ao tentar partilhar e usar os modelos. Para implementar o modelo, você precisa tornar o modelo acessível ao público ou gerir o acesso com fichas SAS. Para contornar esta limitação, os utilizadores podem criar cópias locais, que eventualmente divergem do seu modelo original. As especificações do modelo simplificam os modelos de partilha.

Os modelos que inclui numa especificação de modelo devem ser verificados pelos administradores da sua organização para seguir os requisitos e orientações da organização.

## <a name="create-template-spec"></a>Criar especificação de modelo

O exemplo a seguir mostra um modelo simples para criar uma conta de armazenamento em Azure.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ]
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[concat('store', uniquestring(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "kind": "StorageV2",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      }
    }
  ]
}
```

Quando cria a especificação do modelo, os comandos PowerShell ou CLI são passados no ficheiro do modelo principal. Se o modelo principal de referências ligados modelos, os comandos irão encontrá-los e empacotá-los para criar a especificação do modelo. Para saber mais, consulte [Criar uma especificação de modelo com modelos ligados.](#create-a-template-spec-with-linked-templates)

Crie uma especificação de modelo utilizando:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzTemplateSpec -Name storageSpec -Version 1.0a -ResourceGroupName templateSpecsRg -Location westus2 -TemplateFile ./mainTemplate.json
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0a" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json"
```

---

Pode ver todas as especificações do modelo na sua subscrição utilizando:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzTemplateSpec
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts list
```

---

Pode ver detalhes de uma especificação de modelo, incluindo as suas versões com:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzTemplateSpec -ResourceGroupName templateSpecsRG -Name storageSpec
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts show \
    --name storageSpec \
    --resource-group templateSpecRG \
    --version "1.0a"
```

---

## <a name="deploy-template-spec"></a>Implementar especificação de modelo

Depois de criar a especificação do modelo, os utilizadores com acesso de **leitura** à especificação do modelo podem implantá-lo. Para obter informações sobre a concessão de acesso, consulte [Tutorial: Conceder a um grupo acesso aos recursos da Azure utilizando a Azure PowerShell](../../role-based-access-control/tutorial-role-assignments-group-powershell.md).

As especificações do modelo podem ser implementadas através do portal, PowerShell, Azure CLI, ou como um modelo ligado numa implementação de modelo maior. Os utilizadores de uma organização podem implementar uma especificação de modelo para qualquer âmbito em Azure (grupo de recursos, subscrição, grupo de gestão ou inquilino).

Em vez de passar por um caminho ou URI para um modelo, você implementa uma especificação de modelo fornecendo o seu ID de recurso. O ID de recursos tem o seguinte formato:

**/subscrições/{subscrição-id}/resourceGroups/{resource-group}/providers/Microsoft.Resources/templateSpecs/{template-spec-name}/versões/{modelo-versão**

Note que o ID do recurso inclui um nome de versão para a especificação do modelo.

Por exemplo, implementa uma especificação de modelo com o seguinte comando.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$id = "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/templateSpecsRG/providers/Microsoft.Resources/templateSpecs/storageSpec/versions/1.0a"

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
id = "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/templateSpecsRG/providers/Microsoft.Resources/templateSpecs/storageSpec/versions/1.0a"

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

---

Na prática, normalmente, você vai correr `Get-AzTemplateSpec` ou `az ts show` obter o ID da especificação do modelo que você deseja implementar.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$id = (Get-AzTemplateSpec -Name storageSpec -ResourceGroupName templateSpecsRg -Version 1.0a).Versions.Id

New-AzResourceGroupDeployment `
  -ResourceGroupName demoRG `
  -TemplateSpecId $id
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0a" --query "id")

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

---

Também pode abrir um URL no seguinte formato para implementar uma especificação de modelo:

```url
https://portal.azure.com/#create/Microsoft.Template/templateSpecVersionId/%2fsubscriptions%2f{subscription-id}%2fresourceGroups%2f{resource-group-name}%2fproviders%2fMicrosoft.Resources%2ftemplateSpecs%2f{template-spec-name}%2fversions%2f{template-spec-version}
```

## <a name="parameters"></a>Parâmetros

Passar parâmetros para a especificação do modelo é exatamente como passar parâmetros para um modelo ARM. Adicione os valores dos parâmetros, quer em linha, quer num ficheiro de parâmetros.

Para passar uma linha de parâmetro, use:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG `
  -StorageAccountType Standard_GRS
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group demoRG \
  --template-spec $id \
  --parameters storageAccountType='Standard_GRS'
```

---

Para criar um ficheiro de parâmetro local, utilize:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "StorageAccountType": {
      "value": "Standard_GRS"
    }
  }
}
```

E, passe o arquivo de parâmetros com:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG `
  -TemplateParameterFile ./mainTemplate.parameters.json
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group demoRG \
  --template-spec $id \
  --parameters "./mainTemplate.parameters.json"
```

---

## <a name="versioning"></a>Controlo de versões

Quando cria uma especificação de modelo, fornece-se um nome de versão para o mesmo. Ao iterar no código do modelo, pode atualizar uma versão existente (para hotfixes) ou publicar uma nova versão. A versão é uma cadeia de texto. Pode optar por seguir qualquer sistema de versão, incluindo a versão semântica. Os utilizadores da especificação do modelo podem fornecer o nome da versão que pretendem usar ao implantá-lo.

## <a name="use-tags"></a>Utilizar etiquetas

[As etiquetas](../management/tag-resources.md) ajudam-no a organizar logicamente os seus recursos. Pode adicionar tags às especificações do modelo utilizando a Azure PowerShell e a Azure CLI:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzTemplateSpec `
  -Name storageSpec `
  -Version 1.0a `
  -ResourceGroupName templateSpecsRg `
  -Location westus2 `
  -TemplateFile ./mainTemplate.json `
  -Tag @{Dept="Finance";Environment="Production"}
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0a" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json" \
  --tags Dept=Finance Environment=Production
```

---

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzTemplateSpec `
  -Name storageSpec `
  -Version 1.0a `
  -ResourceGroupName templateSpecsRg `
  -Location westus2 `
  -TemplateFile ./mainTemplate.json `
  -Tag @{Dept="Finance";Environment="Production"}
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts update \
  --name storageSpec \
  --version "1.0a" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json" \
  --tags Dept=Finance Environment=Production
```

---

Ao criar ou modificar uma especificação do modelo com o parâmetro de versão especificado, mas sem o parâmetro tag/tags:

- Se a especificação do modelo existe e tem tags, mas a versão não existe, a nova versão herda as mesmas etiquetas que a especificação do modelo existente.

Ao criar ou modificar uma especificação do modelo com o parâmetro tag/tags e o parâmetro da versão especificado:

- Se a especificação do modelo e a versão não existirem, as etiquetas são adicionadas tanto à nova especificação do modelo como à nova versão.
- Se a especificação do modelo existir, mas a versão não existir, as tags são adicionadas apenas à nova versão.
- Se existirem tanto a especificação do modelo como a versão, as tags aplicam-se apenas à versão.

Ao modificar um modelo com o parâmetro tag/tags especificado, mas sem o parâmetro de versão especificado, as etiquetas só são adicionadas à especificação do modelo.

## <a name="create-a-template-spec-with-linked-templates"></a>Crie uma especificação de modelo com modelos ligados

Se o modelo principal para as referências de especificação do seu modelo ligar modelos, os comandos PowerShell e CLI podem automaticamente encontrar e embalar os modelos ligados a partir da sua unidade local. Não precisa configurar manualmente contas de armazenamento ou repositórios para hospedar as especificações do modelo - tudo é autossuficiente no recurso de especificação do modelo.

O exemplo a seguir consiste num modelo principal com dois modelos ligados. O exemplo é apenas um excerto do modelo. Note que usa uma propriedade nomeada `relativePath` para ligar aos outros modelos. Deve utilizar `apiVersion` `2020-06-01` ou mais tarde para o recurso de implantação.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  ...
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "relativePath": "artifacts/webapp.json"
        }
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "relativePath": "artifacts/database.json"
        }
      }
    }
  ],
  "outputs": {}
}
```

Quando o comando PowerShell ou CLI para criar a especificação do modelo é executado para o exemplo anterior, o comando encontra três ficheiros - o modelo principal, o modelo de aplicação web ( `webapp.json` ) e o modelo de base de dados ( ) - e `database.json` embala-os na especificação do modelo.

Para obter mais informações, consulte [Tutorial: Crie uma especificação de modelo com modelos ligados](template-specs-create-linked.md).

## <a name="deploy-template-spec-as-a-linked-template"></a>Implementar especificação de modelo como um modelo ligado

Uma vez criado uma especificação de modelo, é fácil reutilizá-lo a partir de um modelo ARM ou outra especificação de modelo. Você liga a uma especificação de modelo adicionando o seu ID de recurso ao seu modelo. A especificação do modelo ligado é automaticamente implantada quando implementa o modelo principal. Este comportamento permite desenvolver especificações de modelo modulares e reutilizá-las conforme necessário.

Por exemplo, você pode criar uma especificação de modelo que implementa recursos de networking, e outra especificação de modelo que implementa recursos de armazenamento. Nos modelos ARM, você liga a estas duas especificações de modelo sempre que precisar de configurar recursos de rede ou armazenamento.

O exemplo a seguir é semelhante ao exemplo anterior, mas você usa a `id` propriedade para ligar a uma especificação de modelo em vez da propriedade para ligar a um modelo `relativePath` local. Utilize `2020-06-01` para a versão API para o recurso de implementação. No exemplo, as especificações do modelo estão num grupo de recursos chamado **modeloSPECSRG.**

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  ...
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "networkingDeployment",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'networkingSpec', '1.0a')]"
        }
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "storageDeployment",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0a')]"
        }
      }
    }
  ],
  "outputs": {}
}
```

Para obter mais informações sobre as especificações do modelo de ligação, consulte [Tutorial: Implemente uma especificação do modelo como um modelo ligado](template-specs-deploy-linked-template.md).

## <a name="next-steps"></a>Passos seguintes

* Para criar e implementar uma especificação de modelo, consulte [Quickstart: Criar e implementar a especificação do modelo](quickstart-create-template-specs.md).

* Para obter mais informações sobre os modelos de ligação nas especificações do modelo, consulte [Tutorial: Crie uma especificação de modelo com modelos ligados](template-specs-create-linked.md).

* Para obter mais informações sobre a implementação de uma especificação de modelo como um modelo ligado, consulte [Tutorial: Implemente uma especificação de modelo como um modelo ligado](template-specs-deploy-linked-template.md).
