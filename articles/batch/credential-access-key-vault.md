---
title: Acesso seguro a cofre chave com lote - Lote Azure
description: Aprenda a aceder programáticamente às suas credenciais a partir do Key Vault utilizando o Lote Azure.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.workload: big-compute
ms.topic: article
ms.date: 02/13/2020
ms.author: lahugh
ms.openlocfilehash: 14cbacf43e83dc768e9a85620df131533b746671
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77463105"
---
# <a name="securely-access-key-vault-with-batch"></a>Acesso seguro cofre chave com lote

Neste artigo, você aprenderá a configurar nós de Lote para aceder de forma segura às credenciais armazenadas no Cofre chave Azure. Não vale a pena colocar as suas credenciais de administração no Cofre chave, e depois credenciais de codificação rígida para aceder ao Cofre chave a partir de um guião. A solução é usar um certificado que conceda aos seus nódes de Lote acesso ao Cofre chave. Com alguns passos, podemos implementar armazenamento de chaves seguras para o Lote.

Para autenticar o Cofre chave Azure a partir de um nó de lote, você precisa:

- Uma credencial de diretório ativo Azure (Azure AD)
- Um certificado
- Uma conta de Lote
- Uma piscina de lote com pelo menos um nó

## <a name="obtain-a-certificate"></a>Obter um certificado

Se ainda não tem um certificado, a maneira mais fácil de obter um é gerar um certificado auto-assinado usando a ferramenta `makecert` linha de comando.

Normalmente, pode encontrar `makecert` neste caminho: `C:\Program Files (x86)\Windows Kits\10\bin\<arch>`. Abra um pedido de comando como administrador e navegue para `makecert` usando o seguinte exemplo.

```console
cd C:\Program Files (x86)\Windows Kits\10\bin\x64
```

Em seguida, utilize a ferramenta `makecert` para criar ficheiros de certificados auto-assinados chamados `batchcertificate.cer` e `batchcertificate.pvk`. O nome comum (CN) usado não é importante para esta aplicação, mas é útil torná-lo algo que lhe diga para que o certificado é usado.

```console
makecert -sv batchcertificate.pvk -n "cn=batch.cert.mydomain.org batchcertificate.cer -b 09/23/2019 -e 09/23/2019 -r -pe -a sha256 -len 2048
```

O lote requer um ficheiro `.pfx`. Utilize a ferramenta [pvk2pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx) para converter os ficheiros `.cer` e `.pvk` criados por `makecert` para um único ficheiro `.pfx`.

```console
pvk2pfx -pvk batchcertificate.pvk -spc batchcertificate.cer -pfx batchcertificate.pfx -po
```

## <a name="create-a-service-principal"></a>Criar um principal de serviço

O acesso ao Key Vault é concedido a um **utilizador** ou a um diretor de **serviço**. Para aceder programaticamente ao Key Vault, utilize um diretor de serviço com o certificado que criámos anteriores.

Para obter mais informações sobre os principais de serviço do Azure, consulte [os objetos principais de aplicação e serviço no Diretório Ativo do Azure](../active-directory/develop/app-objects-and-service-principals.md).

> [!NOTE]
> O diretor de serviço deve estar no mesmo inquilino da AD Azure que o Cofre chave.

```powershell
$now = [System.DateTime]::Parse("2020-02-10")
# Set this to the expiration date of the certificate
$expirationDate = [System.DateTime]::Parse("2021-02-10")
# Point the script at the cer file you created $cerCertificateFilePath = 'c:\temp\batchcertificate.cer'
$cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$cer.Import($cerCertificateFilePath)
# Load the certificate into memory
$credValue = [System.Convert]::ToBase64String($cer.GetRawCertData())
# Create a new AAD application that uses this certificate
$newADApplication = New-AzureRmADApplication -DisplayName "Batch Key Vault Access" -HomePage "https://batch.mydomain.com" -IdentifierUris "https://batch.mydomain.com" -certValue $credValue -StartDate $now -EndDate $expirationDate
# Create new AAD service principal that uses this application
$newAzureAdPrincipal = New-AzureRmADServicePrincipal -ApplicationId $newADApplication.ApplicationId
```

Os URLs para a aplicação não são importantes, uma vez que só os estamos a usar para acesso ao Cofre chave.

## <a name="grant-rights-to-key-vault"></a>Direitos de concessão de cofre chave

O diretor de serviço criado no passo anterior precisa de permissão para recuperar os segredos do Cofre Chave. A permissão pode ser concedida através do portal Azure ou com o comando PowerShell abaixo.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'BatchVault' -ServicePrincipalName '"https://batch.mydomain.com' -PermissionsToSecrets 'Get'
```

## <a name="assign-a-certificate-to-a-batch-account"></a>Atribuir um certificado a uma conta de Lote

Crie uma piscina de Lote, depois vá ao separador de certificado na piscina e atribua o certificado que criou. O certificado está agora em todos os nós do Lote.

Em seguida, precisamos atribuir o certificado à conta do Lote. Atribuir o certificado à conta permite-nos atribuí-lo às piscinas e, em seguida, aos nós. A maneira mais fácil de fazer isso é ir à sua conta Batch no portal, navegar para **Certificados,** e selecionar **Adicionar**. Faça upload do ficheiro `.pfx` que geramos no [Certificado Obter um certificado](#obtain-a-certificate) e forneça a palavra-passe. Uma vez concluído, o certificado é adicionado à lista e pode verificar a impressão digital.

Agora, quando você criar uma piscina de lote, você pode navegar para **Certificados** dentro da piscina e atribuir o certificado que você criou para essa piscina. Quando o fizer, certifique-se de selecionar **a LocalMachine** para a localização da loja. O certificado está carregado em todos os nós do Lote na piscina.

## <a name="install-azure-powershell"></a>Instalar o Azure PowerShell

Se planeia aceder ao Key Vault utilizando scripts PowerShell nos seus nós, então precisa da biblioteca Azure PowerShell instalada. Existem algumas formas de o fazer, se os seus nós tiverem o Windows Management Framework (WMF) 5 instalado, então pode utilizar o comando do módulo de instalação para o descarregar. Se estiver a usar nós que não tenham WMF 5, a forma mais fácil de instalá-lo é agregar o ficheiro `.msi` Azure PowerShell com os seus ficheiros Batch e, em seguida, ligar para o instalador como a primeira parte do seu script de arranque do Batch. Consulte este exemplo para mais detalhes:

```powershell
$psModuleCheck=Get-Module -ListAvailable -Name Azure -Refresh
if($psModuleCheck.count -eq 0) {
    $psInstallerPath = Join-Path $downloadPath "azure-powershell.3.4.0.msi" Start-Process msiexec.exe -ArgumentList /i, $psInstallerPath, /quiet -wait
}
```

## <a name="access-key-vault"></a>Aceder ao Key Vault

Agora estamos todos preparados para aceder ao Key Vault em scripts em execução em nós de Batch. Para aceder ao Key Vault a partir de um script, tudo o que precisa é que o seu script autentique contra o Azure AD usando o certificado. Para o fazer no PowerShell, utilize os seguintes comandos de exemplo. Especifique o GUID apropriado para **impressão digital,** id de **aplicação** (o ID do seu principal de serviço) e ID do **inquilino** (o inquilino onde o seu diretor de serviço existe).

```powershell
Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint -ApplicationId
```

Uma vez autenticado, aceda ao KeyVault como normalmente faria.

```powershell
$adminPassword=Get-AzureKeyVaultSecret -VaultName BatchVault -Name batchAdminPass
```

Estas são as credenciais para usar no seu guião.
