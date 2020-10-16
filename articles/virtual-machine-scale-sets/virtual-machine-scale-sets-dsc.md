---
title: Usando a configuração de estado desejada com conjuntos de escala de máquina virtual
description: Utilizando conjuntos de balança de máquina virtual com a extensão de configuração do estado Azure desejada para configurar máquinas virtuais.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 6/25/2020
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 20e5bff87d5cd0d6e0a35a558462bb5598bfe3f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87080493"
---
# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Usando conjuntos de balança de máquina virtual com a extensão Azure DSC
[Os conjuntos de balança de máquina virtual](./overview.md) podem ser utilizados com o controlador de [extensão de configuração de estado (DSC) desejado.](../virtual-machines/extensions/dsc-overview.md?toc=/azure/virtual-machines/windows/toc.json) Os conjuntos de escala de máquinas virtuais fornecem uma forma de implantar e gerir um grande número de máquinas virtuais, e podem dimensionar elasticamente para dentro e para fora em resposta à carga. A DSC é usada para configurar os VMs à medida que entram online, por isso estão a executar o software de produção.

## <a name="differences-between-deploying-to-virtual-machines-and-virtual-machine-scale-sets"></a>Diferenças entre implantação em máquinas virtuais e conjuntos de escala de máquinas virtuais
A estrutura subjacente do modelo para um conjunto de escala de máquina virtual é ligeiramente diferente de um único VM. Especificamente, um único VM implementa extensões sob o nó "virtualMachines". Há uma entrada de tipo "extensões" onde dSC é adicionado ao modelo

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

Um nó de conjunto de escala de máquina virtual tem uma secção de "propriedades" com o atributo "VirtualMachineProfile", "extensionProfile". DSC é adicionado em "extensões"

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
O comportamento de um conjunto de escala de máquina virtual é idêntico ao comportamento de um único VM. Quando um novo VM é criado, é automaticamente a provisionado com a extensão DSC. Se for necessária uma versão mais recente do WMF pela extensão, o VM reinicia antes de ficar online. Uma vez online, descarrega a configuração DSC .zip e fornece-a no VM. Mais detalhes podem ser encontrados [na Visão Geral da Extensão Azure DSC](../virtual-machines/extensions/dsc-overview.md?toc=/azure/virtual-machines/windows/toc.json).

## <a name="next-steps"></a>Passos seguintes
Examine o [modelo do Gestor de Recursos Azure para a extensão DSC](../virtual-machines/extensions/dsc-template.md?toc=/azure/virtual-machines/windows/toc.json).

Saiba como a [extensão DSC lida com as credenciais de forma segura](../virtual-machines/extensions/dsc-credentials.md?toc=/azure/virtual-machines/windows/toc.json). 

Para obter mais informações sobre o controlador de extensão Azure DSC, consulte [Introdução ao controlador de extensão de configuração do estado Azure Desired](../virtual-machines/extensions/dsc-overview.md?toc=/azure/virtual-machines/windows/toc.json). 

Para mais informações sobre o PowerShell DSC, [visite o centro de documentação PowerShell](/powershell/scripting/dsc/overview/overview). 
