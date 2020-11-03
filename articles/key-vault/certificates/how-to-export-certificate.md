---
title: Certificados de exportação do Azure Key Vault
description: Saiba como exportar certificados da Azure Key Vault.
services: key-vault
author: sebansal
tags: azure-key-vault
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.custom: mvc, devx-track-azurecli
ms.date: 08/11/2020
ms.author: sebansal
ms.openlocfilehash: e7ea3ef16b60e53450436bda66ce3dde091c81c2
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289561"
---
# <a name="export-certificates-from-azure-key-vault"></a>Certificados de exportação do Azure Key Vault

Saiba como exportar certificados da Azure Key Vault. Pode exportar certificados utilizando o Azure CLI, a Azure PowerShell ou o portal Azure. Também pode utilizar o portal Azure para exportar certificados do Azure App Service.

## <a name="about-azure-key-vault-certificates"></a>Acerca dos certificados do Azure Key Vault

O Azure Key Vault permite-lhe facilmente provisões, gestão e implementação de certificados digitais para a sua rede. Também permite comunicações seguras para aplicações. Consulte [os certificados Azure Key Vault](./about-certificates.md) para obter mais informações.

### <a name="composition-of-a-certificate"></a>Composição de um certificado

Quando um certificado Key Vault é criado, uma *chave* endereçada e *secreta* são criadas com o mesmo nome. A chave do cofre permite operações chave. O segredo do Cofre chave permite a recuperação do valor do certificado como segredo. Um certificado Key Vault também contém metadados de certificados x509 públicos. Aceda à [Composição de um certificado](./about-certificates.md#composition-of-a-certificate) para mais informações.

### <a name="exportable-and-non-exportable-keys"></a>Chaves exportáveis e não exportáveis

Depois de ser criado um certificado Key Vault, pode recuperá-lo do segredo endereçada com a chave privada. Recupere o certificado em formato PFX ou PEM.

- **Exportável** : A política utilizada para a criação do certificado indica que a chave é exportável.
- **Não exportável** : A política utilizada para a criação do certificado indica que a chave não é exportável. Neste caso, a chave privada não faz parte do valor quando é recuperada como um segredo.

Tipos-chave suportados: RSA, RSA-HSM, EC, EC-HSM, out (listado [aqui](/rest/api/keyvault/createcertificate/createcertificate#jsonwebkeytype)) Exportável só é permitido com RSA, CE. As chaves HSM não seriam exportáveis.

Consulte [os certificados Azure Key Vault](./about-certificates.md#exportable-or-non-exportable-key) para obter mais informações.

## <a name="export-stored-certificates"></a>Certificados armazenados de exportação

Pode exportar certificados armazenados no Cofre da Chave Azure utilizando o Azure CLI, a Azure PowerShell ou o portal Azure.

> [!NOTE]
> Só requer uma senha de certificado quando importa o certificado no cofre da chave. O Cofre-Chave não guarda a senha associada. Quando exporta o certificado, a senha está em branco.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Utilize o seguinte comando no CLI Azure para descarregar a **parte pública** de um certificado Key Vault.

```azurecli
az keyvault certificate download --file
                                 [--encoding {DER, PEM}]
                                 [--id]
                                 [--name]
                                 [--subscription]
                                 [--vault-name]
                                 [--version]
```

Veja [exemplos e definições de parâmetros](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-download) para obter mais informações.

Descarregar como certificado significa obter a parte pública. Se você quiser tanto a chave privada como os metadados públicos, então você pode descarregá-lo como secreto.

```azurecli
az keyvault secret download -–file {nameofcert.pfx}
                            [--encoding {ascii, base64, hex, utf-16be, utf-16le, utf-8}]
                            [--id]
                            [--name]
                            [--subscription]
                            [--vault-name]
                            [--version]
```

Para obter mais informações, consulte [as definições de parâmetros.](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-download)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Utilize este comando em Azure PowerShell para obter o certificado denominado **TestCert01** do cofre-chave chamado **ContosoKV01**. Para descarregar o certificado como um ficheiro PFX, corra a seguir ao comando. Estes comandos acedem **ao SecretId** e, em seguida, guardam o conteúdo como um ficheiro PFX.

```azurepowershell
$cert = Get-AzKeyVaultCertificate -VaultName "ContosoKV01" -Name "TestCert01"
$secret = Get-AzKeyVaultSecret -VaultName $vaultName -Name $cert.Name
$secretValueText = '';
$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue)
try {
    $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr)
} finally {
    [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr)
}
$secretByte = [Convert]::FromBase64String($secretValueText)
$x509Cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2
$x509Cert.Import($secretByte, "", "Exportable,PersistKeySet")
$type = [System.Security.Cryptography.X509Certificates.X509ContentType]::Pfx
$pfxFileByte = $x509Cert.Export($type, $password)

# Write to a file
[System.IO.File]::WriteAllBytes("KeyVault.pfx", $pfxFileByte)
```

Este comando exporta toda a cadeia de certificados com chave privada. O certificado está protegido por senha.
Para obter mais informações sobre o comando e parâmetros **Get-AzKeyVaultCertificate,** consulte [Get-AzKeyVaultCertificate - Exemplo 2](/powershell/module/az.keyvault/Get-AzKeyVaultCertificate?view=azps-4.4.0).

# <a name="portal"></a>[Portal](#tab/azure-portal)

No portal Azure, depois de criar/importar um certificado na lâmina do **Certificado,** recebe a notificação de que o certificado foi criado com sucesso. Selecione o certificado e a versão atual para ver a opção de download.

Para descarregar o certificado, selecione **Baixar em formato CER** ou descarregar em formato **PFX/PEM**.

![Download de certificado](../media/certificates/quick-create-portal/current-version-shown.png)

**Certificados de Serviço de Aplicações Azure Exportação**

Os certificados do Azure App Service são uma forma conveniente de comprar certificados SSL. Pode atribuí-las a Azure Apps a partir do portal. Também pode exportar estes certificados do portal como ficheiros PFX para serem usados em outros lugares. Depois de os importar, os certificados do Serviço de Aplicações estão localizados sob **segredos.**

Para mais informações, consulte os passos para [exportar certificados do Azure App Service](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx).

---

## <a name="read-more"></a>Saiba mais
* [Vários tipos e definições de arquivos de certificados](/archive/blogs/kaushal/various-ssltls-certificate-file-typesextensions)