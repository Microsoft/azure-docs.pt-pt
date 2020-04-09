---
title: Impor segurança com políticas em VMs windows em Azure
description: Como aplicar uma política a uma máquina virtual do Gestor de Recursos Azure Windows
services: virtual-machines-windows
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 0b71ba54-01db-43ad-9bca-8ab358ae141b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 08/02/2017
ms.author: mimckitt
ms.openlocfilehash: cb7580929a40683603263db03f2cfca0e1824233
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80873808"
---
# <a name="apply-policies-to-windows-vms-with-azure-resource-manager"></a>Aplicar políticas para VMs Windows com Gestor de Recursos Azure
Ao usar políticas, uma organização pode impor várias convenções e regras em toda a empresa. A aplicação do comportamento desejado pode ajudar a mitigar o risco, contribuindo para o sucesso da organização. Neste artigo, descrevemos como pode usar as políticas do Gestor de Recursos Azure para definir o comportamento desejado para as Máquinas Virtuais da sua organização.

Para uma introdução às políticas, veja [o que é a Política Azure?](../../governance/policy/overview.md)

## <a name="permitted-virtual-machines"></a>Máquinas Virtuais Permitidas
Para garantir que as máquinas virtuais para a sua organização são compatíveis com uma aplicação, pode restringir os sistemas operativos permitidos. No exemplo da política seguinte, permite acriação apenas de Máquinas Virtuais do Windows Server 2012 R2 Datacenter:

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

Utilize um wild card para modificar a política anterior para permitir qualquer imagem do Datacenter do Windows Server:

```json
{
  "field": "Microsoft.Compute/imageSku",
  "like": "*Datacenter"
}
```

Utilize qualquer Um Of para modificar a política anterior para permitir qualquer Datacenter Do Windows Server 2012 R2 ou imagem superior:

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


## <a name="azure-hybrid-use-benefit"></a>Benefício Híbrido do Azure

Quando tiver uma licença no local, pode poupar a taxa de licença nas suas máquinas virtuais. Quando não tem a licença, deve proibir a opção. A seguinte política proíbe a utilização de Benefício sinuoso de Utilização Azure (AHUB):

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

## <a name="next-steps"></a>Passos seguintes
* Depois de definir uma regra de política (como mostram os exemplos anteriores), é necessário criar a definição de política e atribuí-la a um âmbito. O âmbito pode ser uma subscrição, grupo de recursos ou recurso. Para atribuir políticas, consulte [o portal Use Azure para atribuir e gerir políticas](../../governance/policy/assign-policy-portal.md)de recursos , use [powerShell para atribuir políticas](../../governance/policy/assign-policy-powershell.md), ou use o [Azure CLI para atribuir políticas](../../governance/policy/assign-policy-azurecli.md).
* Para uma introdução às políticas de recursos, veja o que é a [Política Azure?](../../governance/policy/overview.md)
* Para obter documentação de orientação sobre como as empresas podem utilizar o Resource Manager para gerir subscrições de forma eficaz, consulte [Azure enterprise scaffold - prescriptive subscription governance (Andaime empresarial do Azure - governação de subscrições prescritivas)](/azure/architecture/cloud-adoption-guide/subscription-governance).
