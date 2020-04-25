---
title: Crie um certificado de cofre de chave Azure [ Mercado Azure
description: Explica como registar um VM de um VHD implantado em Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 9981f8eda174bbe04b54933528d20d270d360824
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82148245"
---
# <a name="create-certificates-for-azure-key-vault"></a>Criar certificados para o Cofre chave Azure

> [!IMPORTANT]
> A partir de 13 de abril de 2020, começaremos a mover a gestão das suas ofertas de Máquina Virtual Azure para partner Center. Após a migração, você vai criar e gerir suas ofertas no Partner Center. Siga as instruções na certificação de [imagem Azure VM](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-image-certification) para gerir as suas ofertas migratórias.

Este artigo explica como fornecer os certificados auto-assinados necessários para estabelecer uma conectividade de Gestão Remota do Windows (WinRM) a uma máquina virtual hospedada no Azure (VM). Este processo consiste em três etapas:

1.    Crie o certificado de segurança. 
2.    Crie o Cofre chave Azure para armazenar este certificado. 
3.    Guarde os certificados para este cofre chave. 

Pode utilizar um novo ou um grupo de recursos Azure existente para este trabalho.  A abordagem anterior é usada na seguinte explicação.



[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="create-the-certificate"></a>Criar o certificado

Editar e executar o seguinte script Azure Powershell para criar o ficheiro de certificado (.pfx) numa pasta local.  Terá de substituir os valores pelos seguintes parâmetros:

|  **Parâmetro**        |   **Descrição**                                                               |
|  -------------        |   ---------------                                                               |
| `$certroopath` | Pasta local para salvar o ficheiro .pfx para  |
| `$location`    | Uma das localizações geográficas padrão do Azure  |
| `$vmName`      | Nome da máquina virtual Azure planeada   |
| `$certname`    | Nome do certificado; deve corresponder ao nome de domínio totalmente qualificado do VM planeado  |
| `$certpassword` | Palavra-passe para os certificados, deve corresponder à palavra-passe utilizada para o VM planeado  |
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
> Mantenha a mesma sessão de consola PowerShell ativa durante estes passos para que os valores dos vários parâmetros sejam mantidos.

> [!WARNING]
> Se guardar este script, guarde-o apenas num local seguro porque contém informações de segurança (uma palavra-passe).


## <a name="create-the-key-vault"></a>Criar o cofre chave

Copie o conteúdo do modelo de [implantação](./cpp-key-vault-deploy-template.md) do cofre chave para um ficheiro na sua máquina local. (no roteiro de exemplo abaixo, este recurso é `C:\certLocation\keyvault.json`.)  Editar e executar o seguinte script Azure Powershell para criar uma instância azure key vault e o grupo de recursos associados.  Terá de substituir os valores pelos seguintes parâmetros:

|  **Parâmetro**        |   **Descrição**                                                               |
|  -------------        |   ---------------                                                               |
| `$postfix`            | Cadeia numérica arbitrária anexada a identificadores de implantação                     |
| `$rgName`             | Nome do grupo de recursos Azure (RG) para criar                                        |
|  `$location`          | Uma das localizações geográficas padrão do Azure                                  |
| `$kvTemplateJson`     | Caminho do arquivo (keyvault.json) contendo modelo de Gestor de Recursos para cofre chave |
| `$kvname`             | Nome do novo cofre-chave                                                       |
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

Agora pode armazenar os certificados, contidos no ficheiro .pfx, para o novo cofre chave executando o seguinte script. 

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

Em seguida, [irá implementar um VM a partir da imagem VM do seu utilizador](./cpp-deploy-vm-user-image.md).
