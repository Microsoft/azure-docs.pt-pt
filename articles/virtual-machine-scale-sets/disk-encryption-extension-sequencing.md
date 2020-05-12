---
title: Encriptação de disco azure e escala de máquina virtual Azure define sequenciação de extensão
description: Este artigo fornece instruções sobre a ativação da encriptação do disco Microsoft Azure para VMs Linux IaaS.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 1c93359486379ecfc8bf6df1f29978ba369f551a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83117262"
---
# <a name="use-azure-disk-encryption-with-virtual-machine-scale-set-extension-sequencing"></a>Utilize encriptação de disco azure com sequência de extensão de extensão de conjunto de máquina virtual

Extensões como encriptação de disco Azure podem ser adicionadas a uma escala de máquinas virtuais Azure definida numa ordem especificada. Para isso, utilize a sequenciação de [extensões.](virtual-machine-scale-sets-extension-sequencing.md) 

Em geral, a encriptação deve ser aplicada a um disco:

- Depois de extensões ou scripts personalizados que preparem os discos ou volumes.
- Antes de extensões ou scripts personalizados que acedem ou consumam os dados nos discos ou volumes encriptados.

Em qualquer dos `provisionAfterExtensions` casos, a propriedade designa qual extensão deve ser adicionada mais tarde na sequência.

## <a name="sample-azure-templates"></a>Modelos de amostra Azure

Se desejar que a Encriptação do Disco Azure seja aplicada após outra extensão, coloque a propriedade no bloco de `provisionAfterExtensions` extensão AzureDiskEncryption. 

Aqui está um exemplo usando "CustomScriptExtension", um script Powershell que inicializa e forma um disco Windows, seguido de "AzureDiskEncryption":

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "name": "CustomScriptExtension",
        "location": "[resourceGroup().location]",
        "properties": {
          "publisher": "Microsoft.Compute",
          "type": "CustomScriptExtension",
          "typeHandlerVersion": "1.9",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "fileUris": [
              "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/ade-vmss/FormatMBRDisk.ps1"
            ]
          },
          "protectedSettings": {
           "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File FormatMBRDisk.ps1"
          }
        }
      },
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "name": "AzureDiskEncryption",
        "location": "[resourceGroup().location]",
        "properties": {
          "provisionAfterExtensions": [
            "CustomScriptExtension"
          ],
          "publisher": "Microsoft.Azure.Security",
          "type": "AzureDiskEncryption",
          "typeHandlerVersion": "2.2",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "EncryptionOperation": "EnableEncryption",
            "KeyVaultURL": "[reference(variables('keyVaultResourceId'),'2018-02-14-preview').vaultUri]",
            "KeyVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionKeyURL": "[parameters('keyEncryptionKeyURL')]",
            "KekVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionAlgorithm": "[parameters('keyEncryptionAlgorithm')]",
            "VolumeType": "[parameters('volumeType')]",
            "SequenceVersion": "[parameters('sequenceVersion')]"
          }
        }
      },
    ]
  }
}
```

Se desejar que a Encriptação do Disco Azure seja aplicada antes de outra extensão, coloque a `provisionAfterExtensions` propriedade no bloco da extensão a seguir.

Aqui está um exemplo usando "AzureDiskEncryption" seguido por "VMDiagnosticsSettings", uma extensão que fornece capacidades de monitorização e diagnóstico num Azure VM baseado no Windows:


```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "AzureDiskEncryption",
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "location": "[resourceGroup().location]",
        "properties": {
          "publisher": "Microsoft.Azure.Security",
          "type": "AzureDiskEncryption",
          "typeHandlerVersion": "2.2",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "EncryptionOperation": "EnableEncryption",
            "KeyVaultURL": "[reference(variables('keyVaultResourceId'),'2018-02-14-preview').vaultUri]",
            "KeyVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionKeyURL": "[parameters('keyEncryptionKeyURL')]",
            "KekVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionAlgorithm": "[parameters('keyEncryptionAlgorithm')]",
            "VolumeType": "[parameters('volumeType')]",
            "SequenceVersion": "[parameters('sequenceVersion')]"
          }
        }
      },
      { 
        "name": "Microsoft.Insights.VMDiagnosticsSettings", 
        "type": "extensions", 
        "location": "[resourceGroup().location]", 
        "apiVersion": "2016-03-30", 
        "dependsOn": [ 
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
        ], 
        "properties": { 
          "provisionAfterExtensions": [
            "AzureDiskEncryption"
          ],
        "publisher": "Microsoft.Azure.Diagnostics", 
          "type": "IaaSDiagnostics", 
          "typeHandlerVersion": "1.5", 
          "autoUpgradeMinorVersion": true, 
          "settings": { 
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
            variables('wadmetricsresourceid'), 
            concat('myVM', copyindex()),
            variables('wadcfgxend')))]", 
            "storageAccount": "[variables('storageName')]" 
          }, 
          "protectedSettings": { 
            "storageAccountName": "[variables('storageName')]", 
            "storageAccountKey": "[listkeys(variables('accountid'), 
              '2015-06-15').key1]", 
            "storageAccountEndPoint": "https://core.windows.net" 
          } 
        } 
      },
    ]
  }
}
```

Para um modelo mais aprofundado, consulte:
* Aplique a extensão de encriptação do disco Azure após um script personalizado de concha que formata o disco (Linux): [implemente-extseq-linux-ADE-after-customscript.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-linux-ADE-after-customscript.json)


## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre a sequenciação de extensões: [Fornecimento de extensão de sequência em conjuntos de escala](virtual-machine-scale-sets-extension-sequencing.md)de máquinavirtual .
- Saiba mais sobre a `provisionAfterExtensions` propriedade: [Microsoft.Compute virtualMachineScaleSets/extensions template reference](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions).
- [Encriptação de disco azure para conjuntos de escala de máquina virtual](disk-encryption-overview.md)
- [Criptografe um conjunto de escala de máquina virtual usando o Azure CLI](disk-encryption-cli.md)
- [Criptografe um conjunto de escala de máquina virtual usando o Azure PowerShell](disk-encryption-powershell.md)
- [Crie e configure um cofre chave para encriptação de disco azure](disk-encryption-key-vault.md)
