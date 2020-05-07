---
title: Configurar um certificado de encriptação em clusters do Windows
description: Saiba como configurar um certificado de encriptação e encriptar segredos em clusters do Windows.
ms.topic: conceptual
ms.date: 01/04/2019
ms.openlocfilehash: eb4909d62a2627c368f24dab572b25c6f1df30ec
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82583284"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-windows-clusters"></a>Configurar um certificado de encriptação e encriptar segredos em clusters do Windows
Este artigo mostra como configurar um certificado de encriptação e usá-lo para encriptar segredos em clusters do Windows. Para os clusters Linux, consulte Configurar um certificado de [encriptação e encriptar segredos em clusters Linux.][secret-management-linux-specific-link]

[O Azure Key Vault][key-vault-get-started] é usado aqui como um local de armazenamento seguro para certificados e como forma de obter certificados instalados em clusters de Tecido de Serviço em Azure. Se não estiver a ser implantado no Azure, não precisa de usar o Key Vault para gerir segredos em aplicações de Tecido de Serviço. No entanto, *o uso* de segredos numa aplicação é cloud platform-agnóstico para permitir que as aplicações sejam implementadas para um cluster hospedado em qualquer lugar. 

## <a name="obtain-a-data-encipherment-certificate"></a>Obter um certificado de obtenção de dados
Um certificado de codificação de dados é usado estritamente para encriptação e desencriptação de [parâmetros][parameters-link] nas Definições de um serviço.xml e [variáveis ambientais][environment-variables-link] em serviceManifest.xml de um serviço. Não é utilizado para autenticação ou assinatura de texto cifrado. O certificado deve satisfazer os seguintes requisitos:

* O certificado deve conter uma chave privada.
* O certificado deve ser criado para troca de chaves, exportável para um ficheiro de Intercâmbio de Informações Pessoais (.pfx).
* A utilização da chave do certificado deve incluir o Encipherment de Dados (10), e não deve incluir a autenticação do servidor ou a autenticação do cliente. 
  
  Por exemplo, ao criar um certificado auto-assinado utilizando o PowerShell, a `KeyUsage` bandeira deve ser fixada para: `DataEncipherment`
  
  ```powershell
  New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Instale o certificado no seu cluster
Este certificado deve ser instalado em cada nó do cluster. Veja como criar um cluster utilizando o Gestor de [Recursos Azure][service-fabric-cluster-creation-via-arm] para instruções de configuração. 

## <a name="encrypt-application-secrets"></a>Criptografe segredos de aplicação
O seguinte comando PowerShell é usado para encriptar um segredo. Este comando apenas encripta o valor; **não** assina o texto da cifra. Deve utilizar o mesmo certificado de encipherment instalado no seu cluster para produzir texto de cifra para valores secretos:

```powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

A cadeia codificada base-64 resultante contém tanto o texto cifrado secreto como informações sobre o certificado que foi usado para encriptar.

## <a name="next-steps"></a>Passos seguintes
Saiba especificar [segredos encriptados numa aplicação.][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[key-vault-get-started]:../key-vault/general/overview.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
