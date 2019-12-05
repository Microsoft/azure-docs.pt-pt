---
title: Azure Key Vault extensão de VM para Linux
description: Implante um agente executando a atualização automática de certificados de Key Vault em máquinas virtuais usando uma extensão de máquina virtual.
services: virtual-machines-linux
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: d6b8cdf43fea63fa4709dd5fc5319bb92ddefc63
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806978"
---
# <a name="key-vault-virtual-machine-extension-for-linux"></a>Extensão de máquina virtual Key Vault para Linux

A extensão de VM Key Vault fornece a atualização automática de certificados armazenados em um cofre de chaves do Azure. Especificamente, a extensão monitora uma lista de certificados observados armazenados em cofres de chaves.  Após detectar uma alteração, a extensão recupera e instala os certificados correspondentes. A extensão de VM Key Vault é publicada e suportada pela Microsoft, atualmente em VMs Linux. Este documento detalha as plataformas, configurações e opções de implantação com suporte para a extensão de VM Key Vault para Linux. 

### <a name="operating-system"></a>Sistema operativo

A extensão de VM Key Vault dá suporte a essas distribuições do Linux:

- Ubuntu-1604
- Ubuntu-1804
- Debian-9
- SuSE-15 

## <a name="extension-schema"></a>Esquema de extensão

O JSON a seguir mostra o esquema para a extensão de VM Key Vault. A extensão não requer configurações protegidas. todas as suas configurações são consideradas informações sem impacto de segurança. A extensão requer uma lista de segredos monitorados, a frequência de sondagem e o repositório de certificados de destino. Especificamente:  
```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForLinux",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "linkOnRenewal": <Not available on Linux e.g.: false>,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        }      
      }
      }
    }
```

> [!NOTE]
> Suas URLs de certificados observadas devem estar no formato `https://myVaultName.vault.azure.net/secrets/myCertName`.
> 
> Isso ocorre porque o caminho `/secrets` retorna o certificado completo, incluindo a chave privada, enquanto o caminho de `/certificates` não faz isso. Mais informações sobre certificados podem ser encontradas aqui: [Key Vault certificados](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-certificates)


### <a name="property-values"></a>Valores de propriedade

| Nome | Valor / exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| publicador | Microsoft.Azure.KeyVault | string |
| tipo | KeyVaultForLinux | string |
| typeHandlerVersion | 1.0 | int |
| pollingIntervalInS | 3600 | string |
| certificateStoreName | MY | string |
| linkOnRenewal | false | boolean |
| certificateStoreLocation  | LocalMachine | string |
| requiredInitialSync | true | boolean |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate"] | matriz de cadeia de caracteres


## <a name="template-deployment"></a>Implementação de modelos

Extensões VM do Azure podem ser implementadas com modelos Azure Resource Manager. Os modelos são ideais ao implantar uma ou mais máquinas virtuais que exigem a atualização pós-implantação de certificados. A extensão pode ser implantada em VMs individuais ou conjuntos de dimensionamento de máquinas virtuais. O esquema e a configuração são comuns a ambos os tipos de modelo. 

A configuração JSON para uma extensão de máquina virtual deve ser aninhada dentro do fragmento de recurso de máquina virtual do modelo, especificamente `"resources": []` objeto para o modelo de máquina virtual e no caso do conjunto de dimensionamento de máquinas virtuais em `"virtualMachineProfile":"extensionProfile":{"extensions" :[]` objeto.

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KeyVaultForLinux",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        }      
      }
      }
    }
```


## <a name="azure-powershell-deployment"></a>Implantação de Azure PowerShell

O Azure PowerShell pode ser usado para implantar a extensão de VM Key Vault em uma máquina virtual existente ou em um conjunto de dimensionamento de máquinas virtuais. 

* Para implantar a extensão em uma VM:
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName =  "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
       
    
        # Start the deployment
        Set-AzVmExtension -TypeHandlerVersion "1.0" -ResourceGroupName <ResourceGroupName> -Location <Location> -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* Para implantar a extensão em um conjunto de dimensionamento de máquinas virtuais:

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
        
        # Add Extension to VMSS
        $vmss = Get-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName>
        Add-AzVmssExtension -VirtualMachineScaleSet $vmss  -Name $extName -Publisher $extPublisher -Type $extType -TypeHandlerVersion "1.0" -Setting $settings

        # Start the deployment
        Update-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName> -VirtualMachineScaleSet $vmss 
    
    ```

## <a name="azure-cli-deployment"></a>Implementação de CLI do Azure

O CLI do Azure pode ser usado para implantar a extensão de VM Key Vault em uma máquina virtual existente ou em um conjunto de dimensionamento de máquinas virtuais. 
 
* Para implantar a extensão em uma VM:
    
    ```azurecli
       # Start the deployment
         az vm extension set -n "KeyVaultForLinux" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

* Para implantar a extensão em um conjunto de dimensionamento de máquinas virtuais:

   ```azurecli
        # Start the deployment
        az vmss extension set -n "KeyVaultForLinux" `
        --publisher Microsoft.Azure.KeyVault `
        -g "<resourcegroup>" `
        --vm-name "<vmName>" `
        --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

Esteja ciente das seguintes restrições/requisitos:
- Restrições de Key Vault:
  - Ele deve existir no momento da implantação 
  - A política de acesso Key Vault está definida para a identidade VM/VMSS usando o MSI


## <a name="troubleshoot-and-support"></a>Resolução de problemas e suporte

### <a name="troubleshoot"></a>Resolução de problemas

Os dados sobre o estado das implantações de extensão podem ser recuperados do portal do Azure e usando o Azure PowerShell. Para ver o estado de implantação das extensões de uma determinada VM, execute o comando a seguir usando o Azure PowerShell.

## <a name="azure-powershell"></a>Azure PowerShell
```powershell
Get-AzVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

## <a name="azure-cli"></a>CLI do Azure
```azurecli
 az vm get-instance-view --resource-group <resource group name> --name  <vmName> --query "instanceView.extensions"
```

### <a name="support"></a>Suporte

Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure sobre o [fóruns do Azure do MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione o suporte de Get. Para informações sobre como utilizar o suporte do Azure, leia os [FAQ do suporte Microsoft Azure](https://azure.microsoft.com/support/faq/).