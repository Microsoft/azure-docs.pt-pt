---
title: Rollover de um certificado de cluster do Azure Service Fabric | Documentos da Microsoft
description: Saiba como efetuar um rollover um certificado de cluster do Service Fabric identificado pelo nome comum do certificado.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: aljo
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: aljo
ms.openlocfilehash: e60eb33403b9c38972087706b9e62bc3578b97bb
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663881"
---
# <a name="manually-roll-over-a-service-fabric-cluster-certificate"></a>Implementar manualmente ao longo de um certificado de cluster do Service Fabric
Quando um certificado de cluster do Service Fabric se encontra prestes a expirar, terá de atualizar o certificado.  Rollover de certificado é simples se o cluster foi [configurado para utilizar certificados com base no nome comum](service-fabric-cluster-change-cert-thumbprint-to-cn.md) (em vez de thumbprint).  Obtenha um novo certificado a partir de uma autoridade de certificação com uma nova data de expiração.  Certificados autoassinados não são suportadas para os clusters do Service Fabric de produção, para incluir certificados gerados durante o Azure portal Cluster criação fluxo de trabalho. O novo certificado tem de ter o mesmo nome comum do certificado mais antigo. 

Cluster do Service Fabric utilizará automaticamente o certificado declarado com mais uma para a data de expiração futura; Quando mais do que um validar o certificado é instalado no anfitrião. Uma prática recomendada é usar um modelo do Resource Manager para Aprovisionar recursos do Azure. Para o ambiente de não produção, o script seguinte pode ser usado para carregar um novo certificado para um cofre de chaves e, em seguida, instala o certificado no conjunto de dimensionamento de máquina virtual: 

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  <subscription ID>

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "keyvaultgroup"
$VaultName = "cntestvault2"
$certFilename = "C:\users\sfuser\sftutorialcluster20180419110824.pfx"
$certname = "cntestcert"
$Password  = "!P@ssw0rd321"
$VmssResourceGroupName     = "sfclustertutorialgroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzureRmResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Get the key vault.  The key vault must be enabled for deployment.
$keyVault = Get-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName 
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

$certConfig = New-AzureRmVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzureRmVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzureRmVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzureRmVmss -ResourceGroupName $VmssResourceGroupName -Name $VmssName -VirtualMachineScaleSet $vmss  -Verbose
```

>[!NOTE]
> Calcula a segredos de conjunto de dimensionamento de Máquina Virtual não suporta o mesmo id de recurso para dois segredos separados, como cada segredo é um recurso exclusivo com a versão. 

Para saber mais, leia o seguinte:
* Saiba mais sobre [segurança de cluster](service-fabric-cluster-security.md).
* [Atualizar e gerir certificados de cluster](service-fabric-cluster-security-update-certs-azure.md)

