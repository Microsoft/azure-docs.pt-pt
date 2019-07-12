---
title: Impor segurança com as políticas em VMs do Windows no Azure | Documentos da Microsoft
description: Como aplicar uma política à máquina Virtual do Azure Resource Manager Windows
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 0b71ba54-01db-43ad-9bca-8ab358ae141b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: kasing
ms.openlocfilehash: 75a8d5d48d5024b35b39792a481e600692581d95
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67720006"
---
# <a name="apply-policies-to-windows-vms-with-azure-resource-manager"></a>Aplicar políticas às VMs do Windows com o Azure Resource Manager
Ao utilizar políticas, uma organização pode impor várias convenções e regras em toda a empresa. Imposição do comportamento desejado pode ajudar a mitigar o risco ao mesmo tempo que contribuem para o sucesso da organização. Neste artigo, descrevemos como pode utilizar políticas do Azure Resource Manager para definir o comportamento desejado para máquinas de virtuais de sua organização.

Para obter uma introdução às políticas, consulte [o que é o Azure Policy?](../../governance/policy/overview.md).

## <a name="permitted-virtual-machines"></a>Máquinas de virtuais permitidas
Para garantir que as máquinas virtuais para a sua organização são compatíveis com uma aplicação, pode restringir os sistemas de operativos permitidos. No seguinte exemplo de política, permitir apenas Windows Server 2012 R2 Datacenter as máquinas virtuais a ser criado:

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
                "MicrosoftWindowsServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "WindowsServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "2012-R2-Datacenter"
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

Utilize um caráter universal para modificar a política anterior para permitir que qualquer imagem do Windows Server Datacenter:

```json
{
  "field": "Microsoft.Compute/imageSku",
  "like": "*Datacenter"
}
```

Utilize anyOf para modificar a política anterior para permitir que qualquer Datacenter do Windows Server 2012 R2 ou superior imagem:

```json
{
  "anyOf": [
    {
      "field": "Microsoft.Compute/imageSku",
      "like": "2012-R2-Datacenter*"
    },
    {
      "field": "Microsoft.Compute/imageSku",
      "like": "2016-Datacenter*"
    }
  ]
}
```

Para obter informações sobre os campos de política, consulte [aliases de política](../../governance/policy/concepts/definition-structure.md#aliases).

## <a name="managed-disks"></a>Managed disks

Para exigir a utilização de discos geridos, use a seguinte política:

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

## <a name="images-for-virtual-machines"></a>Imagens de máquinas virtuais

Por motivos de segurança, pode exigir que apenas aprovadas imagens personalizadas são implementadas no seu ambiente. Pode especificar qualquer grupo de recursos que contém as imagens aprovadas ou imagens de aprovada a específica.

O exemplo seguinte requer imagens a partir de um grupo de recursos aprovados:

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

O exemplo seguinte especifica os IDs de imagem aprovada:

```json
{
    "field": "Microsoft.Compute/imageId",
    "in": ["{imageId1}","{imageId2}"]
}
```

## <a name="virtual-machine-extensions"></a>Extensões de máquina virtual

Pode pretender proibir o uso de certos tipos de extensões. Por exemplo, uma extensão pode não ser compatível com determinadas imagens de máquina virtual personalizada. O exemplo seguinte mostra como bloquear uma extensão específica. Ele usa o publicador e o tipo para determinar qual extensão para bloquear.

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


## <a name="azure-hybrid-use-benefit"></a>Benefício Híbrido do Azure

Quando tiver uma licença de acesso no local, pode salvar o valor da licença nas suas máquinas virtuais. Quando não tem a licença, deve proibir a opção. A seguinte política proíbe a utilização de benefício de utilização de híbrida do Azure (AHUB):

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in":[ "Microsoft.Compute/virtualMachines","Microsoft.Compute/VirtualMachineScaleSets"]
            },
            {
                "field": "Microsoft.Compute/licenseType",
                "exists": true
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

## <a name="next-steps"></a>Passos Seguintes
* Depois de definir uma regra de política (conforme mostrado nos exemplos anteriores), terá de criar a definição de política e atribuí-lo a um âmbito. O âmbito pode ser uma subscrição, grupo de recursos ou recurso. Para atribuir políticas, veja [portal do Azure de utilização para atribuir e gerir políticas de recursos](../../governance/policy/assign-policy-portal.md), [utilize o PowerShell para atribuir políticas](../../governance/policy/assign-policy-powershell.md), ou [CLI do Azure de utilização para atribuir políticas](../../governance/policy/assign-policy-azurecli.md).
* Para obter uma introdução às políticas de recursos, consulte [o que é o Azure Policy?](../../governance/policy/overview.md).
* Para obter documentação de orientação sobre como as empresas podem utilizar o Resource Manager para gerir subscrições de forma eficaz, consulte [Azure enterprise scaffold - prescriptive subscription governance (Andaime empresarial do Azure - governação de subscrições prescritivas)](/azure/architecture/cloud-adoption-guide/subscription-governance).