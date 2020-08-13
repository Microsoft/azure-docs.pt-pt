---
title: Certificado de exportação do Cofre da Chave Azure
description: Certificado de exportação do Cofre da Chave Azure
services: key-vault
author: sebansal
tags: azure-key-vault
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc, devx-track-azurecli
ms.date: 08/11/2020
ms.author: sebansal
ms.openlocfilehash: 9b7216c36e5dd1ab5e4f65c565bf43cbd17bfbee
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88173025"
---
# <a name="export-certificate-from-azure-key-vault"></a>Certificado de exportação do Cofre da Chave Azure

O Azure Key Vault permite-lhe facilmente provisões, gestão e implementação de certificados digitais para a sua rede e para permitir comunicações seguras para aplicações. Para obter informações mais gerais sobre certificados, consulte [certificados de cofre chave Azure](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates)

## <a name="about-azure-key-vault-certificate"></a>Sobre o certificado de cofre chave Azure

### <a name="composition-of-certificate"></a>Composição do certificado
Quando um certificado Key Vault é criado, uma chave e segredo endereçada também são criados com o mesmo nome. A chave Key Vault permite operações chave e o segredo do Cofre chave permite a recuperação do valor do certificado como segredo. Um certificado Key Vault também contém metadados de certificados x509 públicos. [Saiba mais](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#composition-of-a-certificate)

### <a name="exportable-or-non-exportable-keys"></a>Chaves exportáveis ou não exportáveis
Quando um certificado Key Vault é criado, pode ser recuperado do segredo endereçado com a chave privada em formato PFX ou PEM. A política utilizada para a criação do certificado deve indicar que a chave é exportável. Se a apólice indica não ser exportável, então a chave privada não é uma parte do valor quando recuperada como um segredo.

Dois tipos de chave são suportados - RSA ou RSA HSM com certificados. Exportável só é permitido com RSA, não suportado pela RSA HSM. [Saiba mais](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#exportable-or-non-exportable-key)

O certificado armazenado no Cofre da Chave Azure pode ser exportado através do Azure CLI, PowerShell ou Portal.

> [!NOTE]
> É importante notar que só necessitaria de uma palavra-passe de um certificado quando o importasse no cofre-chave. O Key Vault não salvaria a senha associada, portanto, quando exportaria o certificado, a palavra-passe estaria em branco.

## <a name="exporting-certificate-using-cli"></a>Certificado de exportação utilizando OC
O seguinte comando permitir-lhe-á descarregar a **parte pública** de um certificado Key Vault.

```azurecli
az keyvault certificate download --file
                                 [--encoding {DER, PEM}]
                                 [--id]
                                 [--name]
                                 [--subscription]
                                 [--vault-name]
                                 [--version]
```
Para exemplos e definições de parâmetros, [ver aqui](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-download)



Se pretender descarregar todo o certificado, ou seja, **por parte pública e privada da sua composição,** então pode ser realizado baixando o certificado como segredo.

```azurecli
az keyvault secret download –file {nameofcert.pfx}
                            [--encoding {ascii, base64, hex, utf-16be, utf-16le, utf-8}]
                            [--id]
                            [--name]
                            [--subscription]
                            [--vault-name]
                            [--version]
```
Para definições de parâmetros, [consulte aqui](https://docs.microsoft.com/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-download)


## <a name="exporting-certificate-using-powershell"></a>Certificado de exportação utilizando PowerShell

Este comando obtém o certificado chamado TestCert01 do cofre chamado ContosoKV01. Para descarregar o certificado como ficheiro pfx, corra a seguir ao comando. Estes comandos acedem ao SecretId e, em seguida, guardam o conteúdo como um ficheiro pfx.

```azurepowershell
$cert = Get-AzKeyVaultCertificate -VaultName "ContosoKV01" -Name "TestCert01"
$kvSecret = Get-AzKeyVaultSecret -VaultName "ContosoKV01" -Name $Cert.Name
$kvSecretBytes = [System.Convert]::FromBase64String($kvSecret.SecretValueText)
$certCollection = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2Collection
$certCollection.Import($kvSecretBytes,$null,[System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable)
$password = '******'
$protectedCertificateBytes = $certCollection.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $password)
$pfxPath = [Environment]::GetFolderPath("Desktop") + "\MyCert.pfx"
[System.IO.File]::WriteAllBytes($pfxPath, $protectedCertificateBytes)
```
Isto exportará toda a cadeia de certificados com chave privada e este certificado será protegido por senha.
Para obter mais informações sobre ```Get-AzKeyVaultCertificate``` comando e parâmetros, consulte o [Exemplo 2](https://docs.microsoft.com/powershell/module/az.keyvault/Get-AzKeyVaultCertificate?view=azps-4.4.0)

## <a name="exporting-certificate-using-portal"></a>Certificado de exportação utilizando portal

No portal, quando criar/importar um certificado em lâmina de certificado, receberá a notificação de que o certificado foi criado com sucesso. Ao selecionar o certificado, pode clicar na versão atual e verá a opção de download.


Ao clicar no botão "Descarregar em formato CER" ou "Descarregar em formato PFX/PEM", pode descarregar o certificado.


![Download de certificado](../media/certificates/quick-create-portal/current-version-shown.png)


## <a name="exporting-app-service-certificate-from-key-vault"></a>Certificado de serviço de aplicativos de exportação do cofre chave

Os Certificados de Serviço de Aplicações Azure fornecem uma forma conveniente de comprar certificados SSL e atribuí-los a Azure Apps imediatamente a partir do portal. Estes certificados também podem ser exportados do portal como ficheiros PFX para serem usados em outros lugares.
Uma vez importados, os certificados de serviço de aplicações podem ser **localizados sob segredos.**

Para etapas para exportar certificados de serviço de aplicações, [leia aqui](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx)

## <a name="read-more"></a>Saiba mais
* [Vários tipos e definições de arquivos de certificados](https://docs.microsoft.com/archive/blogs/kaushal/various-ssltls-certificate-file-typesextensions)