---
title: Extensão VM do cofre da chave Azure para Linux
description: Implementar um agente que executa a atualização automática dos certificados key vault em máquinas virtuais utilizando uma extensão de máquina virtual.
services: virtual-machines-linux
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: fa6f569a1a857c09f1e7d1173a5948b1747c05ed
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92124366"
---
# <a name="key-vault-virtual-machine-extension-for-linux"></a>Extensão da máquina virtual key Vault para Linux

A extensão Key Vault VM fornece uma atualização automática dos certificados armazenados num cofre de chaves Azure. Especificamente, a extensão monitoriza uma lista de certificados observados armazenados em cofres-chave.  Ao detetar uma alteração, a extensão recupera e instala os certificados correspondentes. A extensão Key Vault VM é publicada e suportada pela Microsoft, atualmente em VMs Linux. Este documento detalha as plataformas, configurações e opções de implementação suportadas para a extensão VM do Cofre chave para o Linux. 

### <a name="operating-system"></a>Sistema operativo

A extensão Key Vault VM suporta estas distribuições Linux:

- Ubuntu-1604
- Ubuntu-1804
- Debian-9
- Suse-15 

### <a name="supported-certificate-content-types"></a>Tipos de conteúdo de certificado suportado

- #12 PKCS
- PEM

## <a name="prerequisities"></a>Pré-requisitos
  - Caso do Cofre com certificado. Ver [Criar um cofre de chaves](https://docs.microsoft.com/azure/key-vault/general/quick-create-portal)
  - VM/VMSS deve ter atribuído [identidade gerida](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)
  - A Política de Acesso ao Cofre-Chave deve ser definida com segredos `get` e `list` permissão para vM/VMSS identidade gerida para recuperar a parte de um certificado secreto. Ver [como autenticar para o cofre](/azure/key-vault/general/authentication) de chaves e atribuir uma política de acesso ao cofre de [chaves](/azure/key-vault/general/assign-access-policy-cli).

## <a name="extension-schema"></a>Esquema de extensão

O JSON seguinte mostra o esquema para a extensão VM do Cofre de Chaves. A extensão não requer configurações protegidas - todas as suas definições são consideradas informações sem impacto de segurança. A extensão requer uma lista de segredos monitorizados, frequência de sondagens e a loja de certificados de destino. Especificamente:  
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
          "certificateStoreName": <It is ignored on Linux>,
          "linkOnRenewal": <Not available on Linux e.g.: false>,
          "certificateStoreLocation": <disk path where certificate is stored, default: "/var/lib/waagent/Microsoft.Azure.KeyVault">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        },
        "authenticationSettings": {
                "msiEndpoint":  <Optional MSI endpoint e.g.: "http://169.254.169.254/metadata/identity">,
                "msiClientId":  <Optional MSI identity e.g.: "c7373ae5-91c2-4165-8ab6-7381d6e75619">
        }
       }
      }
    }
```

> [!NOTE]
> Os seus certificados observados URLs devem ser do formulário `https://myVaultName.vault.azure.net/secrets/myCertName` .
> 
> Isto porque o `/secrets` caminho devolve o certificado completo, incluindo a chave privada, enquanto o caminho `/certificates` não. Mais informações sobre certificados podem ser encontradas aqui: [Certificados de Cofre Chave](../../key-vault/general/about-keys-secrets-certificates.md)

> [!IMPORTANT]
> A propriedade 'autenticaçõesSettings' só é **necessária** para VMs com **identidades atribuídas pelo utilizador**.
> Especifica a identidade para a autenticação no Cofre de Chaves.


### <a name="property-values"></a>Valores patrimoniais

| Nome | Valor / Exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | data |
| publicador | Microsoft.Azure.KeyVault | cadeia |
| tipo | KeyVaultForLinux | cadeia |
| typeHandlerVersion | 1.0 | int |
| sondagensIntervalInS | 3600 | cadeia |
| certificadoStoreName | É ignorado em Linux | cadeia |
| linkOnRenewal | false | boolean |
| certificaStoreLocalização  | /var/lib/waagent/Microsoft.Azure.KeyVault | cadeia |
| requerinitialSync | true | boolean |
| certificados observados  | ["https://myvault.vault.azure.net/secrets/mycertificate"] | matriz de cordas
| msiEndpoint | http://169.254.169.254/metadata/identity | cadeia |
| msiClientId | c7373ae5-91c2-4165-8ab6-7381d6e75619 | cadeia |


