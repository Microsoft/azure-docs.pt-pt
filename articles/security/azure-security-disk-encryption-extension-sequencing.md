---
title: Sequenciamento de extensão de conjuntos de dimensionamento de máquinas virtuais do Azure Disk Encryption e o Azure
description: Este artigo fornece instruções sobre como ativar o Microsoft Azure Disk Encryption para VMs de IaaS Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/21/2019
ms.openlocfilehash: 33958eae291b03b568491e24f09abefdacb667c8
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58405906"
---
# <a name="use-azure-disk-encryption-with-virtual-machine-scale-set-extension-sequencing"></a>Sequenciamento de extensão do conjunto de utilização do Azure Disk Encryption com o dimensionamento de máquinas virtuais

As extensões, como a encriptação de disco do Azure podem ser adicionadas a um dimensionamento de máquinas virtuais do Azure definido numa ordem especificada. Para tal, utilize [sequenciamento de extensão](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md). 

Em geral, a criptografia deve ser aplicada a um disco:

- Depois de extensões ou scripts personalizados que preparam o discos ou volumes.
- Antes de extensões ou scripts personalizados que acedem ou consumam os dados nos volumes ou discos encriptados.

Em ambos os casos, o `provisionAfterExtensions` propriedade designa a extensão de que deve ser adicionado mais tarde na sequência.

## <a name="sample-azure-templates"></a>Modelos do Azure de exemplo

Se pretender que o Azure Disk Encryption aplicada após a outra extensão, colocar o `provisionAfterExtensions` propriedade no bloco de extensão AzureDiskEncryption. 

Eis um exemplo de uso "Extensão Customscript", um script do Powershell que inicializa e formata um disco do Windows, seguido de "AzureDiskEncryption":

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

Se pretender que o Azure Disk Encryption aplicadas antes de outra extensão, colocar o `provisionAfterExtensions` propriedade no bloco da extensão a seguir.

Eis um exemplo de uso "AzureDiskEncryption", seguido de "VMDiagnosticsSettings", uma extensão que fornece a monitorização e recursos de diagnósticos numa VM do Azure baseado no Windows:


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

Para obter mais modelos detalhados, consulte:
* Aplicar a extensão Azure Disk Encryption depois de um script de shell personalizado que formata o suporte técnico (Linux): [deploy-extseq-linux-ADE-after-customscript.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-linux-ADE-after-customscript.json)
* Aplicar a extensão Azure Disk Encryption depois de um script de Powershell personalizado que inicializa e formata o disco (Windows): [deploy-extseq-linux-ADE-after-customscript.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-windows-ADE-after-customscript.json)
* Aplique a extensão Azure Disk Encryption antes de um script de Powershell personalizado que inicializa e formata o disco (Windows): [deploy-extseq-windows-CustomScript-after-ADE.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-windows-CustomScript-after-ADE.json)

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre a sequenciação de extensão: [Aprovisionamento de extensão em conjuntos de dimensionamento de máquina virtual de sequência](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md).
- Saiba mais sobre o `provisionAfterExtensions` propriedade: [Referência de modelo do Microsoft. Compute virtualMachineScaleSets/extensões](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions).
