---
title: Usando a configuração de estado desejado com conjuntos de dimensionamento de máquinas virtuais
description: Usando conjuntos de dimensionamento de máquinas virtuais com a extensão de configuração de estado desejado do Azure para configurar máquinas virtuais.
services: virtual-machine-scale-sets
documentationcenter: ''
author: zjalexander
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
keywords: ''
ms.assetid: c8f047b5-0e6c-4ef3-8a47-f1b284d32942
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 04/05/2017
ms.author: zachal
ms.openlocfilehash: d3d064bc1d9c0a72b10ca27515d8325e7ca7fef9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75359303"
---
# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Usando conjuntos de dimensionamento de máquinas virtuais com a extensão DSC do Azure
Os [conjuntos de dimensionamento de máquinas virtuais](virtual-machine-scale-sets-overview.md) podem ser usados com o manipulador de extensão [DSC (configuração de estado desejado) do Azure](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) . Os conjuntos de dimensionamento de máquinas virtuais fornecem uma maneira de implantar e gerenciar um grande número de máquinas virtuais e podem reduzir e reduzir de forma elástica em resposta à carga. A DSC é usada para configurar as VMs à medida que elas ficam online para que estejam executando o software de produção.

## <a name="differences-between-deploying-to-virtual-machines-and-virtual-machine-scale-sets"></a>Diferenças entre implantar em máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais
A estrutura de modelo subjacente para um conjunto de dimensionamento de máquinas virtuais é ligeiramente diferente de uma única VM. Especificamente, uma única VM implanta extensões no nó "virtualMachines". Há uma entrada do tipo "Extensions" em que a DSC é adicionada ao modelo

```
"resources": [
          {
              "name": "Microsoft.Powershell.DSC",
              "type": "extensions",
              "location": "[resourceGroup().location]",
              "apiVersion": "2015-06-15",
              "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "tags": {
                  "displayName": "dscExtension"
              },
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": false,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "DscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }
              }
          }
      ]
```

Um nó do conjunto de dimensionamento de máquinas virtuais tem uma seção "Propriedades" com o atributo "VirtualMachineProfile", "extensionProfile". O DSC é adicionado sob "extensões"

```
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": false,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
```

## <a name="behavior-for-a-virtual-machine-scale-set"></a>Comportamento de um conjunto de dimensionamento de máquinas virtuais
O comportamento de um conjunto de dimensionamento de máquinas virtuais é idêntico ao comportamento de uma única VM. Quando uma nova VM é criada, ela é automaticamente provisionada com a extensão de DSC. Se uma versão mais recente do WMF for exigida pela extensão, a VM será reinicializada antes de ficar online. Quando ele estiver online, ele baixará a configuração de DSC. zip e o provisioná na VM. Mais detalhes podem ser encontrados na [visão geral da extensão de DSC do Azure](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-steps"></a>Passos seguintes
Examine o [modelo de Azure Resource Manager para a extensão de DSC](../virtual-machines/windows/extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Saiba como a [extensão DSC manipula com segurança as credenciais](../virtual-machines/windows/extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Para obter mais informações sobre o manipulador de extensões de DSC do Azure, consulte [introdução ao manipulador de extensão de configuração de estado desejado do Azure](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Para obter mais informações sobre o DSC [do PowerShell, visite o centro de documentação do PowerShell](/powershell/scripting/dsc/overview/overview). 

