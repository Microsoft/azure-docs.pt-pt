---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: mimart
ms.openlocfilehash: 3458a4f3ba337d4c470c21fa5d89b28f5f2701a3
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98916930"
---
Se ainda não tiver um certificado, pode usar um certificado auto-assinado para este tutorial. O certificado auto-assinado é um certificado de segurança que não é assinado por uma autoridade de certificados (CA). Não fornecem todas as garantias de segurança de um certificado assinado por uma autoridade de certificados. 

# <a name="windows"></a>[Windows](#tab/windows)

No Windows, utilize o cmdlet [New-SelfSignedCertificate da PowerShell](/powershell/module/pkiclient/new-selfsignedcertificate) para gerar um certificado.

1. Execute este comando PowerShell para gerar um certificado auto-assinado. Modifique o `-Subject` argumento conforme apropriado para a sua aplicação e nome de inquilino Azure AD B2C. Também pode ajustar a `-NotAfter` data para especificar uma validade diferente para o certificado.

    ```PowerShell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```

1. Abra **os certificados de utilizador** De  >  **gestão corrente**  >    >  **yourappname.yourtenant.onmicrosoft.com**  >  
1. Selecione o certificado > **Action**  >  **All Tasks**  >  **Export**
1. Selecione **Yes**  >  **Next**  >  **Yes, exporte a chave privada**  >  **Next**
1. Aceite os predefinições para **o formato de ficheiro de exportação**
1. Forneça uma senha para o certificado.

Para que o Azure AD B2C aceite a senha de ficheiro .pfx, a palavra-passe deve ser encriptada com a opção TripleDES-SHA1 no utilitário de exportação da Loja de Certificados do Windows, em oposição ao AES256-SHA256.

# <a name="macos"></a>[macOS](#tab/macos)

No macOS, utilize [o Certificate Assistant](https://support.apple.com/guide/keychain-access/aside/glosa3ed0609/11.0/mac/11.0) no Keychain Access para gerar um certificado.

1. Siga as instruções de como [criar certificados auto-assinados no Keychain Access on Mac](https://support.apple.com/guide/keychain-access/kyca8916/mac).
1. Na aplicação Keychain Access no seu Mac, selecione o certificado que criou.
1. Escolha o arquivo > itens de exportação.
1. Selecione um nome de ficheiro para guardar o seu certificado. Por exemplo, **auto-assinado-certificado.p12**. 
1. Para o **Formato de Ficheiros**, selecione **Troca de Informações Pessoais (.p12)**.
1. Selecione **Guardar**.
1. Introduza uma **palavra-passe** e, em seguida, **verifique** a palavra-passe.
1. Substitua a extensão do ficheiro para `.pfx` . Por exemplo, **auto-assinado-certificate.pfx**. 

---
