---
title: Criar um certificado do Azure Key Vault | O Azure Marketplace
description: Explica como registar uma VM a partir de um VHD implementadas no Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 11/29/2018
ms.author: pabutler
ms.openlocfilehash: 5163aa0a9195aa712fa333667b3f7ccf227469be
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938395"
---
# <a name="create-certificates-for-azure-key-vault"></a>Criar certificados para o Azure Key Vault

Este artigo explica como aprovisionar os certificados autoassinados necessários para estabelecer uma conectividade de gestão remota do Windows (WinRM) a uma máquina de virtual (VM) alojado no Azure. Este processo consiste em três passos:

1.  Crie o certificado de segurança. 
2.  Crie o Azure Key Vault para armazenar este certificado. 
3.  Store os certificados para este Cofre de chaves. 

Pode utilizar um novo ou um grupo de recursos do Azure existente para esse trabalho.  A primeira abordagem é utilizada na explicação seguinte.



[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="create-the-certificate"></a>Criar o certificado

Edite e execute o seguinte script do Azure Powershell para criar o ficheiro de certificado (. pfx) numa pasta local.  Terá de substituir os valores para os seguintes parâmetros:

|  **Parâmetro**        |   **Descrição**                                                               |
|  -------------        |   ---------------                                                               |
| `$certroopath` | Pasta local para guardar o ficheiro. pfx  |
| `$location`    | Uma das localizações geográficas padrão do Azure  |
| `$vmName`      | Nome da máquina virtual do Azure planeada   |
| `$certname`    | Nome do certificado; tem de corresponder ao nome de domínio completamente qualificado da VM planeada  |
| `$certpassword` | Palavra-passe para os certificados, tem de coincidir com a palavra-passe utilizada para a VM planeada  |
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
> Manter a mesma sessão de consola do PowerShell ativa durante estes passos para que os valores de vários parâmetros serão retidos.

> [!WARNING]
> Se salvar esse script, armazená-lo apenas numa localização segura porque contém informações de segurança (uma palavra-passe).


## <a name="create-the-key-vault"></a>Criar o Cofre de chaves

Copie o conteúdo dos [modelo de implementação de Cofre de chaves](./cpp-key-vault-deploy-template.md) para um ficheiro no seu computador local. (no script de exemplo abaixo, este recurso é `C:\certLocation\keyvault.json`.)  Edite e execute o seguinte script do Azure Powershell para criar uma instância do Azure Key Vault e o grupo de recursos associados.  Terá de substituir os valores para os seguintes parâmetros:

|  **Parâmetro**        |   **Descrição**                                                               |
|  -------------        |   ---------------                                                               |
| `$postfix`            | Arbitrária cadeia de caracteres numérica acrescentada para identificadores de implementação                     |
| `$rgName`             | Nome de grupo (RG) de recursos do Azure para criar                                        |
|  `$location`          | Uma das localizações geográficas padrão do Azure                                  |
| `$kvTemplateJson`     | Caminho do ficheiro (keyvault.json) que contém o modelo do Resource Manager para o key vault |
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

## <a name="store-the-certificate"></a>O certificado de Store

Agora pode armazenar os certificados, contidos no ficheiro. pfx, para o novo cofre de chaves executando o seguinte script. 

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
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
            $objAzureKeyVaultSecret=Set-AzureKeyVaultSecret -VaultName $kvname -Name "ISVSecret$postfix" -SecretValue $secret
            echo $objAzureKeyVaultSecret.Id 
    
```


## <a name="next-steps"></a>Passos Seguintes

Em seguida, irá [implementar uma VM a partir da imagem de VM do utilizador](./cpp-deploy-vm-user-image.md).
