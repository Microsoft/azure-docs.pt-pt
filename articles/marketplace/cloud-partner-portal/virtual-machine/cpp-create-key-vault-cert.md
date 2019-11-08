---
title: Criar um certificado de Azure Key Vault | Azure Marketplace
description: Explica como registrar uma VM por meio de um VHD implantado pelo Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/29/2018
ms.author: pabutler
ms.openlocfilehash: 4adc6f716050e2d792e0a5c022972e4340d2846a
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823116"
---
# <a name="create-certificates-for-azure-key-vault"></a>Criar certificados para Azure Key Vault

Este artigo explica como provisionar os certificados autoassinados necessários para estabelecer uma conectividade de Gerenciamento Remoto do Windows (WinRM) com uma VM (máquina virtual) hospedada pelo Azure. Esse processo consiste em três etapas:

1.  Crie o certificado de segurança. 
2.  Crie o Azure Key Vault para armazenar esse certificado. 
3.  Armazene os certificados para esse cofre de chaves. 

Você pode usar um grupo de recursos novo ou existente do Azure para esse trabalho.  A abordagem anterior é usada na explicação a seguir.



[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="create-the-certificate"></a>Criar o certificado

Edite e execute o seguinte script do Azure PowerShell para criar o arquivo de certificado (. pfx) em uma pasta local.  Você precisará substituir os valores para os seguintes parâmetros:

|  **Meter**        |   **Descrição**                                                               |
|  -------------        |   ---------------                                                               |
| `$certroopath` | Pasta local na qual salvar o arquivo. pfx  |
| `$location`    | Um dos locais geográficos do Azure Standard  |
| `$vmName`      | Nome da máquina virtual planejada do Azure   |
| `$certname`    | Nome do certificado; deve corresponder ao nome de domínio totalmente qualificado da VM planejada  |
| `$certpassword` | A senha para os certificados deve corresponder à senha usada para a VM planejada  |
|  |  |

```powershell
    # Certification creation script 
    
    # pfx certification stored path
    $certroopath = "C:\certLocation"
    
    #location of the resource group
    $location = "westus"
    
    # Azure virtual machine name that we are going to create
    $vmName = "testvm000000906"
    
    # Certification name - should match with FQDN of Windows Azure creating VM 
    $certname = "$vmName.$location.cloudapp.azure.com"
    
    # Certification password - should be match with password of Windows Azure creating VM 
    $certpassword = "SecretPassword@123"
    
    $cert=New-SelfSignedCertificate -DnsName "$certname" -CertStoreLocation cert:\LocalMachine\My
    $pwd = ConvertTo-SecureString -String $certpassword -Force -AsPlainText
    $certwithThumb="cert:\localMachine\my\"+$cert.Thumbprint
    $filepath="$certroopath\$certname.pfx"
    Export-PfxCertificate -cert $certwithThumb -FilePath $filepath -Password $pwd
    Remove-Item -Path $certwithThumb 

```
> [!TIP]
> Mantenha a mesma sessão de console do PowerShell ativa durante essas etapas para que os valores dos vários parâmetros sejam retidos.

> [!WARNING]
> Se você salvar esse script, armazene-o somente em um local seguro, pois ele contém informações de segurança (uma senha).


## <a name="create-the-key-vault"></a>Criar o cofre de chaves

Copie o conteúdo do [modelo de implantação do cofre de chaves](./cpp-key-vault-deploy-template.md) para um arquivo em seu computador local. (no script de exemplo abaixo, esse recurso é `C:\certLocation\keyvault.json`.)  Edite e execute o seguinte script do Azure PowerShell para criar uma instância de Azure Key Vault e o grupo de recursos associado.  Você precisará substituir os valores para os seguintes parâmetros:

|  **Meter**        |   **Descrição**                                                               |
|  -------------        |   ---------------                                                               |
| `$postfix`            | Cadeia de caracteres numérica arbitrária anexada aos identificadores de implantação                     |
| `$rgName`             | Nome do grupo de recursos do Azure (RG) para criar                                        |
|  `$location`          | Um dos locais geográficos do Azure Standard                                  |
| `$kvTemplateJson`     | Caminho do arquivo (keyvault. JSON) que contém o modelo do Resource Manager para o cofre de chaves |
| `$kvname`             | Nome do novo cofre de chaves                                                       |
|  |  |

```powershell
    # Creating Key vault in resource group
    
    # "Random" number for deployment identifiers
    $postfix = "0101048"
    
    # Resource group name
    $rgName = "TestRG$postfix"
    
    # Location of Resource Group
    $location = "westus"
    
    # Key vault template location
    $kvTemplateJson = "C:\certLocation\keyvault.json"
    
    # Key vault name
    $kvname = "isvkv$postfix"
    
    # code snippet to get the Azure user object ID
    try
       {
        $accounts = Get-AzureAccount
        $accountNum = 0
        $accounts.Id | %{ ++$accountNum; Write-Host $accountNum $_}
        Write-Host "`nPlease select User, e.g. 1:" -ForegroundColor DarkYellow
        [Int] $accountChoice = Read-Host 
        
        While($accountChoice -lt 1 -or $accountChoice -gt $accounts.Length)
        {
            Write-Host "incorrect input" -ForegroundColor Red 
            Write-Host "`nPlease select User, e.g. 1:" -ForegroundColor DarkYellow
            [Int] $accountChoice = Read-Host
        }
        
        $accountSelected = $accounts[$accountChoice-1]
        echo $accountSelected
        $id = $accountSelected.Id
                              
        Write-Host "User $id Selected"
        $myobjectId=(Get-AzADUser -Mail $id)[0].Id
      }
      catch
      {
      Write-Host $_.Exception.Message    
      Break
      } 

    # code snippet to get Azure User Tenant Id
      # SELECT Subscriptions
        #**************************************
        try
        {
        $subslist=Get-AzureSubscription
        for($i=1; $i -le $subslist.Length;$i++)
        {
           Write-Host ($i.ToString() +":"+ $subslist[$i-1].SubscriptionName)
        }
        Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
        [int] $selectedsub=Read-Host
    
        While($selectedsub -lt 1 -or $selectedsub -gt $subslist.Length)
        {
            Write-Host "incorrect input" -ForegroundColor Red 
            for($i=1; $i -le $subslist.Length;$i++)
             {
              Write-Host ($i.ToString() +":"+ $subslist[$i-1].SubscriptionName)
             }
            Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
           [int] $selectedsub=Read-Host
        }
        if($selectedsub -ge 1 -and $selectedsub -le $subslist.Length)
        {
        $mysubid=$subslist[$selectedsub-1].SubscriptionId
        $mysubName=$subslist[$selectedsub-1].SubscriptionName
        $mytenantId=$subslist[$selectedsub-1].TenantId
        Write-Host "$mysubName selected"
        }
        }
        catch
        {
        Write-Host $_.Exception.Message    
        Break
        }
    
    # Create a resource group
     Write-Host "Creating Resource Group $rgName"
     Create-ResourceGroup -rgName $rgName -location $location
     Write-Host "-----------------------------------" 
    
    # Create key vault and configure access
    New-AzResourceGroupDeployment -Name "kvdeploy$postfix" -ResourceGroupName $rgName -TemplateFile $kvTemplateJson -keyVaultName $kvname -tenantId $mytenantId -objectId $myobjectId
    
    Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $myobjectId -PermissionsToKeys all -PermissionsToSecrets all 
        
```

## <a name="store-the-certificate"></a>Armazenar o certificado

Agora você pode armazenar os certificados, contidos no arquivo. pfx, no novo cofre de chaves executando o script a seguir. 

```powershell
    #push certificate to key vault secret

     $fileName =$certroopath+"\$certname"+".pfx" 
      
     $fileContentBytes = get-content $fileName -Encoding Byte
     $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    
            $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certpassword"
    }
"@
            echo $certpassword
            $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
            $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
            $objAzureKeyVaultSecret=Set-AzureKeyVaultSecret -VaultName $kvname -Name "ISVSecret$postfix" -SecretValue $secret
            echo $objAzureKeyVaultSecret.Id 
    
```


## <a name="next-steps"></a>Passos seguintes

Em seguida, você [implantará uma VM da imagem de VM do usuário](./cpp-deploy-vm-user-image.md).
