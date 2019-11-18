---
title: Modos de implementação
description: Descreve como especificar se deseja usar um modo de implantação completo ou incremental com Azure Resource Manager.
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 44111787736d4b80cbdd68263b137d67de2218ba
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149885"
---
# <a name="azure-resource-manager-deployment-modes"></a>Azure Resource Manager modos de implantação

Ao implantar seus recursos, você especifica que a implantação é uma atualização incremental ou uma atualização completa.  A diferença entre esses dois modos é como o Resource Manager lida com os recursos existentes no grupo de recursos que não estão no modelo. O modo padrão é incremental.

Para ambos os modos, o Resource Manager tenta criar todos os recursos especificados no modelo. Se o recurso já existir no grupo de recursos e suas configurações estiverem inalteradas, nenhuma operação será executada para esse recurso. Se você alterar os valores de propriedade de um recurso, o recurso será atualizado com esses novos valores. Se você tentar atualizar o local ou o tipo de um recurso existente, a implantação falhará com um erro. Em vez disso, implante um novo recurso com o local ou tipo de que você precisa.

## <a name="complete-mode"></a>Modo completo

No modo completo, o Gerenciador de recursos **exclui** os recursos que existem no grupo de recursos, mas não são especificados no modelo.

Se o seu modelo inclui um recurso que não está implantado porque a [condição](conditional-resource-deployment.md) é avaliada como false, o resultado depende de qual versão da API REST você usa para implantar o modelo. Se você usar uma versão anterior à 2019-05-10, o recurso **não será excluído**. Com o 2019-05-10 ou posterior, o recurso **é excluído**. As versões mais recentes do Azure PowerShell e CLI do Azure excluir o recurso.

Tenha cuidado ao usar o modo completo com [loops de cópia](resource-group-create-multiple.md). Todos os recursos que não são especificados no modelo após a resolução do loop de cópia são excluídos.

Se você implantar em [mais de um grupo de recursos em um modelo](resource-manager-cross-resource-group-deployment.md), os recursos no grupo de recursos especificado na operação de implantação poderão ser excluídos. Os recursos nos grupos de recursos secundários não são excluídos.

Há algumas diferenças em como os tipos de recursos lidam com exclusões de modo completo. Os recursos pai são excluídos automaticamente quando não estão em um modelo implantado no modo completo. Alguns recursos filho não são excluídos automaticamente quando não estão no modelo. No entanto, esses recursos filho serão excluídos se o recurso pai for excluído. 

Por exemplo, se o grupo de recursos contiver uma zona DNS (tipo de recurso Microsoft. Network/dnsZones) e um registro CNAME (tipo de recurso Microsoft. Network/dnsZones/CNAME), a zona DNS será o recurso pai do registro CNAME. Se você implantar o com o modo completo e não incluir a zona DNS em seu modelo, a zona DNS e o registro CNAME serão excluídos. Se você incluir a zona DNS em seu modelo, mas não incluir o registro CNAME, o CNAME não será excluído. 

Para obter uma lista de como os tipos de recursos lidam com a exclusão, consulte [exclusão de recursos do Azure para implantações de modo completo](complete-mode-deletion.md).

Se o grupo de recursos estiver [bloqueado](resource-group-lock-resources.md), o modo completo não excluirá os recursos.

> [!NOTE]
> Somente modelos de nível raiz dão suporte ao modo de implantação completo. Para [modelos vinculados ou aninhados](resource-group-linked-templates.md), você deve usar o modo incremental. 
>
> [Implantações de nível de assinatura](deploy-to-subscription.md) não dão suporte ao modo completo.
>
> Atualmente, o portal não dá suporte ao modo completo.
>

## <a name="incremental-mode"></a>Modo incremental

No modo incremental, o Gerenciador de recursos deixa os recursos **inalterados** que existem no grupo de recursos, mas não são especificados no modelo.

No entanto, ao reimplantar um recurso existente no modo incremental, o resultado é diferente. Especifique todas as propriedades para o recurso, não apenas aquelas que você está atualizando. Um mal-entendido comum é imaginar que as propriedades que não são especificadas sejam deixadas inalteradas. Se você não especificar determinadas propriedades, o Resource Manager interpretará a atualização ao substituir esses valores.

## <a name="example-result"></a>Resultado de exemplo

Para ilustrar a diferença entre os modos incremental e completo, considere o cenário a seguir.

O **grupo de recursos** contém:

* Recurso A
* Recurso B
* Recurso C

O **modelo** contém:

* Recurso A
* Recurso B
* Recurso D

Quando implantado no modo **incremental** , o grupo de recursos tem:

* Recurso A
* Recurso B
* Recurso C
* Recurso D

Quando implantado no modo **completo** , o recurso C é excluído. O grupo de recursos tem:

* Recurso A
* Recurso B
* Recurso D

## <a name="set-deployment-mode"></a>Definir modo de implantação

Para definir o modo de implantação ao implantar com o PowerShell, use o parâmetro `Mode`.

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Mode Complete `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json
```

Para definir o modo de implantação ao implantar com CLI do Azure, use o parâmetro `mode`.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --mode Complete \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

O exemplo a seguir mostra um modelo vinculado definido para o modo de implantação incremental:

```json
"resources": [
  {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          <nested-template-or-external-template>
      }
  }
]
```

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre como criar modelos do Resource Manager, confira [criação de modelos de Azure Resource Manager](resource-group-authoring-templates.md).
* Para saber mais sobre a implantação de recursos, consulte [implantar um aplicativo com Azure Resource Manager modelo](resource-group-template-deploy.md).
* Para exibir as operações de um provedor de recursos, consulte [API REST do Azure](/rest/api/).
