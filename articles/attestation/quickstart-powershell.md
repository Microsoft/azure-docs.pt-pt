---
title: Configurar a Azure Attestation com a Azure PowerShell
description: Como configurar e configurar um fornecedor de atestado utilizando a Azure PowerShell.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 70e86e01a9d37a27620d451bcd5d035dfcb4573d
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89237393"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-powershell"></a>Quickstart: Configurar a Azure Attestation com a Azure PowerShell

Siga os passos abaixo para criar e configurar um provedor de atestado usando a Azure PowerShell. Consulte [a visão geral do Azure PowerShell](/powershell/azure/?view=azps-2.8.0&viewFallbackFrom=azps-2.4.0) para obter informações sobre como instalar e executar a Azure PowerShell.

Por favor, note que a PowerShell Gallery depreendeu as versões 1.0 e 1.1 de Segurança da Camada de Transporte (TLS). Recomenda-se tLS 1.2 ou uma versão posterior. Assim, pode receber os seguintes erros:

- AVISO: Incapaz de resolver a fonte do pacote https://www.powershellgallery.com/api/v2 '
- PackageManagement\Install-Package: Não foi encontrada qualquer correspondência para os critérios de pesquisa especificados e nome do módulo 

Para continuar a interagir com a Galeria PowerShell, executar o seguinte comando antes dos comandos do Módulo de Instalação

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
```

## <a name="install-azattestation-powershell-module"></a>Instalar módulo Az.Attestation PowerShell

Na máquina com Azure PowerShell, instale o módulo Az.Attestation PowerShell, que contém cmdlets para a Azure Attestation.  

### <a name="initial-installation"></a>Instalação inicial

Termine todas as janelas PowerShell existentes.

Para instalar para "utilizador atual", lance uma janela PowerShell não elevada e corra:

```powershell
Install-Module -Name Az.Attestation -AllowClobber -Scope CurrentUser
```

Para instalar para "todos os utilizadores", lance uma janela PowerShell elevada e corra:

```powershell
Install-Module -Name Az.Attestation -AllowClobber -Scope AllUsers
```

Feche a elevada consola PowerShell.

### <a name="update-the-installation"></a>Atualizar a instalação

Termine todas as janelas PowerShell existentes.

Para atualizar para o "utilizador atual", lance uma janela PowerShell não elevada e corra:

```powershell
Update-Module -Name Az.Attestation
```

Para atualizar para "todos os utilizadores", lance uma janela PowerShell elevada e corra:

```powershell
Update-Module -Name Az.Attestation
```

Feche a elevada consola PowerShell.

### <a name="get-installed-modules"></a>Obter os módulos instalados

Versão mínima dos módulos Az necessários para suportar operações de atestado:
- Az 4.5.0
- Az.Contas 1.9.2
- Az.Attestation 0.1.8

Executar o comando abaixo para verificar a versão instalada de todos os módulos Az 

```powershell
Get-InstalledModule
```
Se as versões não corresponderem ao requisito mínimo, executar comandos de Módulo de Atualização.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inscreva-se no Azure na consola PowerShell (sem privilégios de acesso elevados).

```powershell
Connect-AzAccount
```

Se necessário, mude para a subscrição a utilizar para a Azure Atesstation.

```powershell
Set-AzContext -Subscription <subscription id>  
```

## <a name="register-microsoftattestation-resource-provider"></a>Registar o fornecedor de recursos Microsoft.Attestation

Registe-se na subscrição do fornecedor de recursos Microsoft.Attestation. Para obter mais informações sobre os fornecedores de recursos Azure e como configurar e gerir os fornecedores de recursos, consulte [os fornecedores e tipos de recursos da Azure.](../azure-resource-manager/management/resource-providers-and-types.md) Note que o registo de um fornecedor de recursos é exigido apenas uma vez para uma subscrição.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Attestation
```
## <a name="regional-availability-of-azure-attestation"></a>Disponibilidade regional do Azure Attestation

```powershell
(Get-AzResourceProvider -ProviderNamespace Microsoft.Attestation)[0].Locations
```

## <a name="create-an-azure-resource-group"></a>Criar um grupo de recursos Azure

Criar um grupo de recursos para o fornecedor de atestado. Note que outros recursos Azure (incluindo uma máquina virtual com instância de aplicação do cliente) podem ser colocados no mesmo grupo de recursos.

```powershell
$location = "uksouth" 
$attestationResourceGroup = "<attestation provider resource group name>"
New-AzResourceGroup -Name $attestationResourceGroup -Location $location 
```

## <a name="create-and-manage-an-attestation-provider"></a>Criar e gerir um fornecedor de atestado

A Nova AzAttestation cria um fornecedor de atestado.

```powershell
$attestationProvider = "<attestation provider name>" 
New-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Location $location
```

PolicySignerCertateFile é um ficheiro que especifica um conjunto de chaves de assinatura fidedignas. Se for especificado um nome de ficheiro para o parâmetro PolicySignerCertificateFile, o provedor de atestado só pode ser configurado com políticas em formato JWT assinado. Outra política pode ser configurada em texto ou num formato JWT não assinado.

