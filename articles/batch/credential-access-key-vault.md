---
title: Acesso seguro ao Key Vault com o Batch
description: Aprenda a aceder programáticamente às suas credenciais a partir do Key Vault utilizando o Azure Batch.
ms.topic: how-to
ms.date: 02/13/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6938d0fcd2357efcf03053b0c9b2bde3954270b7
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079443"
---
# <a name="securely-access-key-vault-with-batch"></a>Acesso seguro ao Key Vault com o Batch

Neste artigo, você vai aprender a configurar nós batch para aceder de forma segura às credenciais armazenadas no Cofre da Chave Azure. Não vale a pena colocar as credenciais de administração em Key Vault, depois credenciais de codificação para aceder ao Key Vault a partir de um script. A solução é usar um certificado que concede aos seus nós Batch acesso ao Key Vault. Com alguns passos, podemos implementar armazenamento de chaves seguras para Batch.

Para autenticar o Cofre da Chave Azure a partir de um nó de lote, você precisa:

- Uma credencial de Diretório Ativo Azure (Azure AD)
- Um certificado
- Uma conta de lote
- Uma piscina de lote com pelo menos um nó

## <a name="obtain-a-certificate"></a>Obter um certificado

Se ainda não tem um certificado, a maneira mais fácil de obter um é gerar um certificado auto-assinado usando a `makecert` ferramenta de linha de comando.

Normalmente, pode `makecert` encontrar-se neste caminho: `C:\Program Files (x86)\Windows Kits\10\bin\<arch>` . Abra um pedido de comando como administrador e navegue para `makecert` utilizar o seguinte exemplo.

```console
cd C:\Program Files (x86)\Windows Kits\10\bin\x64
```

Em seguida, utilize a `makecert` ferramenta para criar ficheiros de certificado auto-assinados chamados `batchcertificate.cer` e `batchcertificate.pvk` . O nome comum (CN) usado não é importante para esta aplicação, mas é útil torná-lo algo que lhe diga para que o certificado é usado.

```console
makecert -sv batchcertificate.pvk -n "cn=batch.cert.mydomain.org" batchcertificate.cer -b 09/23/2019 -e 09/23/2019 -r -pe -a sha256 -len 2048
```

O lote requer um `.pfx` ficheiro. Utilize a ferramenta [pvk2pfx](/windows-hardware/drivers/devtest/pvk2pfx) para converter os `.cer` ficheiros e `.pvk` ficheiros criados por `makecert` um único `.pfx` ficheiro.

```console
pvk2pfx -pvk batchcertificate.pvk -spc batchcertificate.cer -pfx batchcertificate.pfx -po
```

## <a name="create-a-service-principal"></a>Criar um principal de serviço

O acesso ao Key Vault é concedido a um **utilizador** ou a um **diretor de serviço.** Para aceder programáticamente ao Key Vault, utilize um principal de serviço com o certificado que criamos anteriormente.

Para obter mais informações sobre os principais serviços da Azure, consulte [os objetos principais de aplicação e serviço no Diretório Ativo Azure.](../active-directory/develop/app-objects-and-service-principals.md)

> [!NOTE]
> O diretor de serviço deve estar no mesmo inquilino da AD AZure que o Cofre-Chave.

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

Os URLs para a aplicação não são importantes, já que só os usamos para acesso ao Cofre de Chaves.

## <a name="grant-rights-to-key-vault"></a>Direitos de concessão para o Cofre chave

O diretor de serviço criado no passo anterior precisa de permissão para recuperar os segredos do Key Vault. A permissão pode ser concedida através do portal Azure ou com o comando PowerShell abaixo.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'BatchVault' -ServicePrincipalName '"https://batch.mydomain.com' -PermissionsToSecrets 'Get'
```

## <a name="assign-a-certificate-to-a-batch-account"></a>Atribuir um certificado a uma conta batch

Crie uma piscina de Lote, em seguida, vá ao separador de certificado na piscina e atribua o certificado que criou. O certificado está agora em todos os nós do Lote.

Em seguida, precisamos atribuir o certificado à conta batch. A atribuição do certificado à conta permite-nos atribuí-lo às piscinas e depois aos nós. A maneira mais fácil de o fazer é ir à sua conta Batch no portal, navegar para **Certificados,** e selecionar **Adicionar**. Faça o upload do `.pfx` ficheiro que gerámos no [Obter um certificado](#obtain-a-certificate) e forneça a senha. Uma vez concluído, o certificado é adicionado à lista e pode verificar a impressão digital.

Agora, quando criar uma piscina de Lote, pode navegar para **certificados** dentro da piscina e atribuir o certificado que criou para essa piscina. Quando o fizer, certifique-se de que seleciona **LocalMachine** para a localização da loja. O certificado é carregado em todos os nós do Lote na piscina.

## <a name="install-azure-powershell"></a>Instalar o Azure PowerShell

Se planeia aceder ao Key Vault utilizando scripts PowerShell nos seus nós, então precisa da biblioteca Azure PowerShell instalada. Existem algumas formas de o fazer, se os seus nós tiverem o Windows Management Framework (WMF) 5 instalado, então pode utilizar o comando do módulo de instalação para o descarregar. Se estiver a usar nós que não têm WMF 5, a forma mais fácil de o instalar é embalar o ficheiro Azure PowerShell com os `.msi` seus ficheiros Batch e, em seguida, chamar o instalador como a primeira parte do seu script de arranque Batch. Consulte este exemplo para mais detalhes:

```powershell
$psModuleCheck=Get-Module -ListAvailable -Name Azure -Refresh
if($psModuleCheck.count -eq 0) {
    $psInstallerPath = Join-Path $downloadPath "azure-powershell.3.4.0.msi" Start-Process msiexec.exe -ArgumentList /i, $psInstallerPath, /quiet -wait
}
```

## <a name="access-key-vault"></a>Aceder ao Key Vault

Agora estamos todos preparados para aceder ao Key Vault em scripts em execução nos nós do Batch. Para aceder ao Key Vault a partir de um script, tudo o que precisa é que o seu script autente contra a AD Azure usando o certificado. Para o fazer em PowerShell, utilize os seguintes comandos de exemplo. Especifique o GUID apropriado para **impressão digital thumbprint,** **ID da aplicação** (o ID do seu diretor de serviço) e **ID** do inquilino (o inquilino onde o seu diretor de serviço existe).

```powershell
Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint -ApplicationId
```

Uma vez autenticado, aceda ao KeyVault como normalmente faria.

```powershell
$adminPassword=Get-AzureKeyVaultSecret -VaultName BatchVault -Name batchAdminPass
```

Estas são as credenciais a usar no seu roteiro.
