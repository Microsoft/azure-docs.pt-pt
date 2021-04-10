---
title: Impor segurança com políticas sobre VMs do Windows em Azure
description: Como aplicar uma política a um Gestor de Recursos Azure Windows Virtual Machine
author: mimckitt
manager: vashan
ms.service: virtual-machines
ms.subservice: security
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 08/02/2017
ms.author: mimckitt
ms.openlocfilehash: 4a4e54510c4683dc1be9da09b96d6289136a26f1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102550268"
---
# <a name="apply-policies-to-windows-vms-with-azure-resource-manager"></a>Aplicar políticas a VMs do Windows com gestor de recursos Azure
Ao usar políticas, uma organização pode impor várias convenções e regras em toda a empresa. A aplicação do comportamento pretendido pode ajudar a mitigar o risco, contribuindo para o sucesso da organização. Neste artigo, descrevemos como pode usar as políticas do Gestor de Recursos Azure para definir o comportamento desejado para as Máquinas Virtuais da sua organização.

Para uma introdução às políticas, veja [o que é a Política Azure?](../../governance/policy/overview.md)

## <a name="permitted-virtual-machines"></a>Máquinas virtuais permitidas
Para garantir que as máquinas virtuais para a sua organização são compatíveis com uma aplicação, pode restringir os sistemas operativos permitidos. No seguinte exemplo de política, permite a criação apenas de máquinas virtuais do Centro de Dados R2 do Windows Server 2012 R2:

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

Utilize um wild card para modificar a política anterior para permitir qualquer imagem do Centro de Dados do Windows Server:

```json
{
  "field": "Microsoft.Compute/imageSku",
  "like": "*Datacenter"
}
```

Utilize qualquer Of para modificar a política anterior para permitir qualquer Datacenter Do Windows Server 2012 ou imagem superior:

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

Para obter informações sobre os campos [políticos, consulte pseudónimos de política.](../../governance/policy/concepts/definition-structure.md#aliases)

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

Por razões de segurança, pode exigir que apenas imagens personalizadas aprovadas sejam implementadas no seu ambiente. Pode especificar o grupo de recursos que contém as imagens aprovadas ou as imagens aprovadas específicas.

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

O exemplo a seguir especifica os IDs de imagem aprovados:

```json
{
    "field": "Microsoft.Compute/imageId",
    "in": ["{imageId1}","{imageId2}"]
}
```

## <a name="virtual-machine-extensions"></a>Extensões de máquina virtual

Pode querer proibir o uso de certos tipos de extensões. Por exemplo, uma extensão pode não ser compatível com certas imagens de máquinas virtuais personalizadas. O exemplo a seguir mostra como bloquear uma extensão específica. Utiliza editor e tipo para determinar qual extensão para bloquear.

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

Quando tiver uma licença no local, pode poupar a taxa de licença nas suas máquinas virtuais. Quando não tem a licença, deve proibir a opção. A seguinte política proíbe a utilização do Benefício de Utilização Híbrida Azure (AHUB):

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
* Depois de definir uma regra política (como mostrado nos exemplos anteriores), é necessário criar a definição de política e atribuí-la a um âmbito. O âmbito pode ser uma subscrição, grupo de recursos ou recurso. Para atribuir políticas, consulte [o portal Use Azure para atribuir e gerir políticas de recursos](../../governance/policy/assign-policy-portal.md), Use [PowerShell para atribuir políticas](../../governance/policy/assign-policy-powershell.md), ou Use [Azure CLI para atribuir políticas](../../governance/policy/assign-policy-azurecli.md).
* Para uma introdução às políticas de recursos, veja [o que é a Política Azure?](../../governance/policy/overview.md)
* Para obter documentação de orientação sobre como as empresas podem utilizar o Resource Manager para gerir subscrições de forma eficaz, consulte [Azure enterprise scaffold - prescriptive subscription governance (Andaime empresarial do Azure - governação de subscrições prescritivas)](/azure/architecture/cloud-adoption-guide/subscription-governance).