```powershell
New-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Location $location -PolicySignersCertificateFile "C:\test\policySignersCertificates.pem"
```

Para a amostra PolicySignersCertateFile, consulte [exemplos de certificado de sinalizador de política](policy-signer-examples.md).

A Get-AzAttestation recupera as propriedades do fornecedor de atestados como o status e o AttestURI. Tome nota de AttestURI, como será necessário mais tarde.

```azurepowershell
Get-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup  
```

O comando acima deve produzir uma saída como a seguinte: 

```
Id:/subscriptions/MySubscriptionID/resourceGroups/MyResourceGroup/providers/Microsoft.Attestation/attestationProviders/MyAttestationProvider
Location: MyLocation
ResourceGroupName: MyResourceGroup
Name: MyAttestationProvider
Status: Ready
TrustModel: AAD
AttestUri: https://MyAttestationProvider.us.attest.azure.net 
Tags: 
TagsTable: 
```

Os fornecedores de atestados podem ser eliminados usando o cmdlet remove-AzAttestation.  

''azurepowershell Remove-AzAttestation - Nome $attestationProvider -ResourceGroupName $attestationResourceGroup
```

## Policy management

In order to manage policies, an Azure AD user requires the following permissions for "Actions":
- Microsoft.Attestation/attestationProviders/attestation/read
- Microsoft.Attestation/attestationProviders/attestation/write
- Microsoft.Attestation/attestationProviders/attestation/delete

These permissions can be assigned to an AD user through a role such as "Owner" (wildcard permissions), "Contributor" (wildcard permissions) or "Attestation Contributor" (specific permissions for Azure Attestation only).  

In order to read policies, an Azure AD user requires the following permission for "Actions":
- Microsoft.Attestation/attestationProviders/attestation/read

This permission can be assigned to an AD user through a role such as "Reader" (wildcard permissions) or "Attestation Reader" (specific permissions for Azure Attestation only).

Below PowerShell cmdlets provide policy management for an attestation provider (one TEE at a time).

Get-AzAttestationPolicy returns the current policy for the specified TEE. The cmdlet displays policy in both text and JWT format of the policy.

```powershell
$teeType = "<tee Type>"
Get-AzAttestationPolicy   -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType 
```

Os tipos de TEE suportados são "sgxenclave" e "vbsenclave".

Set-AttestationPolicy define uma nova política para o TEE especificado. O cmdlet aceita a política em qualquer texto ou formato JWT e é controlado pelo parâmetro PolicyFormat. "Texto" é o valor predefinido para PolicyFormat. 

```powershell
$policyFormat = "<policy format>"
$policy=Get-Content -path "C:\test\policy.txt" -Raw
Set-AzAttestationPolicy   -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType -Policy $policy -PolicyFormat $policyFormat 
```

Se o PolicySignerCertificateFile for fornecido durante a criação de um provedor de atestado, as políticas só podem ser configuradas no formato JWT assinado. Outra política pode ser configurada em texto ou num formato JWT não assinado.

A política de atestado no formato JWT deve conter uma reivindicação denominada "AttestationPolicy". Para a política assinada, o JWT deve ser assinado com chave privada correspondente a qualquer um dos certificados de sinalização de política existentes.

Para amostras de políticas, consulte [exemplos de uma política de atestado](policy-examples.md).

Reset-AzAttestationPolicy reinicia a política por defeito para o TEE especificado.

```powershell
Reset-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType 
```

## <a name="policy-signer-certificates-management"></a>Gestão de certificados de signatários de apólices

Abaixo os cmdlets powerShell fornecem a gestão de certificados de signatário de política para um fornecedor de atestado:

```powershell
Get-AzAttestationPolicySigners -Name $attestationProvider -ResourceGroupName $attestationResourceGroup

Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Signer <signer>

Remove-AzAttestationPolicySigner -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Signer <signer>
```

O certificado de signatário da apólice é um JWT assinado com a pretensão denominada "maa-policyCertificate". O valor da reclamação é um JWK que contém a chave de assinatura fidedigna a adicionar. O JWT deve ser assinado com chave privada correspondente a qualquer um dos certificados de sinalização de política existentes.

Note que toda a manipulação semântica do certificado de sinalizador de política deve ser feita fora da PowerShell. No que diz respeito ao PowerShell, é uma simples corda.

Para obter a amostra do certificado do signatário da apólice, consulte [exemplos de certificado de sinalizador de política](policy-signer-examples.md).

Para obter mais informações sobre os cmdlets e seus parâmetros, consulte [os cmdlets Azure Attestation PowerShell](/powershell/module/az.attestation/?view=azps-4.3.0#attestation) 

## <a name="next-steps"></a>Passos seguintes

- [Como autor e assinar uma política de atestado](author-sign-policy.md)
- [Atestar um enclave SGX usando amostras de código](https://docs.microsoft.com/samples/browse/?expanded=azure&terms=attestation)
