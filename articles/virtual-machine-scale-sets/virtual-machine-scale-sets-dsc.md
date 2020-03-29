---
title: Usando a configuração de estado desejada com conjuntos de escala de máquina virtual
description: Utilizando conjuntos de escala de máquina virtual com a extensão de configuração do Estado Do Estado Do Azure para configurar máquinas virtuais.
author: zjalexander
tags: azure-service-management,azure-resource-manager
ms.assetid: c8f047b5-0e6c-4ef3-8a47-f1b284d32942
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.date: 04/05/2017
ms.author: zachal
ms.openlocfilehash: a93a8a9c27be5a1736a50e6c4c4b830980b7d974
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278081"
---
# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Utilização de conjuntos de escala de máquina virtual com a extensão DSC Azure
[Os conjuntos](virtual-machine-scale-sets-overview.md) de escala de máquina virtual podem ser utilizados com o manipulador de extensão de configuração do [Estado Pretendido (DSC) azure.](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) Os conjuntos de escala de máquinas virtuais fornecem uma forma de implantar e gerir um grande número de máquinas virtuais, e podem escalar elásticomente para dentro e para fora em resposta à carga. A DSC é usada para configurar os VMs à medida que entram online, pelo que estão a executar o software de produção.

## <a name="differences-between-deploying-to-virtual-machines-and-virtual-machine-scale-sets"></a>Diferenças entre a implantação para máquinas virtuais e conjuntos de escala de máquinas virtuais
A estrutura do modelo subjacente para um conjunto de escala de máquina virtual é ligeiramente diferente de um único VM. Especificamente, um único VM implementa extensões sob o nó "virtualMachines". Há uma entrada de "extensões" tipo onde dSC é adicionado ao modelo

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

Um nó de conjunto de escala de máquina virtual tem uma secção "propriedades" com o atributo "VirtualMachineProfile", "extensionProfile". DSC é adicionado em "extensões"

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

## <a name="behavior-for-a-virtual-machine-scale-set"></a>Comportamento para um conjunto de escala de máquina virtual
O comportamento de um conjunto de escala de máquina virtual é idêntico ao comportamento de um único VM. Quando um novo VM é criado, é automaticamente provisionado com a extensão DSC. Se uma versão mais recente do WMF for necessária pela extensão, o VM reinicia antes de entrar online. Uma vez on-line, ele descarrega a configuração DSC .zip e disponibiliza-a no VM. Mais detalhes podem ser encontrados na [visão geral da extensão DoD Azure](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-steps"></a>Passos seguintes
Examine o modelo do Gestor de [Recursos Azure para a extensão DSC](../virtual-machines/windows/extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Saiba como a [extensão DSC lida com segurança as credenciais](../virtual-machines/windows/extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Para obter mais informações sobre o manipulador de extensão Azure DSC, consulte introdução ao manipulador de extensão de [configuração de configuração do Estado Pretendido .](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 

Para mais informações sobre o PowerShell DSC, visite o centro de [documentação PowerShell](/powershell/scripting/dsc/overview/overview). 

