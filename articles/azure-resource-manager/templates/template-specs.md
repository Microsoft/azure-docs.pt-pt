---
title: Visão geral das especificações do modelo
description: Descreve como criar especificações de modelo e partilhá-las com outros utilizadores na sua organização.
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 47dcf44b35ad5c0b77dd0b88d683071a7f2f4ecb
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87097724"
---
# <a name="azure-resource-manager-template-specs-preview"></a>Especificações do modelo do Gestor de Recursos Azure (Visualização)

Uma especificação de modelo é um novo tipo de recurso para armazenar um modelo de Gestor de Recursos Azure (modelo ARM) em Azure para posterior implantação. Este tipo de recurso permite-lhe partilhar modelos ARM com outros utilizadores na sua organização. Tal como qualquer outro recurso Azure, pode utilizar o controlo de acesso baseado em funções (RBAC) para partilhar a especificação do modelo.

**Microsoft.Resources/templateSpecs** é o novo tipo de recurso para especificações de modelo. Consiste num modelo principal e em qualquer número de modelos ligados. O Azure armazena de forma segura as especificações do modelo em grupos de recursos. Versão de suporte de especificações [de modelo.](#versioning)

Para implementar a especificação do modelo, utiliza ferramentas Azure padrão como PowerShell, Azure CLI, portal Azure, REST e outros SDKs e clientes suportados. Usa os mesmos comandos e passa nos mesmos parâmetros para o modelo.

O benefício de usar as especificações do modelo é que as equipas da sua organização não têm de recriar ou copiar modelos para cenários comuns. Cria-se modelos canónicos e partilha-os. Os modelos que inclui numa especificação de modelo devem ser verificados pelos administradores da sua organização para seguir os requisitos e orientações da organização.

> [!NOTE]
> As Especificações do Modelo estão atualmente em pré-visualização. Para usá-lo, tem de [se inscrever na lista de espera.](https://aka.ms/templateSpecOnboarding)

## <a name="create-template-spec"></a>Criar especificação de modelo

O exemplo a seguir mostra um modelo simples para criar uma conta de armazenamento em Azure.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "[concat('storage', uniqueString(resourceGroup().id))]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "location": "[resourceGroup().location]",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ]
}
```

Quando cria a especificação do modelo, os comandos PowerShell ou CLI são passados no ficheiro do modelo principal. Se o modelo principal de referências ligados modelos, os comandos irão encontrá-los e empacotá-los para criar a especificação do modelo. Para saber mais, consulte [Criar uma especificação de modelo com modelos ligados.](#create-a-template-spec-with-linked-templates)

Crie uma especificação de modelo utilizando:

```azurepowershell
New-AzTemplateSpec -Name storageSpec -Version 1.0 -ResourceGroupName templateSpecsRg -TemplateJsonFile ./mainTemplate.json
```

Pode ver todas as especificações do modelo na sua subscrição utilizando:

```azurepowershell
Get-AzTemplateSpec
```

Pode ver detalhes de uma especificação de modelo, incluindo as suas versões com:

```azurepowershell
Get-AzTemplateSpec -ResourceGroupName templateSpecsRG -Name storageSpec
```

## <a name="deploy-template-spec"></a>Implementar especificação de modelo

Depois de criar a especificação do modelo, os utilizadores com acesso de **leitura** à especificação do modelo podem implantá-lo. Para obter informações sobre a concessão de acesso, consulte [Tutorial: Conceder a um grupo acesso aos recursos da Azure utilizando a Azure PowerShell](../../role-based-access-control/tutorial-role-assignments-group-powershell.md).

As especificações do modelo podem ser implementadas através do portal, PowerShell, Azure CLI, ou como um modelo ligado numa implementação de modelo maior. Os utilizadores de uma organização podem implementar uma especificação de modelo para qualquer âmbito em Azure (grupo de recursos, subscrição, grupo de gestão ou inquilino).

Em vez de passar por um caminho ou URI para um modelo, você implementa uma especificação de modelo fornecendo o seu ID de recurso. O ID de recursos tem o seguinte formato:

**/subscrições/{subscrição-id}/resourceGroups/{resource-group}/providers/Microsoft.Resources/templateSpecs/{template-spec-name}/versões/{modelo-versão**

Note que o ID do recurso inclui um número de versão para a especificação do modelo.

Por exemplo, implementa uma especificação de modelo com o seguinte comando PowerShell.

```azurepowershell
$id = "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/templateSpecsRG/providers/Microsoft.Resources/templateSpecs/storageSpec/versions/1.0"

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG
```

Na prática, normalmente corre `Get-AzTemplateSpec` para obter o ID da especificação do modelo que pretende implementar.

```azurepowershell
$id = (Get-AzTemplateSpec -Name storageSpec -ResourceGroupName templateSpecsRg -Version 1.0).Version.Id

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG
```

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
                    "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'networkingSpec', '1.0')]"
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
                    "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0')]"
                }
            }
        }
    ],
    "outputs": {}
}
```

Para obter mais informações sobre as especificações do modelo de ligação, consulte [Tutorial: Implemente uma especificação do modelo como um modelo ligado](template-specs-deploy-linked-template.md).

## <a name="versioning"></a>Controlo de versões

Quando cria uma especificação de modelo, fornece-se um número de versão para o mesmo. Ao iterar no código do modelo, pode atualizar uma versão existente (para hotfixes) ou publicar uma nova versão. A versão é uma cadeia de texto. Pode optar por seguir qualquer sistema de versão, incluindo a versão semântica. Os utilizadores da especificação do modelo podem fornecer o número de versão que pretendem utilizar ao implementá-lo.

## <a name="next-steps"></a>Passos seguintes

* Para criar e implementar uma especificação de modelo, consulte [Quickstart: Criar e implementar a especificação do modelo](quickstart-create-template-specs.md).

* Para obter mais informações sobre os modelos de ligação nas especificações do modelo, consulte [Tutorial: Crie uma especificação de modelo com modelos ligados](template-specs-create-linked.md).

* Para obter mais informações sobre a implementação de uma especificação de modelo como um modelo ligado, consulte [Tutorial: Implemente uma especificação de modelo como um modelo ligado](template-specs-deploy-linked-template.md).
