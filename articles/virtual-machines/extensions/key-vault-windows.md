---
title: Extensão VM do cofre da chave Azure para windows
description: Implemente um agente que executa a atualização automática dos segredos do Key Vault em máquinas virtuais utilizando uma extensão de máquina virtual.
services: virtual-machines-windows
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: c02b6bcb6587313f84c4b900fba49df540d12c07
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966365"
---
# <a name="key-vault-virtual-machine-extension-for-windows"></a>Extensão da máquina virtual key Vault para Windows

A extensão Key Vault VM fornece uma atualização automática dos certificados armazenados num cofre de chaves Azure. Especificamente, a extensão monitoriza uma lista de certificados observados armazenados em cofres-chave e, ao detetar uma alteração, recupera e instala os certificados correspondentes. Este documento detalha as plataformas, configurações e opções de implementação suportadas para a extensão VM do Cofre chave para windows. 

### <a name="operating-system"></a>Sistema operativo

A extensão VM do Cofre-Chave suporta versões abaixo do Windows:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012

A extensão Key Vault VM também é suportada em VM local personalizado que é carregado e convertido em uma imagem especializada para uso em Azure usando a instalação do núcleo do Windows Server 2019.

### <a name="supported-certificate-content-types"></a>Tipos de conteúdo de certificado suportado

- #12 PKCS
- PEM

## <a name="prerequisities"></a>Pré-requisitos
  - Caso do Cofre com certificado. Ver [Criar um cofre de chaves](https://docs.microsoft.com/azure/key-vault/general/quick-create-portal)
  - VM/VMSS deve ter atribuído [identidade gerida](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)
  - A Política de Acesso ao Cofre-Chave deve ser definida com segredos `get` e `list` permissão para vM/VMSS identidade gerida para recuperar a parte de um certificado secreto. Ver [como autenticar para o cofre](/azure/key-vault/general/authentication) de chaves e atribuir uma política de acesso ao cofre de [chaves](/azure/key-vault/general/assign-access-policy-cli).

## <a name="extension-schema"></a>Esquema de extensão

O JSON seguinte mostra o esquema para a extensão VM do Cofre de Chaves. A extensão não requer configurações protegidas - todas as suas configurações são consideradas informações públicas. A extensão requer uma lista de certificados monitorizados, frequência de sondagens e a loja de certificados de destino. Especificamente:  

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "linkOnRenewal": <Only Windows. This feature ensures s-channel binding when certificate renews, without necessitating a re-deployment.  e.g.: false>,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
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
| publicador | Microsoft.Azure.KeyVault | string |
| tipo | KeyVaultForWindows | string |
| typeHandlerVersion | 1,0 | int |
| sondagensIntervalInS | 3600 | string |
| certificadoStoreName | MY | string |
| linkOnRenewal | false | boolean |
| certificaStoreLocalização  | LocalMachine ou CurrentUser (sensível a casos) | string |
| requerinitialSync | true | boolean |
| certificados observados  | ["https://myvault.vault.azure.net/secrets/mycertificate","https://myvault.vault.azure.net/secrets/mycertificate2"] | matriz de cordas
| msiEndpoint | http://169.254.169.254/metadata/identity | string |
| msiClientId | c7373ae5-91c2-4165-8ab6-7381d6e75619 | string |


## <a name="template-deployment"></a>Implementação de modelos

As extensões Azure VM podem ser implementadas com modelos Azure Resource Manager. Os modelos são ideais quando se implanta uma ou mais máquinas virtuais que requerem atualização de envio pós-implantação de certificados. A extensão pode ser implantada em VMs individuais ou conjuntos de balanças de máquinas virtuais. O esquema e a configuração são comuns a ambos os tipos de modelos. 

A configuração JSON para uma extensão de máquina virtual deve ser aninhada dentro do fragmento de recurso de máquina virtual do modelo, especificamente `"resources": []` objeto para o modelo de máquina virtual e em caso de escala de máquina virtual definida sob `"virtualMachineProfile":"extensionProfile":{"extensions" :[]` objeto.

 > [!NOTE]
> A extensão VM exigiria que o sistema ou identidade gerida pelo utilizador fosse atribuído para autenticar o cofre key.  Veja [como autenticar o Key Vault e atribuir uma política de acesso ao Cofre de Chaves.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)
> 

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KeyVaultForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: ["https://myvault.vault.azure.net/secrets/mycertificate", "https://myvault.vault.azure.net/secrets/mycertificate2"]>
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
        '", "observedCertificates": ["' + <observedCert1> + '","' + <observedCert2> + '"] } }'
        $extName =  "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
       
    
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
        '", "observedCertificates": ["' + <observedCert1> + '","' + <observedCert2> + '"] } }'
        $extName = "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
        
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
         az vm extension set -name "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         -resource-group "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```

* Para implantar a extensão num conjunto de escala de máquina virtual:

   ```azurecli
        # Start the deployment
        az vmss extension set -name "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         -resource-group "<resourcegroup>" `
         --vmss-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```

Por favor, esteja ciente das seguintes restrições/requisitos:
- Restrições do Cofre chave:
  - Deve existir no momento da implantação 
  - A Política de Acesso ao Cofre de Chaves deve ser definida para identidade VM/VMSS utilizando uma identidade gerida. Ver [como autenticar para o cofre](../../key-vault/general/authentication.md) de chaves e atribuir uma política de acesso ao cofre de [chaves](../../key-vault/general/assign-access-policy-cli.md).

## <a name="troubleshoot-and-support"></a>Resolução de problemas e apoio

### <a name="frequently-asked-questions"></a>Perguntas Mais Frequentes

* Existe um limite para o número de certificados observados que pode configurar?
  Não, a extensão VM do cofre não tem limite no número de Certificados observados.

### <a name="troubleshoot"></a>Resolução de problemas

Os dados sobre o estado das extensões podem ser recuperados a partir do portal Azure e utilizando o Azure PowerShell. Para ver o estado de implantação das extensões para um determinado VM, executar o seguinte comando utilizando o Azure PowerShell.

**Azure PowerShell**
```powershell
Get-AzVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

**CLI do Azure**
```azurecli
 az vm get-instance-view --resource-group <resource group name> --name  <vmName> --query "instanceView.extensions"
```

#### <a name="logs-and-configuration"></a>Registos e configuração

```
%windrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.KeyVault.KeyVaultForWindows\<version>\akvvm_service_<date>.log
```

### <a name="support"></a>Suporte

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas da Azure nos [fóruns msdn Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode apresentar um incidente de suporte Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para obter informações sobre a utilização do Suporte Azure, leia o [suporte do Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).
