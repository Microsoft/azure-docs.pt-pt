---
title: Sobreposição de um certificado de Cluster Service Fabric do Azure | Microsoft Docs
description: Saiba como sobrepor um certificado de Cluster Service Fabric identificado pelo nome comum do certificado.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: atsenthi
ms.openlocfilehash: 5d11054ca8eb684f1f25a25ddeac1b53e82b3775
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599912"
---
# <a name="manually-roll-over-a-service-fabric-cluster-certificate"></a>Reverter manualmente um Service Fabric certificado de cluster
Quando um certificado de Cluster Service Fabric está perto de expirar, você precisa atualizar o certificado.  A substituição de certificado será simples se o cluster tiver sido [configurado para usar certificados com base no nome comum](service-fabric-cluster-change-cert-thumbprint-to-cn.md) (em vez de impressão digital).  Obtenha um novo certificado de uma autoridade de certificação com uma nova data de validade.  Os certificados autoassinados não têm suporte para clusters de Service Fabric de produção, para incluir certificados gerados durante o fluxo de trabalho de criação de cluster portal do Azure. O novo certificado deve ter o mesmo nome comum que o certificado mais antigo. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Service Fabric cluster usará automaticamente o certificado declarado com um adicional na data de validade futura; Quando mais de um certificado de validação estiver instalado no host. Uma prática recomendada é usar um modelo do Resource Manager para provisionar recursos do Azure. Para o ambiente de não produção, o script a seguir pode ser usado para carregar um novo certificado em um cofre de chaves e, em seguida, instalar o certificado no conjunto de dimensionamento de máquinas virtuais: 

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  <subscription ID>

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "keyvaultgroup"
$VaultName = "cntestvault2"
$certFilename = "C:\users\sfuser\sftutorialcluster20180419110824.pfx"
$certname = "cntestcert"
$Password  = "!P@ssw0rd321"
$VmssResourceGroupName     = "sfclustertutorialgroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Get the key vault.  The key vault must be enabled for deployment.
$keyVault = Get-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName 
$resourceId = $keyVault.ResourceId  

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    

Set-StrictMode -Version 3
$ErrorActionPreference = "Stop"

$certConfig = New-AzVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzVmss -ResourceGroupName $VmssResourceGroupName -Name $VmssName -VirtualMachineScaleSet $vmss  -Verbose
```

>[!NOTE]
> Computa os segredos do conjunto de dimensionamento de máquinas virtuais não dão suporte à mesma ID de recurso para dois segredos separados, pois cada segredo é um recurso exclusivo com controle de versão. 

Para saber mais, leia o seguinte:
* Saiba mais sobre a [segurança do cluster](service-fabric-cluster-security.md).
* [Atualizar e gerenciar certificados de cluster](service-fabric-cluster-security-update-certs-azure.md)

