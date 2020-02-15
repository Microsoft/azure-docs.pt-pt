---
title: Modos de implementação
description: Descreve como especificar se deve utilizar um modo de implantação completo ou incremental com o Gestor de Recursos Azure.
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 9cc0e8eb20a0375a98906524d4d598ccb473d2a8
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77207592"
---
# <a name="azure-resource-manager-deployment-modes"></a>Modos de implementação do Gestor de Recursos Azure

Ao implementar os seus recursos, especifica que a implementação é uma atualização incremental ou uma atualização completa. A diferença entre estes dois modos é como o Gestor de Recursos lida com os recursos existentes no grupo de recursos que não estão no modelo.

Para ambos os modos, o Gestor de Recursos tenta criar todos os recursos especificados no modelo. Se o recurso já existir no grupo de recursos e as suas definições forem inalteradas, não é tomada nenhuma operação para esse recurso. Se alterar os valores da propriedade por um recurso, o recurso é atualizado com esses novos valores. Se tentar atualizar a localização ou o tipo de recurso existente, a implementação falha com um erro. Em vez disso, implante um novo recurso com a localização ou tipo de que necessita.

O modo predefinido é incremental.

## <a name="complete-mode"></a>Modo completo

Em modo completo, o Gestor de Recursos **elimina** os recursos que existem no grupo de recursos, mas não estão especificados no modelo.

Se o seu modelo inclui um recurso que não é implantado porque a [condição](conditional-resource-deployment.md) avalia para falso, o resultado depende da versão REST API que você usa para implementar o modelo. Se utilizar uma versão mais cedo do que 2019-05-10, o recurso **não é apagado**. Com 2019-05-10 ou mais tarde, o recurso **é apagado.** As versões mais recentes do Azure PowerShell e do Azure CLI apagam o recurso.

Tenha cuidado ao utilizar o modo completo com [os laços](copy-resources.md)de cópia . Quaisquer recursos que não sejam especificados no modelo após a resolução do ciclo de cópia sou eliminado.

Se se implantar em mais de um grupo de [recursos num modelo,](cross-resource-group-deployment.md)os recursos do grupo de recursos especificados na operação de implantação são elegíveis para serem eliminados. Os recursos nos grupos de recursos secundários não são eliminados.

Existem algumas diferenças na forma como os tipos de recursos lidam com a eliminação completa do modo. Os recursos dos pais são automaticamente eliminados quando não estão num modelo que é implantado em modo completo. Alguns recursos infantis não são automaticamente eliminados quando não estão no modelo. No entanto, estes recursos infantis são eliminados se o recurso-mãe for eliminado.

Por exemplo, se o seu grupo de recursos contiver uma zona DNS (tipo de recurso Microsoft.Network/dnsZones) e um registo CNAME (Microsoft.Network/dnsZones/CNAME), a zona DNS é o recurso-mãe para o registo CNAME. Se implementar com o modo completo e não incluir a zona DNS no seu modelo, a zona DNS e o registo CNAME são ambos eliminados. Se incluir a zona DNS no seu modelo, mas não incluir o registo CNAME, o CNAME não é eliminado.

Para obter uma lista de como os tipos de recursos lidam com a eliminação, consulte a [eliminação dos recursos do Azure para implementações completas do modo](complete-mode-deletion.md).

Se o grupo de recursos estiver [bloqueado,](../management/lock-resources.md)o modo completo não apaga os recursos.

> [!NOTE]
> Apenas os modelos de nível raiz suportam o modo de implantação completo. Para [modelos ligados ou aninhados,](linked-templates.md)deve utilizar o modo incremental.
>
> [As implementações de nível](deploy-to-subscription.md) de subscrição não suportam o modo completo.
>
> Atualmente, o portal não suporta o modo completo.
>

## <a name="incremental-mode"></a>Modo incremental

No modo incremental, o Gestor de Recursos deixa recursos **inalterados** que existem no grupo de recursos, mas não estão especificados no modelo. Os recursos no modelo **são adicionados** ao grupo de recursos.

> [!NOTE]
> Ao reimplantar um recurso existente em modo incremental, todas as propriedades são reaplicadas. As **propriedades não são adicionadas incrementalmente.** Um mal-entendido comum é pensar que propriedades que não estão especificadas no modelo são deixadas inalteradas. Se não especificar determinadas propriedades, o Gestor de Recursos interpreta a implementação como sobreposição desses valores. As propriedades que não estão incluídas no modelo são redefinidas para os valores padrão. Especifique todos os valores não predefinidos para o recurso, e não apenas os que está a atualizar. A definição de recursos no modelo contém sempre o estado final do recurso. Não pode representar uma atualização parcial de um recurso existente.

## <a name="example-result"></a>Resultado de exemplo

Para ilustrar a diferença entre modos incrementais e completos, considere o seguinte cenário.

**O Grupo de Recursos** contém:

* Recurso A
* Recurso B
* Recurso C

**O modelo** contém:

* Recurso A
* Recurso B
* Recurso D

Quando implantado em modo **incremental,** o grupo de recursos tem:

* Recurso A
* Recurso B
* Recurso C
* Recurso D

Quando implantado em modo **completo,** o recurso C é eliminado. O grupo de recursos tem:

* Recurso A
* Recurso B
* Recurso D

## <a name="set-deployment-mode"></a>Definir o modo de implementação

Para definir o modo de implantação ao ser implantado com o PowerShell, utilize o parâmetro `Mode`.

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Mode Complete `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json
```

Para definir o modo de implantação ao ser implantado com o Azure CLI, utilize o parâmetro `mode`.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --mode Complete \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

O exemplo seguinte mostra um modelo ligado definido para o modo de implementação incremental:

```json
"resources": [
  {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "properties": {
          "mode": "Incremental",
          <nested-template-or-external-template>
      }
  }
]
```

## <a name="next-steps"></a>Passos seguintes

* Para aprender sobre a criação de modelos de Gestor de Recursos, consulte [os modelos de Gestor de Recursos Do Azure.](template-syntax.md)
* Para aprender sobre a implantação de recursos, consulte [Implementar uma aplicação com o modelo de Gestor](deploy-powershell.md)de Recursos Azure .
* Para visualizar as operações para um fornecedor de recursos, consulte [a API Do REST Azure](/rest/api/).
