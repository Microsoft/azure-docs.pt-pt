---
title: Impor segurança com políticas sobre VMs Linux em Azure
description: Como aplicar uma política a uma máquina virtual Do Gestor de Recursos Azure Linux
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
ms.openlocfilehash: 7ab48430ae4d6585c908b53017122096175abac3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74035326"
---
# <a name="apply-policies-to-linux-vms-with-azure-resource-manager"></a>Aplicar políticas aos VMs Linux com o Gestor de Recursos Azure
Ao usar políticas, uma organização pode impor várias convenções e regras em toda a empresa. A aplicação do comportamento desejado pode ajudar a mitigar o risco, contribuindo para o sucesso da organização. Neste artigo, descrevemos como pode usar as políticas do Gestor de Recursos Azure para definir o comportamento desejado para as Máquinas Virtuais da sua organização.

Para uma introdução às políticas, veja [o que é a Política Azure?](../../governance/policy/overview.md)

## <a name="permitted-virtual-machines"></a>Máquinas Virtuais Permitidas
Para garantir que as máquinas virtuais para a sua organização são compatíveis com uma aplicação, pode restringir os sistemas operativos permitidos. No exemplo da política a seguir, permite a criação de apenas Máquinas Virtuais Ubuntu 14.04.2-LTS.

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

Utilize um wild card para modificar a política anterior para permitir qualquer imagem Ubuntu LTS: 

```json
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*LTS"
}
```

Para obter informações sobre os domínios políticos, consulte [pseudónimos políticos.](../../governance/policy/concepts/definition-structure.md#aliases)

## <a name="managed-disks"></a>Managed disks

Para exigir a utilização de discos geridos, utilize a seguinte política:

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

## <a name="images-for-virtual-machines"></a>Imagens para Máquinas Virtuais

Por razões de segurança, pode exigir que apenas as imagens personalizadas aprovadas sejam implementadas no seu ambiente. Pode especificar o grupo de recursos que contém as imagens aprovadas ou as imagens aprovadas específicas.

O exemplo que se segue requer imagens de um grupo de recursos aprovados:

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

O exemplo que se segue especifica as iDs de imagem aprovadas:

```json
{
    "field": "Microsoft.Compute/imageId",
    "in": ["{imageId1}","{imageId2}"]
}
```

## <a name="virtual-machine-extensions"></a>Extensões de Máquina Virtual

Pode proibir o uso de certos tipos de extensões. Por exemplo, uma extensão pode não ser compatível com certas imagens de máquinas virtuais personalizadas. O exemplo que se segue mostra como bloquear uma extensão específica. Utiliza o editor e o tipo para determinar qual a extensão a bloquear.

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


## <a name="next-steps"></a>Passos seguintes
* Depois de definir uma regra de política (como mostram os exemplos anteriores), é necessário criar a definição de política e atribuí-la a um âmbito. O âmbito pode ser uma subscrição, grupo de recursos ou recurso. Para atribuir políticas, consulte [o portal Use Azure para atribuir e gerir políticas](../../governance/policy/assign-policy-portal.md)de recursos , use [powerShell para atribuir políticas](../../governance/policy/assign-policy-powershell.md), ou use o [Azure CLI para atribuir políticas](../../governance/policy/assign-policy-azurecli.md).
* Para uma introdução às políticas de recursos, veja o que é a [Política Azure?](../../governance/policy/overview.md)
* Para obter documentação de orientação sobre como as empresas podem utilizar o Resource Manager para gerir subscrições de forma eficaz, consulte [Azure enterprise scaffold - prescriptive subscription governance (Andaime empresarial do Azure - governação de subscrições prescritivas)](/azure/architecture/cloud-adoption-guide/subscription-governance).
