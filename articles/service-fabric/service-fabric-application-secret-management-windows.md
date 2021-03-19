---
title: Configurar um certificado de encriptação em clusters Windows
description: Saiba como configurar um certificado de encriptação e encriptar segredos em clusters Windows.
ms.topic: conceptual
ms.date: 01/04/2019
ms.openlocfilehash: eb4909d62a2627c368f24dab572b25c6f1df30ec
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "82583284"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-windows-clusters"></a>Crie um certificado de encriptação e criptografe segredos em clusters Windows
Este artigo mostra como configurar um certificado de encriptação e usá-lo para encriptar segredos em clusters Windows. Para os clusters Linux, consulte [configurar um certificado de encriptação e encriptar segredos em clusters Linux.][secret-management-linux-specific-link]

[O Azure Key Vault][key-vault-get-started] é usado aqui como um local de armazenamento seguro para certificados e como uma forma de obter certificados instalados em clusters de tecido de serviço em Azure. Se não estiver a ser implantado para o Azure, não precisa de utilizar o Key Vault para gerir segredos em aplicações de Tecido de Serviço. No entanto, *usar* segredos numa aplicação é agnóstico na plataforma para permitir que as aplicações sejam implementadas num cluster hospedado em qualquer lugar. 

## <a name="obtain-a-data-encipherment-certificate"></a>Obter um certificado de cifra de dados
Um certificado de cifra de dados é utilizado estritamente para encriptação e desencriptação de [parâmetros][parameters-link] nas [variáveis][environment-variables-link] de Settings.xml e ambiente de um serviço na ServiceManifest.xml de um serviço. Não é utilizado para autenticação ou assinatura de texto cifrado. O certificado deve satisfazer os seguintes requisitos:

* O certificado deve conter uma chave privada.
* O certificado deve ser criado para troca de chaves, exportável para um ficheiro De troca de informações pessoais (.pfx).
* A utilização da chave do certificado deve incluir o Encipherment de Dados (10), e não deve incluir a Autenticação do Servidor ou a Autenticação do Cliente. 
  
  Por exemplo, ao criar um certificado auto-assinado utilizando o PowerShell, a `KeyUsage` bandeira deve ser definida `DataEncipherment` para:
  
  ```powershell
  New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Instale o certificado no seu cluster
Este certificado deve ser instalado em cada nó do aglomerado. Veja [como criar um cluster utilizando o Azure Resource Manager][service-fabric-cluster-creation-via-arm] para obter instruções de configuração. 

## <a name="encrypt-application-secrets"></a>Encriptar segredos de aplicação
O seguinte comando PowerShell é usado para encriptar um segredo. Este comando apenas encripta o valor; **não** assina o texto da cifra. Deve utilizar o mesmo certificado de cifra que está instalado no seu cluster para produzir texto cifrado para valores secretos:

```powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

A cadeia codificada base-64 resultante contém tanto o texto cifrado secreto como as informações sobre o certificado que foi usado para criptografá-lo.

## <a name="next-steps"></a>Passos seguintes
Saiba como [especificar segredos encriptados numa aplicação.][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[key-vault-get-started]:../key-vault/general/overview.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
