---
title: Modos de implementação do Azure Resource Manager | Documentos da Microsoft
description: Descreve como especificar se pretende utilizar um modo de implantação completas ou incrementais com o Azure Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2019
ms.author: tomfitz
ms.openlocfilehash: 5213affe953636c46486614ee2a020d7727e1478
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57407541"
---
# <a name="azure-resource-manager-deployment-modes"></a>Modos de implementação do Azure Resource Manager

Quando a implementação de recursos, especifica que a implementação é uma atualização incremental ou uma atualização completa.  A principal diferença entre esses dois modos é como o Resource Manager processa os recursos existentes no grupo de recursos que não estão no modelo. O modo predefinido é incremental.

Em ambos os modos, Gestor de recursos tenta criar todos os recursos especificados no modelo. Se o recurso já existe no grupo de recursos e as respetivas definições são iguais, nenhuma operação está a ser utilizada para esse recurso. Se alterar os valores de propriedade para um recurso, o recurso é atualizado com os novos valores. Se tentar atualizar a localização ou o tipo de um recurso existente, a implementação falhar com um erro. Em vez disso, implementar um novo recurso com a localização ou escreva o que precisa.

## <a name="complete-mode"></a>Modo de conclusão

No modo de conclusão, Gestor de recursos **elimina** recursos de que existem no grupo de recursos, mas não estão especificados no modelo. Recursos que são especificados no modelo, mas não implementados porque um [condição](resource-group-authoring-templates.md#condition) for avaliada como falsa, não são eliminados.

Há alguma diferença no como tipos de recurso lidar com as eliminações de modo completa. Recursos de principal são automaticamente eliminados quando não num modelo que é implementada no modo de conclusão. Alguns recursos filho não são automaticamente eliminados quando não estiver no modelo. No entanto, esses recursos subordinados são eliminados se o recurso principal é eliminado. 

Por exemplo, se o seu grupo de recursos contém uma zona DNS (tipo de recurso Microsoft.Network/dnsZones) e um registo CNAME (tipo de recurso Microsoft.Network/dnsZones/CNAME), a zona DNS é o recurso principal para o registo CNAME. Se implementar com o modo de conclusão e não incluem a zona DNS no seu modelo, a zona DNS e o registo CNAME são ambos eliminados. Se incluir a zona DNS no seu modelo, mas não incluem o registo CNAME, não é eliminado o CNAME. 

Para obter uma lista de como tipos de recurso lidar com a eliminação, veja [recursos de eliminação do Azure para implementações no modo completa](complete-mode-deletion.md).

> [!NOTE]
> Apenas os modelos de nível de raiz suportam o modo de implantação completa. Para [ligada ou para aninhados modelos](resource-group-linked-templates.md), tem de utilizar o modo de incremental. 
>
> [Implementações de nível de subscrição](deploy-to-subscription.md) não suporta o modo de conclusão.
>
> Atualmente, o portal não suporta o modo de conclusão.
>

## <a name="incremental-mode"></a>Modo de incremental

No modo de incremental, Gestor de recursos **deixa inalterado** recursos de que existem no grupo de recursos, mas não estão especificados no modelo. Quando voltar a implementar um recurso no modo de incremental, especifique todos os valores de propriedade para o recurso, não apenas aqueles que estiver a atualizar. Se não especificar determinadas propriedades, o Resource Manager interpreta a atualização como substituir esses valores.

## <a name="example-result"></a>Resultado de exemplo

Para ilustrar a diferença entre os modos de incrementais e completas, considere o seguinte cenário.

**Grupo de recursos** contém:

* Recurso A
* Recurso B
* Recurso C

**Modelo** contém:

* Recurso A
* Recurso B
* Recurso D

Quando implementado num **incremental** modo, o grupo de recursos tem:

* Recurso A
* Recurso B
* Recurso C
* Recurso D

Quando implementado num **concluída** modo, C de recurso é eliminado. O grupo de recursos tem:

* Recurso A
* Recurso B
* Recurso D

## <a name="set-deployment-mode"></a>Definir o modo de implementação

Para definir o modo de implementação ao implementar com o PowerShell, utilize o `Mode` parâmetro.

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Mode Complete `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json
```

Para definir o modo de implementação ao implementar com a CLI do Azure, utilize o `mode` parâmetro.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --mode Complete \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

O exemplo seguinte mostra um modelo ligado definido para o modo de implantação incremental:

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

## <a name="next-steps"></a>Passos Seguintes

* Para saber mais sobre a criação de modelos do Resource Manager, veja [modelos Authoring Azure Resource Manager](resource-group-authoring-templates.md).
* Para saber mais sobre a implementação de recursos, veja [implementar uma aplicação com o modelo Azure Resource Manager](resource-group-template-deploy.md).
* Para ver as operações de um fornecedor de recursos, consulte [API REST do Azure](/rest/api/).
