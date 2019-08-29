---
title: Impor segurança com políticas em VMs do Linux no Azure | Microsoft Docs
description: Como aplicar uma política a uma máquina virtual Azure Resource Manager Linux
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 06778ab4-f8ff-4eed-ae10-26a276fc3faa
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 08/02/2017
ms.author: kasing
ms.openlocfilehash: c0399044e1776d10a70cf4bcb1dca8d87e4981c7
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70091584"
---
# <a name="apply-policies-to-linux-vms-with-azure-resource-manager"></a>Aplicar políticas a VMs Linux com Azure Resource Manager
Usando políticas, uma organização pode impor várias convenções e regras em toda a empresa. A imposição do comportamento desejado pode ajudar a reduzir o risco enquanto contribui para o sucesso da organização. Neste artigo, descrevemos como você pode usar as políticas de Azure Resource Manager para definir o comportamento desejado para as máquinas virtuais da sua organização.

Para obter uma introdução às políticas, consulte [o que é Azure Policy?](../../governance/policy/overview.md).

## <a name="permitted-virtual-machines"></a>Máquinas virtuais permitidas
Para garantir que as máquinas virtuais da sua organização sejam compatíveis com um aplicativo, você pode restringir os sistemas operacionais permitidos. No exemplo de política a seguir, você permite que somente máquinas virtuais Ubuntu 14.04.2-LTS sejam criadas.

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "in": [
          "Microsoft.Compute/disks",
          "Microsoft.Compute/virtualMachines",
          "Microsoft.Compute/VirtualMachineScaleSets"
        ]
      },
      {
        "not": {
          "allOf": [
            {
              "field": "Microsoft.Compute/imagePublisher",
              "in": [
                "Canonical"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "UbuntuServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "14.04.2-LTS"
              ]
            },
            {
              "field": "Microsoft.Compute/imageVersion",
              "in": [
                "latest"
              ]
            }
          ]
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Use um curinga para modificar a política anterior para permitir qualquer imagem do Ubuntu LTS: 

```json
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*LTS"
}
```

Para obter informações sobre os campos de política, consulte [aliases de política](../../governance/policy/concepts/definition-structure.md#aliases).

## <a name="managed-disks"></a>Managed disks

Para exigir o uso de discos gerenciados, use a seguinte política:

```json
{
  "if": {
    "anyOf": [
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/virtualMachines"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/osDisk.uri",
            "exists": true
          }
        ]
      },
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/VirtualMachineScaleSets"
          },
          {
            "anyOf": [
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osDisk.vhdContainers",
                "exists": true
              },
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl",
                "exists": true
              }
            ]
          }
        ]
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="images-for-virtual-machines"></a>Imagens para máquinas virtuais

Por motivos de segurança, você pode exigir que apenas imagens personalizadas aprovadas sejam implantadas em seu ambiente. Você pode especificar o grupo de recursos que contém as imagens aprovadas ou as imagens aprovadas específicas.

O exemplo a seguir requer imagens de um grupo de recursos aprovado:

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in": [
                    "Microsoft.Compute/virtualMachines",
                    "Microsoft.Compute/VirtualMachineScaleSets"
                ]
            },
            {
                "not": {
                    "field": "Microsoft.Compute/imageId",
                    "contains": "resourceGroups/CustomImage"
                }
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
} 
```

O exemplo a seguir especifica as IDs de imagem aprovadas:

```json
{
    "field": "Microsoft.Compute/imageId",
    "in": ["{imageId1}","{imageId2}"]
}
```

## <a name="virtual-machine-extensions"></a>Extensões de máquina virtual

Talvez você queira proibir o uso de determinados tipos de extensões. Por exemplo, uma extensão pode não ser compatível com determinadas imagens de máquina virtual personalizada. O exemplo a seguir mostra como bloquear uma extensão específica. Ele usa Publisher e Type para determinar qual extensão bloquear.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "equals": "{extension-type}"

      }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```


## <a name="next-steps"></a>Passos Seguintes
* Depois de definir uma regra de política (conforme mostrado nos exemplos anteriores), você precisa criar a definição de política e atribuí-la a um escopo. O escopo pode ser uma assinatura, um grupo de recursos ou um recurso. Para atribuir políticas, consulte [usar portal do Azure para atribuir e gerenciar políticas de recursos](../../governance/policy/assign-policy-portal.md), [usar o PowerShell para atribuir políticas](../../governance/policy/assign-policy-powershell.md)ou [usar CLI do Azure para atribuir políticas](../../governance/policy/assign-policy-azurecli.md).
* Para obter uma introdução às políticas de recursos, consulte [o que é Azure Policy?](../../governance/policy/overview.md).
* Para obter documentação de orientação sobre como as empresas podem utilizar o Resource Manager para gerir subscrições de forma eficaz, consulte [Azure enterprise scaffold - prescriptive subscription governance (Andaime empresarial do Azure - governação de subscrições prescritivas)](/azure/architecture/cloud-adoption-guide/subscription-governance).
