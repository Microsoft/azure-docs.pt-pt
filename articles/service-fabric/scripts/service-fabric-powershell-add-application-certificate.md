---
title: Adicione cert de aplicação a um cluster em Powershell
description: Exemplo do Script do Azure PowerShell - Adicionar o certificado de aplicação a um cluster do Service Fabric.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 0f5a7d49200c7dec08f71f9cb0256630728e4711
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90090147"
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>Adicionar um certificado de aplicação a um cluster do Service Fabric

Este script de amostra sai como criar um certificado no Key Vault e, em seguida, implantá-lo para uma das escalas de máquinas virtuais que o seu cluster funciona. Este cenário não utiliza o Service Fabric diretamente, mas depende do Cofre de Chaves e dos conjuntos de escala de máquinas virtuais.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Se necessário, instale o Azure PowerShell utilizando as instruções encontradas no [guia Azure PowerShell](/powershell/azure/) e, em seguida, corra `Connect-AzAccount` para criar uma ligação com a Azure. 

## <a name="create-a-certificate-in-key-vault"></a>Criar um certificado em Key Vault

```powershell
$VaultName = ""
$CertName = ""
$SubjectName = "CN="

$policy = New-AzKeyVaultCertificatePolicy -SubjectName $SubjectName -IssuerName Self -ValidityInMonths 12
Add-AzKeyVaultCertificate -VaultName $VaultName -Name $CertName -CertificatePolicy $policy
```

## <a name="or-upload-an-existing-certificate-into-key-vault"></a>Ou enviar um certificado existente para o Cofre de Chaves

```powershell
$VaultName= ""
$CertName= ""
$CertPassword= ""
$PathToPFX= ""

$bytes = [System.IO.File]::ReadAllBytes($PathToPFX)
$base64 = [System.Convert]::ToBase64String($bytes)
$jsonBlob = @{
   data = $base64
   dataType = 'pfx'
   password = $CertPassword
   } | ConvertTo-Json
$contentbytes = [System.Text.Encoding]::UTF8.GetBytes($jsonBlob)
$content = [System.Convert]::ToBase64String($contentbytes)

$SecretValue = ConvertTo-SecureString -String $content -AsPlainText -Force

# Upload the certificate to the key vault as a secret
$Secret = Set-AzKeyVaultSecret -VaultName $VaultName -Name $CertName -SecretValue $SecretValue

```

## <a name="update-virtual-machine-scale-sets-profile-with-certificate"></a>Atualizar escala de máquina virtual define perfil com certificado

```powershell
$ResourceGroupName = ""
$VMSSName = ""
$CertStore = "My" # Update this with the store you want your certificate placed in, this is LocalMachine\My

# If you have added your certificate to the keyvault certificates, use
$CertConfig = New-AzVmssVaultCertificateConfig -CertificateUrl (Get-AzKeyVaultCertificate -VaultName $VaultName -Name $CertName).SecretId -CertificateStore $CertStore

# Otherwise, if you have added your certificate to the keyvault secrets, use
$CertConfig = New-AzVmssVaultCertificateConfig -CertificateUrl (Get-AzKeyVaultSecret -VaultName $VaultName -Name $CertName).Id -CertificateStore $CertStore

$VMSS = Get-AzVmss -ResourceGroupName $ResourceGroupName -VMScaleSetName $VMSSName

# If this KeyVault is already known by the virtual machine scale set, for example if the cluster certificate is deployed from this keyvault, use
$VMSS.virtualmachineprofile.osProfile.secrets[0].vaultCertificates.Add($CertConfig)

# Otherwise use
$VMSS = Add-AzVmssSecret -VirtualMachineScaleSet $VMSS -SourceVaultId (Get-AzKeyVault -VaultName $VaultName).ResourceId  -VaultCertificate $CertConfig
```

## <a name="update-the-virtual-machine-scale-set"></a>Atualize o conjunto de escala de máquina virtual
```powershell
Update-AzVmss -ResourceGroupName $ResourceGroupName -VirtualMachineScaleSet $VMSS -VMScaleSetName $VMSSName
```

> Se quiser o certificado colocado em vários tipos de nó no seu cluster, a segunda e terceira partes deste script devem ser repetidas para cada tipo de nó que deve ter o certificado.

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos: cada comando na tabela está ligado a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Política new-AzKeyVaultCertificate](/powershell/module/az.keyvault/New-AzKeyVaultCertificatePolicy) | Cria uma política de memória que representa o certificado |
| [Add-AzKeyVaultCertificate](/powershell/module/az.keyvault/Add-AzKeyVaultCertificate)| Implementa a política para certificados de cofre chave |
| [Set-AzKeyVaultSecret](/powershell/module/az.keyvault/Set-AzKeyVaultSecret)| Implementa a política para key vault secrets |
| [New-AzVmssVaultCertificateConfig](/powershell/module/az.compute/New-AzVmssVaultCertificateConfig) | Cria um config na memória representando o certificado num VM |
| [Get-AzVmss](/powershell/module/az.compute/Get-AzVmss) |  |
| [Add-AzVmssSecret](/powershell/module/az.compute/Add-AzVmssSecret) | Adiciona o certificado à definição na memória do conjunto de escala de máquina virtual |
| [Atualização-AzVmss](/powershell/module/az.compute/Update-AzVmss) | Implementa a nova definição do conjunto de escala de máquina virtual |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/).

Pode ver os exemplos do Azure Powershell adicionais para o Azure Service Fabric em [Exemplos do Azure PowerShell](../service-fabric-powershell-samples.md).
