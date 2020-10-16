---
title: Encriptação do disco Azure e escala de máquina virtual Azure conjuntos de sequenciação de extensão
description: Neste artigo, saiba como ativar a encriptação do disco do Microsoft Azure para Os VMs do Linux IaaS.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 1aff05e51bcbc99f33325efb905ade819ae22e02
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90988028"
---
# <a name="use-azure-disk-encryption-with-virtual-machine-scale-set-extension-sequencing"></a>Use encriptação de disco Azure com sequenciação de extensão de conjunto de escala de máquina virtual

Extensões como encriptação de disco Azure podem ser adicionadas a uma escala de máquinas virtuais Azure definida numa ordem especificada. Para tal, utilize [a sequência de extensão](virtual-machine-scale-sets-extension-sequencing.md). 

Em geral, a encriptação deve ser aplicada a um disco:

- Após extensões ou scripts personalizados que preparam os discos ou volumes.
- Antes de extensões ou scripts personalizados que acedam ou consomem os dados nos discos ou volumes encriptados.

Em qualquer dos `provisionAfterExtensions` casos, a propriedade designa qual extensão deve ser adicionada mais tarde na sequência.

## <a name="sample-azure-templates"></a>Modelos de Azure de amostra

Se desejar que a Encriptação do Disco Azure seja aplicada após outra extensão, coloque a `provisionAfterExtensions` propriedade no bloco de extensão AzureDiskEncryption. 

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

Aqui está um exemplo usando "AzureDiskEncryption" seguido de "VMDiagnosticsSettings", uma extensão que fornece capacidades de monitorização e diagnóstico num Azure VM baseado no Windows:


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

Para obter um modelo mais aprofundado, consulte:
* Aplique a extensão de encriptação do disco Azure após um script de concha personalizado que formata o disco (Linux): [deploy-extseq-linux-ADE-after-customscript.jsem](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-linux-ADE-after-customscript.json)


## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre a sequência de extensão: [Provisão de extensão de sequência em conjuntos de escala de máquina virtual](virtual-machine-scale-sets-extension-sequencing.md).
- Saiba mais sobre a `provisionAfterExtensions` propriedade: [Microsoft.Compute virtualMachineScaleSets/extensions referência do modelo](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions).
- [Encriptação do disco Azure para conjuntos de escala de máquinas virtuais](disk-encryption-overview.md)
- [Criptografe conjuntos de escala de máquina virtual usando o Azure CLI](disk-encryption-cli.md)
- [Criptografe conjuntos de escala de máquina virtual usando o Azure PowerShell](disk-encryption-powershell.md)
- [Criar e configurar um cofre chave para encriptação de disco Azure](disk-encryption-key-vault.md)
