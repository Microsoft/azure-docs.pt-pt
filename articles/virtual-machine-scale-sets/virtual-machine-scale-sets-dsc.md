---
title: Usando o Desired State Configuration com conjuntos de dimensionamento de máquinas virtuais | Documentos da Microsoft
description: Utilizar o dimensionamento de máquinas virtuais conjuntos com a extensão DSC do Azure
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
ms.openlocfilehash: 24a37d352413ff9ac55ce8e189691988383950f3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60203997"
---
# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Utilizar o dimensionamento de máquinas virtuais conjuntos com a extensão DSC do Azure
[Os conjuntos de dimensionamento de máquinas virtuais](virtual-machine-scale-sets-overview.md) pode ser utilizado com o [Azure Desired State Configuration (DSC)](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) manipulador de extensão. Os conjuntos de dimensionamento de máquinas virtuais proporcionam uma forma de implementar e gerir um grande número de máquinas virtuais e podem dimensionar de forma elástica e terminar em resposta a carregar. DSC é utilizado para configurar as VMs à medida que ficam online, para que estão a executar o software de produção.

## <a name="differences-between-deploying-to-virtual-machines-and-virtual-machine-scale-sets"></a>Diferenças entre implementar máquinas virtuais e conjuntos de dimensionamento de Máquina Virtual
A estrutura subjacente do modelo para um conjunto de dimensionamento de máquina virtual é ligeiramente diferente de uma única VM. Especificamente, uma única VM implementa as extensões sob o nó de "virtualMachines". Existe uma entrada do tipo "extensões" onde o DSC é adicionada ao modelo

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

Um nó de conjunto de dimensionamento de máquina virtual tem uma seção de "Propriedades" com o "VirtualMachineProfile", "extensionProfile" atributo. DSC é adicionado em "extensões"

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
O comportamento de um conjunto de dimensionamento de máquina virtual é idêntico para o comportamento de uma única VM. Quando é criada uma nova VM, é automaticamente aprovisionada com a extensão de DSC. Se uma versão mais recente do WMF é necessário para a extensão, a VM é reiniciada antes de entrar online. Quando estiver online, transfere a configuração de DSC. zip e aprovisioná-lo na VM. Podem encontrar mais detalhes no [descrição geral de extensão do DSC do Azure](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-steps"></a>Passos Seguintes
Examine os [modelo Azure Resource Manager para a extensão DSC](../virtual-machines/windows/extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Saiba como o [extensão de DSC com segurança lida com as credenciais](../virtual-machines/windows/extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Para obter mais informações sobre o manipulador de extensão DSC do Azure, consulte [introdução ao manipulador de extensão Azure Desired State Configuration](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Para obter mais informações sobre o DSC de PowerShell, [visite o Centro de documentação do PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 

