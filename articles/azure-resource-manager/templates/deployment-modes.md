---
title: Modos de implementação
description: Descreve como especificar se deve utilizar um modo de implementação completo ou incremental com o Azure Resource Manager.
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: 45eee255cec06925095ed0696c669b5c205f8b56
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97724413"
---
# <a name="azure-resource-manager-deployment-modes"></a>Modos de implementação do Azure Resource Manager

Ao implementar os seus recursos, especifica que a implementação é uma atualização incremental ou uma atualização completa. A diferença entre estes dois modos é a forma como o Gestor de Recursos lida com os recursos existentes no grupo de recursos que não estão no modelo.

Para ambos os modos, o Gestor de Recursos tenta criar todos os recursos especificados no modelo. Se o recurso já existir no grupo de recursos e as suas definições forem inalteradas, não será necessária qualquer operação para esse recurso. Se alterar os valores de propriedade para um recurso, o recurso é atualizado com esses novos valores. Se tentar atualizar a localização ou o tipo de recurso existente, a implementação falha com um erro. Em vez disso, implemente um novo recurso com a localização ou tipo de que necessita.

O modo predefinido é incremental.

## <a name="complete-mode"></a>Modo completo

Em modo completo, o Gestor de Recursos elimina os recursos **existentes** no grupo de recursos, mas não estão especificados no modelo.

> [!NOTE]
> Utilize sempre o [funcionamento do "e se"](template-deploy-what-if.md) antes de colocar um modelo em modo completo. E se lhe mostrar quais os recursos que serão criados, eliminados ou modificados. Use o que se para evitar apagar involuntariamente recursos.

Se o seu modelo incluir um recurso que não é implementado porque [a condição](conditional-resource-deployment.md) avalia a falsa, o resultado depende da versão API REST que utiliza para implementar o modelo. Se utilizar uma versão anterior a 2019-05-10, o recurso **não é eliminado**. Com 2019-05-10 ou posteriormente, o recurso **é eliminado.** As versões mais recentes da Azure PowerShell e do Azure CLI apagam o recurso.

Tenha cuidado ao utilizar o modo completo com [laços de cópia](copy-resources.md). Quaisquer recursos que não sejam especificados no modelo após a resolução do ciclo de cópia são eliminados.

Se implementar para [mais de um grupo de recursos num modelo,](./deploy-to-resource-group.md)os recursos do grupo de recursos especificados na operação de implantação podem ser eliminados. Os recursos nos grupos de recursos secundários não são eliminados.

Existem algumas diferenças na forma como os tipos de recursos lidam com as eliminações completas do modo. Os recursos dos pais são automaticamente eliminados quando não estão num modelo que é implantado em modo completo. Alguns recursos infantis não são automaticamente eliminados quando não estão no modelo. No entanto, estes recursos para crianças são eliminados se o recurso dos pais for eliminado.

Por exemplo, se o seu grupo de recursos contiver uma zona DE DNS `Microsoft.Network/dnsZones` (tipo de recurso) e um registo CNAME `Microsoft.Network/dnsZones/CNAME` (tipo de recurso), a zona DE DNS é o recurso principal para o registo CNAME. Se implementar com o modo completo e não incluir a zona DNS no seu modelo, a zona DE DNS e o registo CNAME são ambos eliminados. Se incluir a zona DNS no seu modelo, mas não incluir o registo CNAME, o CNAME não é eliminado.

Para obter uma lista de como os tipos de recursos lidam com a eliminação, consulte [a Eliminação dos recursos Azure para implementações completas do modo](complete-mode-deletion.md).

Se o grupo de recursos estiver [bloqueado,](../management/lock-resources.md)o modo completo não apaga os recursos.

> [!NOTE]
> Apenas os modelos de nível de raiz suportam o modo de implementação completo. Para [modelos ligados ou aninhados,](linked-templates.md)deve utilizar o modo incremental.
>
> [As implementações do nível de subscrição](deploy-to-subscription.md) não suportam o modo completo.
>
> Atualmente, o portal não suporta o modo completo.
>

## <a name="incremental-mode"></a>Modo incremental

Em modo incremental, o Gestor de Recursos deixa recursos **inalterados** que existem no grupo de recursos mas não são especificados no modelo. Os recursos no modelo **são adicionados** ao grupo de recursos.

> [!NOTE]
> Ao recolocar um recurso existente em modo incremental, todas as propriedades são reaplicadas. As **propriedades não são adicionadas gradualmente.** Um mal-entendido comum é pensar que as propriedades que não são especificadas no modelo são deixadas inalteradas. Se não especificar determinadas propriedades, o Gestor de Recursos interpreta a implementação como uma sobreposição desses valores. As propriedades que não estão incluídas no modelo são reiniciadas para os valores predefinidos. Especifique todos os valores não predefinidos para o recurso, e não apenas os que está a atualizar. A definição de recursos no modelo contém sempre o estado final do recurso. Não pode representar uma atualização parcial a um recurso existente.
>
> Em casos raros, as propriedades que especifica para um recurso são realmente implementadas como um recurso infantil. Por exemplo, quando fornece valores de configuração do site para uma aplicação web, esses valores são implementados no tipo de recurso infantil `Microsoft.Web/sites/config` . Se recolocar a aplicação web e especificar um objeto vazio para os valores de configuração do site, o recurso da criança não é atualizado. No entanto, se fornecer novos valores de configuração do site, o tipo de recurso para criança é atualizado.

## <a name="example-result"></a>Exemplo resultado

Para ilustrar a diferença entre modos incrementais e completos, considere o seguinte cenário.

**O Grupo de Recursos** contém:

* Recurso A
* Recurso B
* Recursos C

**O modelo** contém:

* Recurso A
* Recurso B
* Recursos D

Quando implementado em modo **incremental,** o grupo de recursos tem:

* Recurso A
* Recurso B
* Recursos C
* Recursos D

Quando implantado em modo **completo,** o recurso C é eliminado. O grupo de recursos tem:

* Recurso A
* Recurso B
* Recursos D

## <a name="set-deployment-mode"></a>Definir o modo de implementação

Para definir o modo de implementação ao ser implantado com o PowerShell, utilize o `Mode` parâmetro.

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Mode Complete `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json
```

Para definir o modo de implantação ao ser implantado com O Azure CLI, utilize o `mode` parâmetro.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --mode Complete \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

O exemplo a seguir mostra um modelo ligado definido para o modo de implementação incremental:

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

* Para saber sobre a criação de modelos de Gestor de Recursos, consulte [compreender a estrutura e a sintaxe dos modelos ARM](template-syntax.md).
* Para saber sobre a implantação de recursos, consulte [implementar recursos com modelos ARM e Azure PowerShell](deploy-powershell.md).
* Para visualizar as operações de um fornecedor de recursos, consulte [a AZure REST API](/rest/api/).