## <a name="template-deployment"></a>Implementação de modelos

As extensões Azure VM podem ser implementadas com modelos Azure Resource Manager. Os modelos são ideais quando se implanta uma ou mais máquinas virtuais que requerem atualização de envio pós-implantação de certificados. A extensão pode ser implantada em VMs individuais ou conjuntos de balanças de máquinas virtuais. O esquema e a configuração são comuns a ambos os tipos de modelos. 

A configuração JSON para uma extensão de máquina virtual deve ser aninhada dentro do fragmento de recurso de máquina virtual do modelo, especificamente `"resources": []` objeto para o modelo de máquina virtual e em caso de escala de máquina virtual definida sob `"virtualMachineProfile":"extensionProfile":{"extensions" :[]` objeto.

 > [!NOTE]
> A extensão VM exigiria que o sistema ou identidade gerida pelo utilizador fosse atribuído para autenticar o cofre key.  Veja [como autenticar o Key Vault e atribuir uma política de acesso ao Cofre de Chaves.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)
> 

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
          "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <ingnored on linux>,
          "certificateStoreLocation": <disk path where certificate is stored, default: "/var/lib/waagent/Microsoft.Azure.KeyVault">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        }      
      }
      }
    }
```


## <a name="azure-powershell-deployment"></a>Implantação Azure PowerShell
> [!WARNING]
> Os clientes PowerShell muitas vezes `\` adicionam-se ao `"` settings.jssobre o qual causará falhas akvvm_service com erro: `[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.`

O Azure PowerShell pode ser utilizado para implantar a extensão VM do Cofre de Chaves a uma máquina virtual ou conjunto de balanças de máquinas virtuais existentes. 

* Para implantar a extensão num VM:
    
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

* Para implantar a extensão num conjunto de escala de máquina virtual:

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

## <a name="azure-cli-deployment"></a>Implantação do Azure CLI

O CLI Azure pode ser utilizado para implantar a extensão VM do Cofre de Chaves a uma máquina virtual ou conjunto de balanças de máquinas virtuais existentes. 
 
* Para implantar a extensão num VM:
    
    ```azurecli
       # Start the deployment
         az vm extension set -n "KeyVaultForLinux" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCerts> \"] }}'
    ```

* Para implantar a extensão num conjunto de escala de máquina virtual:

   ```azurecli
        # Start the deployment
        az vmss extension set -n "KeyVaultForLinux" `
        --publisher Microsoft.Azure.KeyVault `
        -g "<resourcegroup>" `
        --vm-name "<vmName>" `
        --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCerts> \"] }}'
    ```
Por favor, esteja ciente das seguintes restrições/requisitos:
- Restrições do Cofre chave:
  - Deve existir no momento da implantação 
  - A Política de Acesso ao Cofre de Chaves deve ser definida para identidade VM/VMSS utilizando uma identidade gerida. Ver [como autenticar para o cofre](../../key-vault/general/authentication.md) de chaves e atribuir uma política de acesso ao cofre de [chaves](../../key-vault/general/assign-access-policy-cli.md).

## <a name="troubleshoot-and-support"></a>Resolução de problemas e apoio

### <a name="troubleshoot"></a>Resolução de problemas

Os dados sobre o estado das extensões podem ser recuperados a partir do portal Azure e utilizando o Azure PowerShell. Para ver o estado de implantação das extensões para um determinado VM, executar o seguinte comando utilizando o Azure PowerShell.

### <a name="frequently-asked-questions"></a>Perguntas Mais Frequentes

* Existe um limite para o número de certificados observados que pode configurar?
  Não, a extensão VM do cofre não tem limite no número de Certificados observados.
  
## <a name="azure-powershell"></a>Azure PowerShell
```powershell
Get-AzVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

## <a name="azure-cli"></a>CLI do Azure
```azurecli
 az vm get-instance-view --resource-group <resource group name> --name  <vmName> --query "instanceView.extensions"
```
### <a name="logs-and-configuration"></a>Registos e configuração

```
/var/log/waagent.log
/var/log/azure/Microsoft.Azure.KeyVault.KeyVaultForLinux/*
/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-<most recent version>/config/*
```

### <a name="support"></a>Suporte

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas da Azure nos [fóruns msdn Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode apresentar um incidente de suporte Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para obter informações sobre a utilização do Suporte Azure, leia o [suporte do Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).